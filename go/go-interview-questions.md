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

```go
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

```go
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

```go
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

```go
答案:
第二个返回值没有命名。

解析:
在函数有多个返回值时，只要有一个返回值有命名，其他的也必须命名。如果有多个返回值必须加上括号()；如果只有一个返回值且命名也必须加上括号()。这里的第一个返回值有命名 sum，第二个没有命名，所以错误。
```

### 5. new()和make()的区别

```go
解答:
new(T) 和 make(T,args) 是 Go 语言内建函数，用来分配内存，但适用的类型不同。

new(T) 会为 T 类型的新值分配已置零的内存空间，并返回地址（指针），即类型为 *T 的值。换句话说就是，返回一个指针，该指针指向新分配的、类型为 T 的零值。适用于值类型，如数组、结构体等。

make(T,args) 返回初始化之后的 T 类型的值，这个值并不是 T 类型的零值，也不是指针 *T，是经过初始化之后的 T 的引用。make() 只适用于 slice、map 和 channel.

// new() 只能新建命名类型，比如基础数据类型、比如使用 type Name Typ 这样形式声明的命名类型，不能用于未命名类型，比如匿名结构体

```

### 6. 切片append

```go
func main() {
    list := new([]int)
    list = append(list, 1)
    fmt.Println(list)
}
```

能否通过编译?如果能，输出是什么?

```go
答案:
不能。new([]int) 返回切片指针，切片本身是引用类型，根据切片指针无法索引其内部元素，也就没办法使用append。
换言之，想要使用append，其参数一必须是切片本身。

// 可以使用 make() 初始化之后再用。同样的，map 和 channel 建议使用 make() 或字面量的方式初始化，不要用 new()

```

### 7. 切片append

```go
func main() {
    s1 := []int{1, 2, 3}
    s2 := []int{4, 5}
    s1 = append(s1, s2)
    fmt.Println(s1)
}
```

能否通过编译?如果能，输出是什么?

```go
答案：
不能。切片的append方法参数二不能是切片，必须是切片内元素类型，可以一次传多个。但必须写成如下形式之一：
1.  s1 = append(s1, s2...)
2.  s1 = append(s1, 4, 5)

// append() 的第二个参数不能直接使用 slice，需使用 … 操作符，将一个切片追加到另一个切片上：append(s1,s2…)。或者直接跟上元素，形如：append(s1,1,2,3)。

```

### 8. 短变量声明

```go
var(
    size := 1024
    max_size = size*2
)

func main() {
    fmt.Println(size,max_size)
}
```

能否通过编译?如果能，输出是什么?

```go
答案：
不能。短变量声明 size := 1024 只能用在函数或者方法内部，不能用于外部变量。

// 变量声明的简短模式，形如：x := 100。但这种声明方式有限制：
// 1. 必须使用显示初始化；
// 2. 不能提供数据类型，编译器会自动推导；
// 3. 只能在函数内部使用简短模式；

```

### 9. 结构体之 == 操作符

```go
func main() {
    sn1 := struct {
        age  int
        name string
    }{age: 11, name: "qq"}
    sn2 := struct {
        age  int
        name string
    }{age: 11, name: "qq"}

    if sn1 == sn2 {
        fmt.Println("sn1 == sn2")
    }

    sm1 := struct {
        age int
        m   map[string]string
    }{age: 11, m: map[string]string{"a": "1"}}
    sm2 := struct {
        age int
        m   map[string]string
    }{age: 11, m: map[string]string{"a": "1"}}

    if sm1 == sm2 {
        fmt.Println("sm1 == sm2")
    }
}
```

能否通过编译?如果能，输出是什么?

```go
答案：
不能。
sn1 == sn2 是可以比较是否相等的，因为其内部元素类型及内存分布完全一致。
但是 sm1 == sm2 是不被允许的，其内部元素 m 是 map，不能进行比较。
两个结构体之间能用 == 判断相等的前提是其内部元素类型都可比较，而且顺序完全一致。

// 参考答案及解析：编译不通过 invalid operation: sm1 == sm2

// 这道题目考的是结构体的比较，有几个需要注意的地方：

// 结构体只能比较是否相等，但是不能比较大小。
// 相同类型的结构体才能够进行比较，结构体是否相同不但与属性类型有关，还与属性顺序相关，sn3 与 sn1 就是不同的结构体；
// 1    sn3:= struct {
// 2        name string
// 3        age  int
// 4    }{age:11,name:"qq"}
// 如果 struct 的所有成员都可以比较，则该 struct 就可以通过 == 或 != 进行比较是否相等，比较时逐个项进行比较，如果每一项都相等，则两个结构体才相等，否则不相等；
// 那什么是可比较的呢，常见的有 bool、数值型、字符、指针、数组等，像切片、map、函数等是不能比较的。 具体可以参考 Go 说明文档。https://golang.org/ref/spec#Comparison_operators

