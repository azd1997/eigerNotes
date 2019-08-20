---
title: "资源管理与出错处理"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["go"]
tags: ["go", "error"]
keywords: ["defer", "panic", "recover", "errorWrapper"]

---

**Catch All The Errors!**

### 1. 资源管理与defer调用

defer调用特点：

- 确保函数结束时发生

- 多个defer倒序执行

例子：

```go
package main

import "fmt"

func main() {
   tryDefer()
}

func tryDefer() {
   defer fmt.Println(1)
   defer fmt.Println(2)
   fmt.Println(3)
   return
   fmt.Println(4)
}
```

执行结果：

```
3
2
1

Process finished with exit code 0
```

defer相比于顺序执行的好处在于，不用担心中间出现return、panic等程序退出。

defer常用语成对的资源操作：

- Open/Close

- Lock/Unlock

- PrintHeader/PrintFooter

另一个例子：



```go
package fib

// 1 1 2 3 5 8 13 ...
func Fibonacci() func() int {
	a, b := 0, 1
	return func() int {
		a, b = b, a + b
		return a
	}
}
```

```go
package main

import (
   "bufio"
   "fmt"
   "go-mastering-lecture/07-errors/defer-02/fib"
   "os"
)

func main() {
   tryDefer2()
   //writeFile("07-errors/fib.txt")
}

func tryDefer() {
   defer fmt.Println(1)
   defer fmt.Println(2)
   fmt.Println(3)
   return
   fmt.Println(4)
}

func tryDefer2() {
   for i:=0;i<100;i++ {
      defer fmt.Println(i)
      if i == 30 {
         panic("print too many")
      }
   }
}

func writeFile(filename string) {
   file, err := os.Create(filename)
   if err != nil {
      panic(err)
   }
   defer file.Close()

   writer := bufio.NewWriter(file)  //写入缓存
   defer writer.Flush()  //写入文件

   f := fib.Fibonacci()
   for i := 0; i < 20; i++ {
      fmt.Fprintln(writer, f())
   }
}
```

main函数中执行tryDefer2将会倒序打出30,29，...，0

执行writeFile将会新建文档，写入斐波那契序列。

### 2. 初尝错误处理

对前面writeFile()进行修改：

```go
func writeFileV2(filename string) {
   file, err := os.OpenFile(filename, os.O_EXCL|os.O_CREATE, 0666)	//os.EXCL要求文件本不存在，因此在已有fib.txt的时候会报错
   if err != nil {
      panic(err)  //执行之后会panic
   }
   defer file.Close()

   writer := bufio.NewWriter(file)  //写入缓存
   defer writer.Flush()  //写入文件

   f := fib.Fibonacci()
   for i := 0; i < 20; i++ {
      fmt.Fprintln(writer, f())
   }
}
```

执行结果：

```
panic: open 07-errors/fib.txt: The file exists.

goroutine 1 [running]:
main.writeFileV2(0x4c9000, 0x11)
	E:/GO/src/go-mastering-lecture/07-errors/defer-01/defer.go:52 +0x25a
main.main()
	E:/GO/src/go-mastering-lecture/07-errors/defer-01/defer.go:13 +0x3d

Process finished with exit code 2
```

显然，panic会使得输出结果变得非常不利于阅读。那么怎么改进呢？

可以使用fmt.Println这类输出函数，再return

```go
fmt.Println("The file exists.")
//fmt.Println("Error:", err)
return
```

这样结果变成了：

```
The file exists.

Process finished with exit code 0
```

再来看看OpenFile返回的error：

```go
// If there is an error, it will be of type *PathError.
func OpenFile(name string, flag int, perm FileMode) (*File, error) {
   testlog.Open(name)
   return openFileNolog(name, flag, perm)
}
```

```go
// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
   Error() string
}
```

根据OpenFile()注释，返回的是*PathError。

