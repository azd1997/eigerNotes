---
title: "Go面试每日一题汇总"
date: 2019-10-29T09:30:35+08:00
draft: false
categories: ["go"]
tags: ["go", "面试"]
keywords: ["面试题", "go"]
---

## 声明

本篇所收集题目来自《Golang来啦》公众号，仅作学习之用！
<https://mp.weixin.qq.com/s/rEXhrAqEOg9Ja4wYomOsGw>

## Go面试题汇总

### 1. defer后进先出

```go
func main() {
    defer_call()
}

func defer_call() {
    defer func() { fmt.Println("打印前") }()
    defer func() { fmt.Println("打印中") }()
    defer func() { fmt.Println("打印后") }()

    panic("触发异常")
}
```

输出结果是?

```shell
解答:
panic时defer后进先出，都defer完了才 panic：触发异常。
```

### 2. for-range迭代变量复用

```go
func main() {

    slice := []int{0,1,2,3}
    m := make(map[int]*int)

    for key,val := range slice {
        m[key] = &val
    }

    for k,v := range m {
        fmt.Println(k,"->",*v)
    }
}
```

输出结果是？

```shell
解答：
由于for k, v := range slice/array/map 遍历时 k, v 其实是两个被复用的变量，其在遍历过程中地址并不更改，所以上面代码中指针引用 val 其会导致最终 m 中全是 3.

解决办法:
在循环体内每次将 val 赋值给另一个变量。 p ：= val; m[key] = &p
```

### 3. 切片初始化与append

```go
// 1.
func main() {
    s := make([]int, 5)
    s = append(s, 1, 2, 3)
    fmt.Println(s)
}

// 2.
func main() {
    s := make([]int,0)
    s = append(s,1,2,3,4)
    fmt.Println(s)
}
```

这两段代码输出?

```shell
输出:
//1. [0, 0, 0, 0, 0, 1, 2, 3]
//2. [1, 2, 3, 4]

解析:
复合数据结构初始化时其内部元素为各自零值。
```

### 4. 命名返回值

```go
func funcMui(x,y int)(sum int,error){
    return x+y,nil
}
```

指出代码缺陷。

```shell
答案:
第二个返回值没有命名。

解析:
在函数有多个返回值时，只要有一个返回值有命名，其他的也必须命名。如果有多个返回值必须加上括号()；如果只有一个返回值且命名也必须加上括号()。这里的第一个返回值有命名 sum，第二个没有命名，所以错误。
```

### 5. new()和make()的区别

```shell
解答:
new(T) 和 make(T,args) 是 Go 语言内建函数，用来分配内存，但适用的类型不同。

new(T) 会为 T 类型的新值分配已置零的内存空间，并返回地址（指针），即类型为 *T 的值。换句话说就是，返回一个指针，该指针指向新分配的、类型为 T 的零值。适用于值类型，如数组、结构体等。

make(T,args) 返回初始化之后的 T 类型的值，这个值并不是 T 类型的零值，也不是指针 *T，是经过初始化之后的 T 的引用。make() 只适用于 slice、map 和 channel.

#new() 只能新建命名类型，比如基础数据类型、比如使用 type Name Typ 这样形式声明的命名类型，不能用于未命名类型，比如匿名结构体
#make()
```
