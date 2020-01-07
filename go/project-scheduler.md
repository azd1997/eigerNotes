---
title: "Go项目实战——基于ETCD的分布式任务调度"
date: 2019-11-19T02:58:24+08:00
draft: false
categories: ["go"]
tags: ["project"]
keywords: ["go", "crontab", "scheduler", "etcd", "mongodb"]
---

## 系统架构

## 基础铺垫

### os/exec包

```go
// 1. 创建Command
cmd := exec.Command(name string, args ...string)
// 例如： cmd := exec.Command("bash", "-c", "sleep 5s;ls -l")

// 2.（底层通过pipe机制实现）获取shell命令的输出结果 []byte
output, err := cmd.CombinedOutput()

// 3. 能够被杀死的command
cmd := exec.CommandContext(ctx context.Context, name string, args ...string)
// 用法：
ctx, cancelFunc := context.WithCancel(context.TODO())       // ctx : chan byte;  ctx close(chan byte)
go func() {
    cmd := exec.CommandContext(ctx context.Context, name string, args ...string)
    output, err := cmd.CombinedOutput()     // 正常应该用通道把结果传出去，这里省略
}
cancelFunc()    // main协程主动调用cancelFunc会关闭ctx的这个chan，然后cmd内部会select{case <- ctx.Done()}   然后获取执行的shell命令子进程的pid，并调用kill pid杀死进程
```

### cronexpr包

```go
// github.com/gori/cronexpr
// 支持秒～年，七级时间粒度

// 1. 解析cron表达式
expr, err := cronexpr.Parse("*/6 * * * * * *")  // 每6秒调度一次.
// 2. 计算下一次调度时间
nextTime := expr.Next(time.Now())
```