```go
if err != nil {
   //panic(err)
   //fmt.Println("The file exists.")
   //fmt.Println("Error:", err)
   if pathError, ok := err.(*os.PathError); !ok {
      panic(err)
   } else {
      fmt.Println(pathError.Op, pathError.Path, pathError.Err)
   }
   return
}
```

执行结果：

```
open 07-errors/fib.txt The file exists.

Process finished with exit code 0
```

也可以自定义error：

```go
func writeFileV3(filename string) {
   file, err := os.OpenFile(filename, os.O_EXCL|os.O_CREATE, 0666)
   err = errors.New("this is a custom error.")
   if err != nil {
      if pathError, ok := err.(*os.PathError); !ok {
         panic(err)
      } else {
         fmt.Println(pathError.Op, pathError.Path, pathError.Err)
      }
      return
   }
   defer file.Close()

   writer := bufio.NewWriter(file)  //写入缓存
   defer writer.Flush()  //写入文件

   f := fib.Fibonacci()
   for i := 0; i < 20; i++ {
      fmt.Fprintln(writer, f())
   }
}
```

执行结果：

```
panic: this is a custom error.

goroutine 1 [running]:
main.writeFileV3(0x4b0a9f, 0x11)
	E:/GO/src/go-mastering-lecture/07-errors/defer-01/defer.go:80 +0xa5
main.main()
	E:/GO/src/go-mastering-lecture/07-errors/defer-01/defer.go:15 +0x3d

Process finished with exit code 2
```

也可以通过实现内建的error接口来自定义错误类型：

```
type ErrFileAlreadyExists struct {
   ErrMsg string  //错误信息
   File string  //文件路径及名称
}

func (err *ErrFileAlreadyExists) Error() string {
   return fmt.Sprintf("出错：%s, %s", err.File, err.ErrMsg)
}
```

### 3. 统一错误处理逻辑

现在实现一个将前边创建的fib文件内容展示在网页上的http服务器：

```go
package main

import (
   "io/ioutil"
   "net/http"
   "os"
)

func main() {
   // 127.0.0.1:8080/list/07-errors/fib.txt
   http.HandleFunc("/list/", handleListFile)
   err := http.ListenAndServe(":8080", nil)
   if err != nil {
      panic(err)
   }
}

func handleListFile(w http.ResponseWriter, r *http.Request) {
   path := r.URL.Path[len("/list/"):] //也可以使用strings中的去除前缀方法
   file, err := os.Open(path)
   if err != nil {
      panic(err)
   }
   defer file.Close()

   all, err := ioutil.ReadAll(file)
   if err != nil {
      panic(err)
   }
   _, err = w.Write(all)
   if err != nil {
      panic(err)
   }
}
```

运行后浏览器访问结果：

![1562761154997](/images/1562761154997.png)

但是这里没有做错误请求的处理，这使得当浏览器访问错误的url（例如：http://localhost:8080/list/07-errors/fib.txta）时没有任何响应，终端输出了一长串panic。

![1562763262725](/images/1562763262725.png)

现在将handleListFile()中Open(path)错误处理进行修改：

```go
file, err := os.Open(path)
if err != nil {
   //panic(err)
   http.Error(w, err.Error(), http.StatusInternalServerError)
   return
}
```

重新运行，并访问http://localhost:8080/list/07-errors/fib.txta

![1562764035481](/images/1562764035481.png)

但是这样做也有问题：将程序内部的错误信息暴露给普通用户了。

那么现在需要对错误进行一个包装，包装成用来给普通用户看的error。

代码改为：

