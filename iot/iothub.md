---
title: "IotHub物联网平台搭建：从0到1"
date: 2019-08-17T02:55:10+08:00
draft: false
categories: ["iot"]
tags: ["IotHub"]
keywords: ["IotHub", "NodeJS", "EMQX", "MongoDB", "RabbitMQ"]
---

# Iothub物联网平台搭建：从0到1

## 1. 项目介绍

## 2. 准备工作台

本节安装开发物联网平台用到的组件，并将开发环境搭建起来。

### 2.1 安装组件

在安装组件前建议先更新一下软件列表及软件：

```shell
sudo apt update
sudo apt upgrade
```

#### 2.1.1 MongoDB

MongoDB是一个基于分布式文件存储的数据库，在该项目中作为物联网平台的主要数据存储工具。

- 安装：

    ```shell
    sudo apt install mongodb
    ```

- 进入mongodb：

    ```shell
    mongo
    ```

    输出：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ mongo
    MongoDB shell version v3.6.3
    connecting to: mongodb://127.0.0.1:27017
    MongoDB server version: 3.6.3
    Welcome to the MongoDB shell.
    For interactive help, type "help".
    For more comprehensive documentation, see
        http://docs.mongodb.org/
    Questions? Try the support group
        http://groups.google.com/group/mongodb-user
    Server has startup warnings:
    2019-08-14T09:51:31.023-0400 I STORAGE  [initandlisten]
    2019-08-14T09:51:31.023-0400 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
    2019-08-14T09:51:31.023-0400 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
    2019-08-14T09:51:32.038-0400 I CONTROL  [initandlisten]
    2019-08-14T09:51:32.038-0400 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
    2019-08-14T09:51:32.038-0400 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
    2019-08-14T09:51:32.038-0400 I CONTROL  [initandlisten]
    >
    > ^C
    bye

    ```

    (ps: CTRL+C退出工作台)

- 启动与停止：

    ```shell
    sudo service mongodb start
    sudo service mongodb stop
    ```

#### 2.1.2 Redis

Redis是一个高效的内存数据库，本项目使用其实现缓存和简单的队列功能。

- 安装：

    ```shell
    sudo apt install redis-server
    ```

    版本信息：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ redis-server --version
    Redis server v=4.0.9 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 build=9435c3c2879311f3
    ```

- 运行：

    ```shell
    redis-server  # 运行服务端
    redis-cli  # 运行客户端
    ```

    测试服务器是否启动成功：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ redis-cli
    127.0.0.1:6379> ping
    PONG
    127.0.0.1:6379>
    ```

    (ps: CTRL+C退出客户端交互)

#### 2.1.3 Node.js

Node.js是一个基于Chrome V8的Javascript运行环境，本项目用它开发物联网平台的主要功能。

- 安装Node.js稳定版本

    ```shell
    sudo apt install nodejs
    ```

- 安装npm

    ```shell
    sudo apt install npm
    ```

- 查看版本信息

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ nodejs --version
    v8.10.0
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ npm --version
    6.10.3
    ```

#### 2.1.4 RabbitMQ

RabbitMQ是Erlang编写的AMQP Broker，物联网平台用其作为队列系统，实现平台内部以及平台到业务系统的异步通信。

安装及使用参考： <https://codeleading.com/article/3099906725/>

- 安装

    ```shell
    sudo apt install rabbitmq-server
    ```

#### 2.1.5 EMQ X

EMQ X 是Erlang编写的MQTT Broker，物联网平台使用它实现MQTT/CoAP协议接入，并使用其一些高级功能来简化开发。

建议仔细阅读其官方文档，有利于了解物联网平台的构架。

- <https://docs.emqx.io/docs/tutorial/zh/>

安装参考(官方文档)：

- <https://docs.emqx.io/docs/tutorial/zh/quick_start/install_first.html>

EMQ X Broker下载页面： <https://www.emqx.io/products/broker>。选择v3.2.2 linux/ubuntu18.04/zip包下载（对于这类不是很熟悉的软件，尤其是没有apt源的软件，尽量建议下载压缩包，自行安装。）

解压后将文件夹复制到/usr/local下，~~设置环境变量`export PATH=$PATH:/usr/local/emqx/bin`~~，不设置环境变量（因为后边还装了emqx-edge，他俩执行文件名是完全一样的，而我又不想修改程序名），每次启动时自项目根目录启动，也就是`emqx/`目录下启动。

检查安装成功与否：

```shell
cd /usr/local/emqx
./bin/emqx console
```

这里顺便安装了一下EMQ X Edge，与本文章物联网平台实现暂时无关，考虑到后面学习可能会使用到，所以也安装了。安装文档在官网

- <https://docs.emqx.io/docs/edge/v3/cn/>

由于前面将软件包复制到/usr/local下需要用sudo，所以现在`emqx`和`emqx-edge`（emqx-edge安装目录）只有root用户才有权限，为了让其他用户也能运行，这里修改下文件夹全部内容的权限：

```shell
sudo chmod -R 777 /usr/local/emqx
sudo chmod -R 777 /usr/local/emqx-edge
```

(ps: 实际生产环境千万别无脑提升权限，这里为了省事)

现在附上emqx的执行测试：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/emqx$ ./bin/emqx console
Exec: /usr/local/emqx/erts-10.3/bin/erlexec -boot /usr/local/emqx/releases/v3.2.1/emqx -mode embedded -boot_var ERTS_LIB_DIR /usr/local/emqx/erts-10.3/../lib -mnesia dir "/usr/local/emqx/data/mnesia/emqx@127.0.0.1" -config /usr/local/emqx/data/configs/app.2019.08.14.23.01.21.config -args_file /usr/local/emqx/data/configs/vm.2019.08.14.23.01.21.args -vm_args /usr/local/emqx/data/configs/vm.2019.08.14.23.01.21.args -- console
Root: /usr/local/emqx
/usr/local/emqx
Erlang/OTP 21 [erts-10.3] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:32] [hipe]

