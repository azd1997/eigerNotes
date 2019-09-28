---
title: "实现一个简单的HTTP服务器"
date: 2019-09-28T03:16:29+08:00
draft: false
categories: ["cs"]
tags: ["HTTP", "TCP", "Python"]
keywords: ["HTTP", "TCP", "面向字节流"]
---

## 1. 构建TCP服务器

### 1.1. 包装TCPServer类

```python
# simpleHttpServer/server/socket_server.py

import socket

class TCPServer:

    # TODO：handle_class
    def __init__(self, server_address, handler_class):
        self.server_address = server_address
        self.HandlerClass = handler_class
        self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)     # 参数1协议，参数2指定字节流
        self.is_shutdown = False

    # 服务器启动函数
    def serve_forever(self):
        self.socket.bind(self.server_address)
        self.socket.listen(10)
        while not self.is_shutdown:
            # 1.接收请求
            request, client_address = self.get_request()
            # 2.处理请求，需要捕获异常。这里是阻塞式的处理请求
            try:
                self.process_request(request, client_address)
            except Exception as e:
                print(e)
        pass

    # 接收请求
    def get_request(self):
        return self.socket.accept()

    # 处理请求
    def process_request(self, request, client_address):
        # TODO: 处理请求
        self.close_request(request)

    # 关闭请求
    def close_request(self, request):
        request.shutdown(socket.SHUT_WR)
        request.close()

    # 关闭服务器
    def shutdown(self):
        self.is_shutdown = True     # 跳出循环标志
        pass
```

TCP服务器在进行网络通信时需要接收请求、处理请求、关闭请求，接收和关闭都是基于socket api。而处理请求正是需要自定义的部分，这里将请求处理这块功能构建一个handler类。

### 1.2.构造Handler

由于TCP是面向字节流的协议，所以它处理请求时，需要解码，而发送消息时需要编码，与之相关的一系列功能都实现在`StreamRequestHandler`中构造handler如下：

```python
# simpleHttpServer/handler/base_handler.py

class BaseRequestHandler:
    def __init__(self, server, request, client_address):
        self.server = server
        self.request = request
        self.client_address = client_address

    # 没有实现，留给具体业务逻辑实现
    def handle(self):
        pass


class StreamRequestHandler(BaseRequestHandler):
    def __init__(self, server, request, client_address):
        BaseRequestHandler.__init__(self, server, request, client_address)
        self.wbuf = []      # 定义缓存区
        self.rfile = self.request.makefile('rb')        # unix 一切皆文件
        self.wfile = self.request.makefile('wb')

    # 编码。字符串->字节码
    def encode(self, msg):
        # 判断msg是否为字节码，不是则编码
        if not isinstance(msg, bytes):
            msg = bytes(msg, encoding='utf-8')
        return msg

    # 解码。字节码解码为字符串
    def decode(self, msg):
        if isinstance(msg, bytes):
            msg = msg.decode()
        return msg

    # 读
    def read(self, length):
        msg = self.rfile.read(length)
        return self.decode(msg)

    # 读行
    def readline(self, length=65536):   # http报文默认最大长度65536
        msg = self.rfile.readline(length).strip()
        return self.decode(msg)

    # 写内容到handler缓冲区
    def write_content(self, msg):
        msg = self.encode(msg)
        self.wbuf.append(msg)

    # 发送
    def send(self):
        for line in self.wbuf:
            self.wfile.write(line)
        self.wfile.flush()
        self.wbuf = []      # 清空缓冲

    # 关闭
    def close(self):
        self.wfile.close()
        self.rfile.close()

```

### 1.3. 完善TCPServer

实现handler之后可以将之前的TODO项给完成：

```python

class TCPServer:

    ...

    # 处理请求
    def process_request(self, request, client_address):
        handler = self.HandlerClass(self, request, client_address)
        handler.handle()
        # 3.关闭请求
        self.close_request(request)
```

### 1.4. 测试TCPServer

构建测试类和测试函数：

```python
# simpleHttpServer/test/test.py

import socket
import time
import threading

from server.socket_server import TCPServer
from handler.base_handler import StreamRequestHandler


class TestBaseRequestHandler(StreamRequestHandler):
    # 实现一个handle，覆盖默认的handle
    def handle(self):
        msg = self.readline()
        print('Server received msg: ' + msg)
        time.sleep(1)
        self.write_content(msg)
        self.send()
        pass


# 测试SocketServer(TCPServer)
class SocketServerTest:
    def run_server(self):
        tcp_server = TCPServer(('127.0.0.1', 8888), TestBaseRequestHandler)
        tcp_server.serve_forever()

    # 客户端的具体连接逻辑
    def client_connect(self):
        client = socket.socket()
        client.connect(('127.0.0.1', 8888))
        client.send(b'Hello TCPServer\r\n')
        msg = client.recv(1024)
        print('Client recv msg: ' + msg.decode())

    def gen_clients(self, num):
        clients = []
        for i in range(num):
            # 新建线程连接客户端
            client_thread = threading.Thread(target=self.client_connect)
            clients.append(client_thread)
        return clients

    def run(self):
        server_thread = threading.Thread(target=self.run_server)
        server_thread.start()

        clients = self.gen_clients(5)
        for client in clients:
            client.start()

        server_thread.join()
        for client in clients:
            client.join()

if __name__ == '__main__':
    SocketServerTest().run()
```