```go

import (
	"context"
	"github.com/golang/gddo/log"
	"io/ioutil"
	"net/http"
	"os"
)

func main() {
   // 127.0.0.1:8080/list/07-errors/fib.txt
   //http.HandleFunc("/list/", handleListFile)
   http.HandleFunc("/list/", errWrapper(handleListFileV2))
   err := http.ListenAndServe(":8080", nil)
   if err != nil {
      panic(err)
   }
}

type appHandler func(http.ResponseWriter, *http.Request) error

func errWrapper(handler appHandler) func(http.ResponseWriter, *http.Request) {
	return func(w http.ResponseWriter, r *http.Request) {
		err := handler(w, r)
		if err != nil {
			//log是服务端终端打印的日志，给维护人员看的
			log.Warn(context.TODO(), "Error handling request: %s", err.Error())
			code := http.StatusOK
			switch {
			case os.IsNotExist(err):	//请求的文件不存在
				code = http.StatusNotFound
			default:
				code = http.StatusInternalServerError
			}
			//http.StatusText(http.StatusNotFound隐藏内部错误，只告诉外部一个错误代码的简单信息
			http.Error(w, http.StatusText(code), code)
		}
	}
}

func handleListFileV2(w http.ResponseWriter, r *http.Request) error {
	path := r.URL.Path[len("/list/"):]	//也可以使用strings中的去除前缀方法
	file, err := os.Open(path)
	if err != nil {
		return err
	}
	defer file.Close()

	all, err := ioutil.ReadAll(file)
	if err != nil {
		return err
	}
	_, err = w.Write(all)
	if err != nil {
		return err
	}
	return nil
}
```

现在结果变成了：

![1562766248918](/images/1562766248918.png)

终端输出日志：

```
t=2019-07-10T21:43:51+0800 lvl=warn msg="Error handling request: %s" open 07-errors/fib.txta: The system cannot find the file specified.=nil LOG15_ERROR="Normalized odd number of arguments by adding nil"
```

这正是我们想要的效果：避免将内部信息暴露给普通用户。

进一步完善前边的错误处理代码，增加其他的错误类型：

```go
case os.IsNotExist(err):    //请求的文件不存在
   code = http.StatusNotFound
case os.IsPermission(err):
   code = http.StatusForbidden
default:
   code = http.StatusInternalServerError
}
```

为了测试没有权限情况下的响应，先给文件设置权限，将fib.txt复制一份为fib2.txt并设置权限为500（`r-x------`）

```shell
$ sudo chmod 500 fib2.txt
```

（linux下root用户可读可运行，其他用户无任何权限）。

//Windows TODO



这里的包装器errWrapper()是函数式的编程，输入是一个函数，输出又是一个函数。

### 4. panic和recover

#### 4.1 panic

- 停止当前函数执行
- 一直向上返回，执行每一层的defer
- 如果没有遇见recover，程序退出。

#### 4.2 recover

- 仅在defer调用中执行
- 可以获取panic的值（panic函数传入的参数）
- 如果无法处理，可以重新panic

#### 4.3 实例

```go
package main

import (
   "errors"
   "fmt"
)

func main() {
   tryRecover()
}

//panic传入参数类型为interface{}；recover输出类型为interface{}
func tryRecover() {
   defer func() {
      r := recover()  //获取到了panic的传入值
      if err, ok := r.(error); ok {
         fmt.Println("出错了：", err)
      } else {
          panic(fmt.Sprintln("我不知道该干嘛：", r))
      }
   }()

   panic(errors.New("出错panic"))
}
```

输出：

```
出错了： 出错panic

Process finished with exit code 0
```

将`panic(errors.New("出错panic"))`注释掉，换成`panic("随便一句话")`

输出：

```
panic: 随便一句话 [recovered]
	panic: 我不知道该干嘛： 随便一句话


goroutine 1 [running]:
main.tryRecover.func1()
	E:/GO/src/go-mastering-lecture/07-errors/panicAndRecover/recover.go:18 +0x195
panic(0x4a2ea0, 0x4dcbf0)
	C:/Go/src/runtime/panic.go:522 +0x1c3
main.tryRecover()
	E:/GO/src/go-mastering-lecture/07-errors/panicAndRecover/recover.go:22 +0x5c
main.main()
	E:/GO/src/go-mastering-lecture/07-errors/panicAndRecover/recover.go:8 +0x27

Process finished with exit code 2
```

### 5. 错误处理综合处理

如果前边的服务器在编写时由两个人开发，一个人写main，一个人写handleFunc，假如出现了这种情况：(main中路由为"/"，而handleFunc中为"/list/")

