---
title: "Go语言协程池"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["go语言"]
tags: ["go语言", "并发"]
keywords: ["协程池", "并发编程", "协程通信"]

---

### 1. Golang并发三件套

​		这里不作过多介绍：goroutine、channel、select。goroutine是go语言的协程机制，channel负责协程间的通信，select语法用来对channel传输的数据做选择处理。

​		简单的用例如下：

```go
package main

func main() {
    
    c := make(chan bool)  
    fmt.Println("我是主协程")
    go worker(c)
    
    //循环等待各个管道数据
    select {
    case finished <- c:
        if funished == true {
            fmt.Println("worker告诉我工作做完了")
        }
    }
    
    return
}

func worker(c chan bool) {
    fmt.Println("我是worker协程")
    c <- true //工作结束，向主协程返回一个信号量
}
```



### 2. 为什么需要协程池

尽管Go语言可以轻松的支持百万级并发，我们可以不停地 go func()来创建协程，但是要注意的是：CPU执行协程任务的时间是随机且大致相同的（“雨露均沾”），而CPU在协程间切换需要一定的开销，如果无节制的开启协程，当协程数量非常多时，协程间切换的开销可能会使得高并发节约成本的初衷得不偿失。

![1562585061449](/images/1562585061449.png)

通常，进程与线程的数量都设置为CPU核心数量，而协程数取决于CPU频率和内存等，对于具体的每一台计算机，都有一个最优的协程数。因此，需要将协程数量限制在最优协程数上，使得CPU计算机性能得到最大利用。而协程池就是设置一个经验上的最优协程数，开启所有的协程，去执行一个接一个的任务。

![1562585087280](/images/1562585087280.png)

什么时候需要协程池？从前边描述知协程池是无论任务有多少，都要开启这许多的协程。因此一般适用于淘宝这种要求高并发的场景。

### 3. 简单的协程池

```go
package main

import "fmt"
import "time"

//***************************Task部分

//Task对外公开，所以要export
type Task struct {
	f func() error  //一个task代表一个任务，函数形式
}

func NewTask(arg_f func() error) *Task {
    return &Task{
        f:arg_f,
    }
}

func (t *Task) Execute() {
    t.f()
}

//***************************Pool部分
type Pool struct {
    EntryChan chan *Task  //对外的任务入口
    JobsChan chan *Task  //内部的任务队列
    workerNum int  //协程池最大的worker数
}

func NewPool(num int) *Pool {
    return &Pool{
        EntryChan:make(chan *Task),
        JobsChan:make(chan *Task),
        workerNum:num,
    }
} 

//Pool创建worker，由worker一直去JobsChan取任务并执行
func (p *Pool) worker(workerId int) {
    for task := range p.JobsChan {
        task.Execute()
        fmt.Println("workerID ", workerId, " 执行完了一个任务")
    }
}

//Pool运行
func (p *Pool) run() {
    //根据workerNum创建workers
    for i:=0;i<p.workerNum;i++ {
        go p.worker(i) //i作为workerId
    }
    //从EntryChan取任务，发送给JobsChan
    for task := range p.EntryChan {
        p.JobsChan <- task
    }
}

func main() {
    //创建任务
    t := NewTask(func() error {
        fmt.Println(time.Now())
    })
    //创建协程池
    p := NewPool(4)
    //把任务交给协程池
    go func() {
        for {
            p.EntryChan <- t
        }
    }
    
    //启动协程池
    p.run()
}
```