运行起来后能看到5台客户端节点（运行在5个线程上）先后发送数据给TCPServer并接受TCPServer的回应。但这个过程中由于TCPServer处理请求的过程中是先接受请求再处理请求。也就是说当多个客户端连接时，服务器想要接收客户端2的请求必须等客户端1的请求处理完成之后，这样的话并发效率太低。

### 1.5. 多线程处理请求

修改的地方为：

```python
...
import threading
...


class TCPServer:

    ...

    # 服务器启动函数
    def serve_forever(self):
        self.socket.bind(self.server_address)
        self.socket.listen(10)
        while not self.is_shutdown:
            # 1.接收请求
            request, client_address = self.get_request()
            # 2.处理请求，需要捕获异常。这里是阻塞式的处理请求
            try:
                # self.process_request(request, client_address)
                self.process_request_multithread(request, client_address)
            except Exception as e:
                print(e)
        pass

    ...

    # 多线程处理请求
    def process_request_multithread(self, request, client_address):
        t = threading.Thread(target=self.process_request, args=(request, client_address))
        t.start()
```

至此，TCPServer就基本完善了。

## 2. 构造一个HTTP服务器

### 2.1. 包装BaseHTTPServer

直接基于（继承自）TCPServer类构造BaseHTTPServer：

```python
# simpleHttpServer/server/base_http_server.py

from server.socket_server import TCPServer


class BaseHTTPServer(TCPServer):
    def __init__(self, server_address, handler_class):
        self.server_name = 'BaseHTTPServer'
        self.version = 'v0.1'
        TCPServer.__init__(self, server_address, handler_class)
```

### 2.2. 编写BaseHTTPRequestHandler

同样的，HTTPServer也需要传入handler，且自然是HTTP数据报文的编解码相关方法集合。