```go
func main() {
   // 127.0.0.1:8080/list/07-errors/fib.txt
   //http.HandleFunc("/list/", handleListFile)
   http.HandleFunc("/", errWrapper(handleListFileV2))
   err := http.ListenAndServe(":8080", nil)
   if err != nil {
      panic(err)
   }
}
```

那么当我请求http://localhost:8080/list/07-errors/fib.txt时一样是正常运行的，请求http://localhost:8080/07-errors/fib.txt返回NotFound也是正常的，因为路径不对。但是如果请求http://localhost:8080/azd（/azd长度小于/list/）呢？

结果是出现了数组越界的panic了。（问题出在handleFunc中解析请求的文本路径时采用了`path := r.URL.Path[len("/list/"):]`的做法）

![1562772065570](/images/1562772065570.png)

在输出中可以看到，httpServer虽然panic了，但是程序并没有退出，显然http包对此作了保护，在某个地方进行了recover（server.go/serve()）。

那么现在怎么来作错误处理，使得当发生这种情况时不panic呢？

使用recover()。在errWrapper中添加对handleFunc（这里指handleListFileV2）的panic的recover。

```go
func errWrapperV2(handler appHandler) func(http.ResponseWriter, *http.Request) {
   return func(w http.ResponseWriter, r *http.Request) {

      //对handleFunc的panic作recover保护
      defer func() {
         r := recover()  //获取panic值
         fmt.Printf("panic: %v", r)
         http.Error(w, http.StatusText(http.StatusInternalServerError), http.StatusInternalServerError)
      }()

      err := handler(w, r)
      if err != nil {
         //log是服务端终端打印的日志，给维护人员看的
         log.Warn(context.TODO(), "Error handling request: %s", err.Error())
         code := http.StatusOK
         switch {
         case os.IsNotExist(err):   //请求的文件不存在
            code = http.StatusNotFound
         case os.IsPermission(err):
            code = http.StatusForbidden
         default:
            code = http.StatusInternalServerError
         }
         //http.StatusText(http.StatusNotFound隐藏内部错误，只告诉外部一个错误代码的简单信息
         http.Error(w, http.StatusText(code), code)
      }
   }
}
```

执行结果：

![1562774945426](/images/1562774945426.png)

![1562775060842](/images/1562775060842.png)

这满足了我们的需求。

现在进一步优化：写handleFunc的时候就可以对路由进行检查：

```go
const PREFIX  = "/list/"
func handleListFileV3(w http.ResponseWriter, r *http.Request) error {
   //增加对URL Path的检查,检查Path中这个"list"是否是出现在最前面
   if strings.Index(r.URL.Path, PREFIX) != 0 {
      return errors.New("path必须以 "+ PREFIX + " 开头")
   }

   path := r.URL.Path[len(PREFIX):]
   file, err := os.Open(path)
   if err != nil {
      return err
   }
   defer file.Close()

   all, err := ioutil.ReadAll(file)
   if err != nil {
      return err
   }
   _, err = w.Write(all)
   return err
}
```

先访问下http://localhost:8080/list/07-errors/fib.txt

![1562775825093](/images/1562775825093.png)

![1562775882563](/images/1562775882563.png)

发现正常情况下，也出现问题，原因是panic为空时也执行了recover，现在将之纠正：

```go
func errWrapperV3(handler appHandler) func(http.ResponseWriter, *http.Request) {
   return func(w http.ResponseWriter, r *http.Request) {

      //对handleFunc的panic作recover保护
      defer func() {
         if r := recover(); r != nil {   //纠正处
            fmt.Printf("panic: %v\n", r)
            http.Error(w, http.StatusText(http.StatusInternalServerError), http.StatusInternalServerError)
            return
         }
      }()

      err := handler(w, r)
      if err != nil {
         //log是服务端终端打印的日志，给维护人员看的
         log.Warn(context.TODO(), "Error handling request: %s", err.Error())
         code := http.StatusOK
         switch {
         case os.IsNotExist(err):   //请求的文件不存在
            code = http.StatusNotFound
         case os.IsPermission(err):
            code = http.StatusForbidden
         default:
            code = http.StatusInternalServerError
         }
         //http.StatusText(http.StatusNotFound隐藏内部错误，只告诉外部一个错误代码的简单信息
         http.Error(w, http.StatusText(code), code)
      }
   }
}
```