Starting emqx on node emqx@127.0.0.1
Start http:management listener on 8080 successfully.
Start http:dashboard listener on 18083 successfully.
Start mqtt:tcp listener on 127.0.0.1:11883 successfully.
Start mqtt:tcp listener on 0.0.0.0:1883 successfully.
Start mqtt:ws listener on 0.0.0.0:8083 successfully.
Start mqtt:ssl listener on 0.0.0.0:8883 successfully.
Start mqtt:wss listener on 0.0.0.0:8084 successfully.
EMQ X Broker 3.2.2 is running now!
Eshell V10.3  (abort with ^G)
(emqx@127.0.0.1)1>
```

说明没有问题。

### 2.2 项目实体定义

- **Maque IotHub:** 即将开发的物联网平台，简称IotHub
- **Maque IotHub Server API:** 服务端API，以Restful API形式将功能提供给外部业务系统调用，简称Server API
- **Maque IotHub Server:** 服务端，包含了Server API和主要的IotHub服务端功能代码，简称IotHub Server
- **业务系统:** 指实现特定物联网应用服务端的业务逻辑系统，它通过调用Server API来控制设备、使用设备上传的数据，IotHub为它屏蔽了和设备交互的细节
- **Maque IotHub DeviceSDK:** IotHub提供的设备端SDK，设备通过调用SDK提供的API接入IotHub，并和业务系统发生交互，简称Device SDK
- **设备应用代码:** 实现设备具体功能的代码，比如打开灯、显示温度等，调用DeviceSDK来使用IotHub提供的功能

### 2.3 项目结构

该项目包括两个Node.js的项目，一个是Server项目，一个是DeviceSDK项目。

#### 2.3.1 Maque IotHub Server

基于Express项目开发，Express是一个轻量级的、易于开发Restful API的Node.js的Web框架。安装、使用参考： <http://expressjs.com.cn>

关于Express的项目创建参见[10.3.1 Express项目创建](#1031-express%e9%a1%b9%e7%9b%ae%e5%88%9b%e5%bb%ba)





## 3. 设备生命周期管理

## 4. 上行数据处理

## 5. 下行数据处理

## 6. 进一步抽象

## 7. 扩展EMQ X

## 8. CoAP

## 9. 总结

## 10. 软件与编程语言教程

### 10.1 MongoDB教程

### 10.2 Redis教程

### 10.3 Node.js教程

#### 10.3.1 Express项目创建

##### 10.3.1.1 手动安装Express并创建项目

安装express项目教程参考： <http://www.expressjs.com.cn/starter/installing.html>

我的安装流程：

```shell
# 1.建好项目目录并切换到项目目录
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop$ mkdir IotHub
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop$ ls
IotHub  iothub-notes  linux-notes  nlp-notes
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop$ cd IotHub/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub$ mkdir IotHub-Server
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub$ mkdir IotHub-DeviceSDK
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub$ ls
IotHub-DeviceSDK  IotHub-Server
# 注意这里使用了通配符
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub$ cd *Server
# 2.在项目目录下初始化npm项目
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (iothub-server)
version: (1.0.0)
description:
entry point: (index.js) server.js
test command:
git repository:
keywords:
author: Eiger
license: (ISC)
About to write to /home/eiger/Desktop/IotHub/IotHub-Server/package.json:

{
  "name": "iothub-server",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Eiger",
  "license": "ISC"
}


Is this OK? (yes)
# 3.安装express框架并添加至依赖（--save）
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ npm install expressjs --save
npm ERR! code EACCES
npm ERR! syscall open
npm ERR! path /home/eiger/.npm/_cacache/tmp/a2c4ad2d
npm ERR! errno -13
npm ERR!
npm ERR! Your cache folder contains root-owned files, due to a bug in
npm ERR! previous versions of npm which has since been addressed.
npm ERR!
npm ERR! To permanently fix this problem, please run:
npm ERR!   sudo chown -R 1000:1000 "/home/eiger/.npm"

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/eiger/.npm/_logs/2019-08-15T04_12_52_463Z-debug.log
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ sudo chown -R 1000:1000 "/home/eiger/.npm"
[sudo] password for eiger:
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ npm install expressjs --save
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN iothub-server@1.0.0 No description
npm WARN iothub-server@1.0.0 No repository field.

+ expressjs@1.0.1
added 1 package and audited 1 package in 2.474s
found 0 vulnerabilities

eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ ls
node_modules  package.json  package-lock.json
# 安装tree工具查看目录结构树形图
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ sudo apt install tree
# tree安装输出信息略
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ tree
.
├── node_modules
│   └── expressjs
│       ├── package.json
│       └── README.md
├── package.json
└── package-lock.json

2 directories, 4 files
```
接下来可以参考 <http://www.expressjs.com.cn/starter/hello-world.html>开发第一个Helloword代码

##### 10.3.1.2 安装Express生成器并使用其生成Express项目

安装及使用方法参考： <http://www.expressjs.com.cn/starter/generator.html>

在本文章中实际采用express生成器来生成其框架代码，并基于此进行开发。因此，先将上一小节中产生的文件全部删除

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ rm -f *
rm: cannot remove 'node_modules': Is a directory
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ ls
node_modules
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ rm -r node_modules/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$ ls
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/IotHub/IotHub-Server$
```

我的安装及express项目生成流程：



### 10.4 RabbitMQ教程

### 10.5 EMQ X教程

### 10.6 Javascript教程

## 11. 平台的进一步改进