```python

import logging

from handler.base_handler import StreamRequestHandler
from util import date_time_string
# 引入的date_time_string方法获取当前时间并构造成字符串

logging.basicConfig(level=logging.DEBUG,
                    format='%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')


class BaseHTTPRequestHandler(StreamRequestHandler):
    def __init__(self, server, request, client_address):
        self.default_http_version = 'HTTP/1.1'
        self.request_line = None
        self.method = None
        self.path = None
        self.version = None
        self.headers = None
        self.body = None
        StreamRequestHandler.__init__(self, server, request, client_address)

    # 请求的处理
    def handle(self):
        try:
            # 1. 解析请求
            if not self.parse_request():
                return
            # 2. 方法执行(GET/POST)
            method_name = 'do_' + self.method
            # 自检是否拥有这个方法
            if not hasattr(self, method_name):
                self.write_error(404, None)
                self.send()
                return
            method = getattr(self, method_name)
            method() # 需要进行应答报文的封装
            # 3. 发送结果
            self.send()
        except Exception as e:
            logging.exception(e)

    def do_GET(self):
        msg = '<h1>Hello World</h1>'
        self.write_response(200, 'Success')
        self.write_header('Content-Length', len(msg))
        self.end_headers()
        self.write_content(msg)

    # 解析请求头
    def parse_headers(self):
        headers = {}
        while True:
            line = self.readline()
            # 如果遇到空行，表示请求头已经结束，不是则解析
            if line:
                key, value = line.split(":", 1)
                key = key.strip()
                value = value.strip()
                headers[key] = value
            else:
                # 行为空则退出循环
                break
        return headers

    # 解析请求
    def parse_request(self):
        # 1. 解析请求行
        first_line = self.readline()
        self.request_line = first_line
        if not self.request_line:   # 如果请求行为空，直接返回
            return
        words = first_line.split()  # 切割空行
        # 请求方法、请求地址、请求的HTTP版本
        self.method, self.path, self.version = words

        # 2. 解析请求头
        self.headers = self.parse_headers()

        # 3. 解析请求内容
        key = 'Content-Length'
        # 检查内容长度这个键是否存在
        if key in self.headers.keys():
            body_length = int(self.headers[key])
            self.body = self.read(body_length)

        return True

    # 写入应答头
    def write_header(self, key, value):
        msg = '%s: %s\r\n' % (key, value)
        self.write_content(msg)

    # 写入正常HTTP应答的头部
    def write_response(self, code, msg=None):
        if msg is None:
            logging.info("%s, code: %s." % (self.request_line, code))
            msg = self.RESPONSES[code][0]
        # 状态行封装
        response_line = '%s %d %s\r\n' % (self.default_http_version, code, msg)
        self.write_content(response_line)
        # 应答头
        self.write_header('Server', '%s: %s' % (self.server.server_name, self.server.version))
        self.write_header('Date', date_time_string())
        pass

    DEFAULT_ERROR_MESSAGE_TEMPLATE = r'''
    <head>
    <title>Error response</title>
    </head>
    <body>
    <h1>Error response</h1>
    <p>Error code: %(code)d.
    <p>Message: %(message)s.
    <p>Error code explanation: %(code)s = %(explain)s.
    </body>
    '''

    RESPONSES = {
        100: ('Continue', 'Request received, please continue'),
        101: ('Switching Protocols',
              'Switching to new protocol; obey Upgrade header'),

        200: ('OK', 'Request fulfilled, document follows'),
        201: ('Created', 'Document created, URL follows'),
        202: ('Accepted',
              'Request accepted, processing continues off-line'),
        203: ('Non-Authoritative Information', 'Request fulfilled from cache'),
        204: ('No Content', 'Request fulfilled, nothing follows'),
        205: ('Reset Content', 'Clear input form for further input.'),
        206: ('Partial Content', 'Partial content follows.'),

        300: ('Multiple Choices',
              'Object has several resources -- see URI list'),
        301: ('Moved Permanently', 'Object moved permanently -- see URI list'),
        302: ('Found', 'Object moved temporarily -- see URI list'),
        303: ('See Other', 'Object moved -- see Method and URL list'),
        304: ('Not Modified',
              'Document has not changed since given time'),
        305: ('Use Proxy',
              'You must use proxy specified in Location to access this '
              'resource.'),
        307: ('Temporary Redirect',
              'Object moved temporarily -- see URI list'),

        400: ('Bad Request',
              'Bad request syntax or unsupported method'),
        401: ('Unauthorized',
              'No permission -- see authorization schemes'),
        402: ('Payment Required',
              'No payment -- see charging schemes'),
        403: ('Forbidden',
              'Request forbidden -- authorization will not help'),
        404: ('Not Found', 'Nothing matches the given URI'),
        405: ('Method Not Allowed',
              'Specified method is invalid for this resource.'),
        406: ('Not Acceptable', 'URI not available in preferred format.'),
        407: ('Proxy Authentication Required', 'You must authenticate with '
                                               'this proxy before proceeding.'),
        408: ('Request Timeout', 'Request timed out; try again later.'),
        409: ('Conflict', 'Request conflict.'),
        410: ('Gone',
              'URI no longer exists and has been permanently removed.'),
        411: ('Length Required', 'Client must specify Content-Length.'),
        412: ('Precondition Failed', 'Precondition in headers is false.'),
        413: ('Request Entity Too Large', 'Entity is too large.'),
        414: ('Request-URI Too Long', 'URI is too long.'),
        415: ('Unsupported Media Type', 'Entity body in unsupported format.'),
        416: ('Requested Range Not Satisfiable',
              'Cannot satisfy request range.'),
        417: ('Expectation Failed',
              'Expect condition could not be satisfied.'),

        500: ('Internal Server Error', 'Server got itself in trouble'),
        501: ('Not Implemented',
              'Server does not support this operation'),
        502: ('Bad Gateway', 'Invalid responses from another server/proxy.'),
        503: ('Service Unavailable',
              'The server cannot process the request due to a high load'),
        504: ('Gateway Timeout',
              'The gateway server did not receive a timely response'),
        505: ('HTTP Version Not Supported', 'Cannot fulfill request.'),
    }

    # 写入错误HTTP请求的结果
    def write_error(self, code, msg=None):
        s_msg, l_msg = self.RESPONSES[code]     # s-short; l-long
        if msg:
            s_msg = msg
        # 错误的html信息
        response_content = self.DEFAULT_ERROR_MESSAGE_TEMPLATE % {
            'code': code,
            'message': s_msg,
            'explain': l_msg
        }
        self.write_response(code, s_msg)
        # 请求头与应答正文间空行
        self.end_headers()
        self.write_content(response_content)
        pass

    # 结束应答头
    def end_headers(self):
        self.write_content('\r\n')
```

### 2.3. 测试BaseHTTPServer

添加测试文件：