重新访问http://localhost:8080/list/07-errors/fib.txt，结果归于正常。

访问http://localhost:8080/azd，网页显示Internal Server Error，终端输出：

```
t=2019-07-11T00:29:51+0800 lvl=warn msg="Error handling request: %s" path必须以 /list/ 开头=nil LOG15_ERROR="Normalized odd number of arguments by adding nil"
```

但是呢，这个自定义的`errors.New("path必须以 "+ PREFIX + " 开头")`其实是应该给普通用户看的，告诉他们请求的url错了。

现在来进一步改进：增加自定义错误类型，并对自定义错误直接返回给网页客户端。

完整代码：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
	"strings"
)

type userErrorInterface interface {
	error  //组合自error接口
	Message() string
}
func main() {
	// 127.0.0.1:8080/list/07-errors/fib.txt
	http.HandleFunc("/", errWrapperV4(handleListFileV4))
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		panic(err)
	}
}

type appHandler func(http.ResponseWriter, *http.Request) error

func errWrapperV4(handler appHandler) func(http.ResponseWriter, *http.Request) {
	return func(w http.ResponseWriter, r *http.Request) {
		//对handleFunc的panic作recover保护
		defer func() {
			if r := recover(); r != nil {
				fmt.Printf("panic: %v\n", r)
				http.Error(w, http.StatusText(http.StatusInternalServerError), http.StatusInternalServerError)
				return
			}
		}()

		err := handler(w, r)
		if err != nil {
			fmt.Println("出错：", err.Error())
			//增加对自定义错误的区别处理
			if userErr, ok := err.(userErrorInterface); ok {
				//直接把自定义的错误返回给用户，因为是需要他们知道这些信息
				http.Error(w, userErr.Message(), http.StatusBadRequest)
			}

			code := http.StatusOK
			switch {
			case os.IsNotExist(err):	//请求的文件不存在
				code = http.StatusNotFound
			case os.IsPermission(err):
				code = http.StatusForbidden
			default:
				code = http.StatusInternalServerError
			}
			//http.StatusText(http.StatusNotFound隐藏内部错误，只告诉外部一个错误代码的简单信息
			http.Error(w, http.StatusText(code), code)
		}
	}
}

const PREFIX  = "/list/"

type userError string
func (e userError) Error() string {
	return e.Message()
}
func (e userError) Message() string {
	return string(e)
}

func handleListFileV4(w http.ResponseWriter, r *http.Request) error {
	//增加对URL Path的检查,检查Path中这个"list"是否是出现在最前面
	if strings.Index(r.URL.Path, PREFIX) != 0 {
		//自定义错误
		return userError("path必须以 "+ PREFIX + " 开头")
	}
	path := r.URL.Path[len(PREFIX):]	//也可以使用strings中的去除前缀方法
	file, err := os.Open(path)
	if err != nil {
		return err
	}
	defer file.Close()

	all, err := ioutil.ReadAll(file)
	if err != nil {
		return err
	}
	_, err = w.Write(all)
	return err
}
```

网页运行结果：

![1562777302224](/images/1562777302224.png)

终端日志输出：

![1562777343460](/images/1562777343460.png)

### 6. 总结（error vs panic）

尽量不要用panic。具体的判断方法：

- 意料之中的：使用error，比如文件打不开。这种时候需要自己去做出错后的处理。
- 意料之外的：才用panic，比如数组越界。（能保护的话还是要尽量去保护）

part 5中实现了错误处理的综合示例，使用了：

- defer + panic + recover
- Type Assertion
- 函数式编程的应用