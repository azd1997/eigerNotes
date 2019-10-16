---
title: "pflag简介"
date: 2019-10-16T03:15:39+08:00
draft: false
categories: ["go"]
tags: ["pflag"]
keywords: ["pflag"]
---


## 官方文档

- https://github.com/spf13/pflag/blob/master/README.md

## 使用

导入：

``` go
import flag "github.com/spf13/pflag"
```

Pflag和官方Flag大部分用法都是相同且兼容的。唯一注意的一点是，如果直接实例化Flag结构体，那么会没有flag的短名称，需要额外设置。

那么通常我们会采用 String(), BoolVar(), Var()等来实例化Flag

例如：

``` go
var ip *int = flag.Int("flagname", 1234, "help message for flagname")
```

也可以使用`Var()`函数将flag绑定到一个变量（就是会将命令行给定的flag值赋到程序运行时的一个变量中）：

``` go
var flagvar int
func init() {
    flag.IntVar(&flagvar, "flagname", 1234, "help message for flagname")
}
```

也可以绑定到自定义变量中（上面这句是要指定选择哪种数据类型对应的`Var()`）

``` go
flag.Var(&flagVal, "name", "help message for flagname")
```

这样的做法就是不考虑命令行参数输入成啥样，丢给flagVar再去处理。

**谨记！这些flag默认值为绑定的变量的初始值**

在所有flag定义好之后，使用
``` go
flag.Parse()
```
解析参数（解析后就可以使用）。

在使用时，flag是指针形式。而对应的变量则是值形式
``` go
fmt.Println("ip has value ", *ip)
fmt.Println("flagvar has value ", flagvar)
```

访问FlagSet参数集时如果难以记住所有参数指针，有下面这个函数：
```go
i, err := flagset.GetInt("flagname")
```

解析之后flag参数是切片形式。访问可以使用`flag.Args()` 或者`flag.Arg(i)`，下标索引范围0~` flag.NArg()-1`

pfalg包也定义了一些官方flag没有的函数，如：参数名的短名(-p)。
如何带有短名？使用带有“p”的函数：
```go
var ip = flag.IntP("flagname", "f", 1234, "help message")
var flagvar bool
func init() {
	flag.BoolVarP(&flagvar, "boolname", "b", true, "help message")
}
flag.VarP(&flagVal, "varname", "v", "help message")
```

### 为flag设置无选项默认值

创建flag之后可以设置其无选项的默认值` pflag.NoOptDefVal`，使用方法是：

```go
var ip = flag.IntP("flagname", "f", 1234, "help message")
flag.Lookup("flagname").NoOptDefVal = "4321"
```

这样做的结果是：

| Parsed Arguments | Resulting Value |
| -------------    | -------------   |
| --flagname=1357  | ip=1357         |
| **--flagname**   | ip=4321         |
| [nothing]        | ip=1234         |


### 命令行flag语法

```
--flag    // boolean flags, or flags with no option default values
--flag x  // only on flags without a default value
--flag=x
```

与官方flag包不同的是，选项前的单横线“-”和双横线“--”意义不同，单横代表短名，双横代表长名。
但是注意：**最后一个的短名字母代表的必须是布尔值flag或者是带有默认值的flag**


```
// boolean or flags where the 'no option default value' is set
-f
-f=true
-abc
but
-b true is INVALID

// non-boolean and flags without a 'no option default value'
-n 1234
-n=1234
-n1234

// mixed
-abcs "hello"
-absd="hello"
-abcs1234
```