```python
# simpleHttpServer/test/test.py

from server.base_http_server import BaseHTTPServer
from handler.base_http_handler import BaseHTTPRequestHandler

class BaseHTTPRequestHandlerTest:
    def run_server(self):
        BaseHTTPServer(('127.0.0.1', 9999), BaseHTTPRequestHandler).serve_forever()

    def run(self):
        self.run_server()

if __name__ == '__main__':
    # SocketServerTest().run()
    BaseHTTPRequestHandlerTest().run()
```

如果正常的话，启动程序后在浏览器访问该url会看到HelloWorld网页。

### 2.4. 构造一个简单的HTTP服务器

上一节的HTTP服务器只有一个默认的GET方法（直接返回Hello网页），在这一小节将实现GET方法和POST方法，实现访问WEB资源和表单登录的功能。

```python
# simpleHttpServer/server/simple_http_server.py

from server.base_http_server import BaseHTTPServer


class SimpleHTTPServer(BaseHTTPServer):
    def __init__(self, server_address, handler_class):
        self.server_name = 'SimpleHTTPServer'
        self.version = 'v0.1'
        BaseHTTPServer.__init__(self, server_address, handler_class)


if __name__ == '__main__':
    from handler.simple_http_handler import SimpleHTTPRequestHandler
    SimpleHTTPServer(('127.0.0.1', 8888), SimpleHTTPRequestHandler).serve_forever()

```

对应的handler:

```python
# simpleHttpServer/handler/simple_http_server.py

import os
import json
from handler.base_http_handler import BaseHTTPRequestHandler
from urllib import parse

# 获取当前文件夹目录并转换为绝对路径，再与'../resources'合并，就得到了resources路径
RESOURCES_PATH = os.path.join(os.path.abspath(os.path.dirname(__name__)), '../resources')


class SimpleHTTPRequestHandler(BaseHTTPRequestHandler):
    def __init__(self, server, request, client_address):
        BaseHTTPRequestHandler.__init__(self, server, request, client_address)

    def do_GET(self):
        found, resource_path = self.get_resources(self.path)
        if not found:
            self.write_error(404)
            self.send()
        else:
            with open(resource_path, 'rb') as f:
                # 调用系统API获取文件内容长度，在fs的第7个位置
                fs = os.fstat(f.fileno())
                clength = str(fs[6])
                self.write_response(200)
                self.write_header('Content-Length', clength)
                self.end_headers()
                while True:
                    buf = f.read(1024)
                    if not buf:
                        break
                    self.write_content(buf)
                # 不需要self.send()。因为handle中下一步就是send
        pass

    # 判断路径并获取资源
    def get_resources(self, path):
        url_result = parse.urlparse(path)
        resource_path = str(url_result[2])
        if resource_path.startswith('/'):
            resource_path = resource_path[1:]
        resource_path = os.path.join(RESOURCES_PATH, resource_path)
        if os.path.exists(resource_path) and os.path.isfile(resource_path):
            return True, resource_path
        else:
            return False, resource_path

    def do_POST(self):
        # 账号密码的校验
        body = json.loads(self.body)
        username = body['username']
        password = body['password']
        if username == 'eiger' and password == '123456':
            response = {'message': 'success', 'code': 0}
        else:
            response = {'message': 'failed', 'code': -1}
        response = json.dumps(response)
        self.write_response(200)
        self.write_header('Content-Length', len(response))
        # 避免跨域问题
        self.write_header('Access-Control-Allow-Origin', 'http://%s:%d' % (self.server.server_address[0], self.server.server_address[1]))
        self.end_headers()
        self.write_content(response)
```

现在就实现了一个带有GET和POST方法的简单http服务器

### 2.5. 测试SimpleHTTPServer

在simpleHttpServer/resources/目录下有index.html，其中有一段登陆用的脚本：

```js
    function submitData() {
        var username = $("#inputUsername").val();
        var password = $("#inputPassword").val();
        if (username.length == 0 || password.length == 0) {
            alert("账户或密码为空！")
        } else {
            $.ajax({
                type: 'POST',
                // 注意这里写定了必须是8888端口
                url: 'http://localhost:8888/api/login',
                data: JSON.stringify({
                    "username": username,
                    "password": password
                }),
                dataType: 'json',
                success: function (data) {
                    if (data.message == 'success') {
                        $('#loginModal').modal('hide');
                        console.log('success');
                        window.location="https://eiger.me";
                    } else {
                        alert('账号密码校验失败');
                    }
                }
            })
        }
    };

```

也就是说，当启动SimpleHTTPServer后浏览器访问`localhost:8888/index.html`会进入index.html并可以以账号eiger、密码123456进行登录，成功登陆后会跳转至我的个人博客网站。这表示测试成功。

## 最后

完整代码：<https://github.com/azd1997/python-simpleHttpServer>