```

### 10. 结构体指针访问内部成员

```go
1.通过指针变量 p 访问其成员变量 name，有哪几种方式？

A.p.name
B.(&p).name
C.(*p).name
D.p->name
```

```go
答案： A、C (应该没什么好解释的))

// & 取址运算符，* 指针解引用

```

### 11. 类型定义与类型别名

```go
type MyInt1 int
type MyInt2 = int

func main() {
    var i int =0
    var i1 MyInt1 = i
    var i2 MyInt2 = i
    fmt.Println(i1,i2)
}
```

能否通过编译？

```go
答案： 不能。
type MyInt2 = int 是类型别名，所以是可以将int类型的i赋给i2的；
type MyInt1 int 是类型定义，所以不可以将int类型的i赋给MyInt1类型的i2；

// 参考答案及解析：编译不通过，cannot use i (type int) as type MyInt1 in assignment。
// 这道题考的是类型别名与类型定义的区别。
// 第 5 行代码是基于类型 int 创建了新类型 MyInt1，第 6 行代码是创建了 int 的类型别名 MyInt2，注意类型别名的定义时 = 。所以，第 10 行代码相当于是将 int 类型的变量赋值给 MyInt1 类型的变量，Go 是强类型语言，编译当然不通过；而 MyInt2 只是 int 的别名，本质上还是 int，可以赋值。
// 第 10 行代码的赋值可以使用强制类型转化 var i1 MyInt1 = MyInt1(i).

```

### 12. 字符串拼接

```go
关于字符串连接，下面语法正确的是？

A. str := 'abc' + '123'
B. str := "abc" + "123"
C. str := '123' + "abc"
D. fmt.Sprintf("abc%d", 123)
```

```go
答案： B
单引号声明的字面量为byte类型，而双引号声明的字面量为string类型。二者不可以直接连接。
fmt.Sprintf("abc%d", 123)从字符串拼接角度倒是没问题，只是缺少返回结果的接收变量。

//参考答案及解析：BD。
//知识点：字符串连接。除了以上两种连接方式，还有 strings.Join()、buffer.WriteString()等。

```

### 13. 自增变量iota

```go
const (
    x = iota
    _
    y
    z = "zz"
    k
    p = iota
)

func main()  {
    fmt.Println(x,y,z,k,p)
}
```

能否通过编译？若能，输出什么？

```go
答案：
能。

输出结果：
0， 2， zz, 4, 5

// 参考答案及解析：编译通过，输出：0 2 zz zz 5。
// 知识点：iota 的使用。给大家贴篇文章，讲的很详细 https://www.cnblogs.com/zsy/p/5370052.html

错误反思:
const() 内定义的常量，若不明确指出其常量值，则默认为紧邻的上一个常量的值。这就是为什么正确答案中 k 的值也是 zz 了。
const() 常量组中提供了 iota 这种自增常量。 使得紧邻着的下一个的值中的iota值增1。
比如这里 x = iota ,因为是const后第一次出现，所以值为0.
接下来第二个使用 _ 忽略的值就是1， 而再下一个， y 的值就是2.
中间可以使用具体指明的值来覆盖这个自增产生的默认规律，比如 z = "zz" 所带来的默认规律 （后边的常量默认与之相同） 覆盖了 iota带来的这种规律，所以 k 仍是 "zz" ！
在 p = iota 处，相当于又把iota的自增规律拉起来了。（想象下图片处理的”图层“概念）
除非是在另一个常量组中，否则只要是在一个 const() 中，iota的增长就一直存在，可以被覆盖，但不会被重置。

iota的高级一点的用法，用在常量表达式中。
const (
    a = 1 << iota   // a = 1
    b               // b继承了这个表达式（默认，除非自己指定），但iota值自增了，所以 b = 2
    c               // c = 4
)

```

### 14. 变量初始化为nil

```go
下面赋值正确的是()

A. var x = nil
B. var x interface{} = nil
C. var x string = nil
D. var x error = nil
```

```go
答案： B、D

// 参考答案及解析：BD。知识点：nil 值。nil 只能赋值给指针、chan、func、interface、map 或 slice 类型的变量。强调下 D 选项的 error 类型，它是一种内置接口类型，看下方贴出的源码就知道，所以 D 是对的。
// type error interface {
//     Error() string
// }

```