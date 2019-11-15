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

### 15. init函数

```go
关于init函数，下面说法正确的是()

A. 一个包中，可以包含多个 init 函数；
B. 程序编译时，先执行依赖包的 init 函数，再执行 main 包内的 init 函数；
C. main 包中，不能有 init 函数；
D. init 函数可以被其他函数调用；
```

```go
答案：
A

// 参考答案及解析：AB。关于 init() 函数有几个需要注意的地方：

// init() 函数是用于程序执行前做包的初始化的函数，比如初始化包里的变量等;
// 一个包可以出线多个 init() 函数,一个源文件也可以包含多个 init() 函数；
// 同一个包中多个 init() 函数的执行顺序没有明确定义，但是不同包的init函数是根据包导入的依赖关系决定的（看下图）;
// init() 函数在代码中不能被显示调用、不能被引用（赋值给函数变量），否则出现编译错误;
// 一个包被引用多次，如 A import B,C import B,A import C，B 被引用多次，但 B 包只会初始化一次；
// 引入包，不可出现死循坏。即 A import B,B import A，这种情况编译失败；
```

![go-interview-questions_15](/images/go-interview-questions_15.jpg)
<!--![go-interview-questions_15](../../../static/images/go-interview-questions_15.png)-->

### 16. 函数变量是指向函数的指针

```go
下面这段代码输出什么以及原因？

func hello() []string {
    return nil
}

func main() {
    h := hello
    if h == nil {
        fmt.Println("nil")
    } else {
        fmt.Println("not nil")
    }
}

A. nil
B. not nil
C. compilation error
```

```go
答案：
B。h是hello函数的一个指针

// 答案及解析：B。这道题目里面，是将 hello() 赋值给变量 h，而不是函数的返回值，所以输出 not nil
```

### 17. 类型分支仅针对接口类型使用

```go
下面这段代码能否编译通过？如果可以，输出什么？

func GetValue() int {
    return 1
}

func main() {
    i := GetValue()
    switch i.(type) {
    case int:
        println("int")
    case string:
        println("string")
    case interface{}:
        println("interface")
    default:
        println("unknown")
    }
}
```

```go
答案：
不能，类型分支是给接口用的

//参考答案及解析：编译失败。考点：类型选择，类型选择的语法形如：i.(type)，其中 i 是接口，type 是固定关键字，需要注意的是，只有接口类型才可以使用类型选择。看下关于接口的文章。
```

### 18. channel

```go
关于channel，下面语法正确的是()

A. var ch chan int
B. ch := make(chan int)
C. <- ch
D. ch <-
```

```go
答案：
ABC

// 参考答案及解析：ABC。A、B都是声明 channel；C 读取 channel；写 channel 是必须带上值，所以 D 错误。
```

### 19. 空map也可以读

```go
下面这段代码输出什么？

A.0
B.1
C.Compilation error

type person struct {
    name string
}

func main() {
    var m map[person]int
    p := person{"mike"}
    fmt.Println(m[p])
}
```

```go
答案：
C。声明了map但是没有初始化，这样使用 m[p]会造成 panic: 空指针调用


// 参考答案及解析：A。打印一个 map 中不存在的值时，返回元素类型的零值。这个例子中，m 的类型是 map[person]int，因为 m 中不存在 p，所以打印 int 类型的零值，即 0。
```

### 20. 可变函数入参slice...可以改变该切片

```go
下面这段代码输出什么？

A.18
B.5
C.Compilation error

func hello(num ...int) {
    num[0] = 18
}

func main() {
    i := []int{5, 6, 7}
    hello(i...)
    fmt.Println(i[0])
}
```

```go
答案：
B。 实参是值传递，所以 num 只是由 i 的元素的副本构建成的一个切片，修改 num[0] 不会对 i 造成任何影响

// 参考答案及解析：18。知识点：可变函数。https://mp.weixin.qq.com/s/aOaNkCKudeI7fArogzpCsw
// 可变函数最后一个入参如果是切片并使用了...，切片本身会作为参数直接传入，不会再创建一个新的切片。这样，在函数内对这个入参的操作会影响这个切片
```

### 21. 不同类型数值变量不能直接相加

```go
下面这段代码输出什么？

func main() {
    a := 5
    b := 8.1
    fmt.Println(a + b)
}
A.13.1
B.13
C.compilation error
```

```go
答案：
C。 不能直接加，a 是int, b 是float64, 要显式转换类型.

// 参考答案及解析：C。a 的类型是 int，b 的类型是 float，两个不同类型的数值不能相加，编译报错。

```

### 22. 基于切片构造切片a[i:j:k]

```go
下面这段代码输出什么？

package main

import (
    "fmt"
)

func main() {
    a := [5]int{1, 2, 3, 4, 5}
    t := a[3:4:4]
    fmt.Println(t[0])
}
A.3
B.4
C.compilation error
```

```go
答案：
C。  t := a[3:4:4]， 第二个选项是间隔。

// 参考答案及解析：B。知识点：操作符 [i,j]。基于数组（切片）可以使用操作符 [i,j] 创建新的切片，从索引 i，到索引 j 结束，截取已有数组（切片）的任意部分，返回新的切片，新切片的值包含原数组（切片）的 i 索引的值，但是不包含 j 索引的值。i、j 都是可选的，i 如果省略，默认是 0，j 如果省略，默认是原数组（切片）的长度。i、j 都不能超过这个长度值。

// 假如底层数组的大小为 k，截取之后获得的切片的长度和容量的计算方法：长度：j-i，容量：k-i。

// 截取操作符还可以有第三个参数，形如 [i,j,k]，第三个参数 k 用来限制新切片的容量，但不能超过原数组（切片）的底层数组大小。截取获得的切片的长度和容量分别是：j-i、k-i。

// 所以例子中，切片 t 为 [4]，长度和容量都是 1。

// 详细的知识点可以看下关于切片的文章。
```

### 23. 不同长度数组类型不同

```go
下面这段代码输出什么？

func main() {
    a := [2]int{5, 6}
    b := [3]int{5, 6}
    if a == b {
        fmt.Println("equal")
    } else {
        fmt.Println("not equal")
    }
}
A. compilation error
B. equal
C. not equal
```

```go
答案：
A。 类型不同不可比较，使用 a == b 触发panic

// 参考答案及解析：A。Go 中的数组是值类型，可比较，另外一方面，数组的长度也是数组类型的组成部分，所以 a 和 b 是不同的类型，是不能比较的，所以编译错误。
```

### 24. map没有cap操作

```go
关于 cap() 函数的适用类型，下面说法正确的是()

A. array
B. slice
C. map
D. channel
```

```go
答案：
ABCD

//参考答案及解析：ABD。知识点：cap()，cap() 函数不适用 map。
```

### 25. 空接口动态类型与值都为nil

```go
下面这段代码输出什么？

func main() {
    var i interface{}
    if i == nil {
        fmt.Println("nil")
        return
    }
    fmt.Println("not nil")
}

A. nil
B. not nil
C. compilation error
```

```go
答案：
B。空接口不是nil， 但可以和 nil 作 == 比较

//参考答案及解析：A。当且仅当接口的动态值和动态类型都为 nil 时，接口类型值才为 nil。
```

### 26. map删除不存在键不报错

```go
下面这段代码输出什么？

func main() {
    s := make(map[string]int)
    delete(s, "h")
    fmt.Println(s["h"])
}

A. runtime panic
B. 0
C. compilation error
```

```go
答案：
C。此时还是空map,删除一个不存在的键会panic。

//参考答案及解析：B。删除 map 不存在的键值对时，不会报错，相当于没有任何作用；获取不存在的减值对时，返回值类型对应的零值，所以返回 0。
```

### 27. 25个关键字

```go
1.下面属于关键字的是（）

A.func
B.struct
C.class
D.defer
```

```go
答案：
C

看反题了。。。
// 参考答案及解析：ABD。知识点：Go 语言的关键字。Go 语言有 25 个关键字，看下图：
```

![go-interview-questions_27](/images/go-interview-questions_27.jpg)
<!--![go-interview-questions_27](../../../static/images/go-interview-questions_27.png)-->

### 28. Printf之verb

```go
下面这段代码输出什么？

func main() {
    i := -5
    j := +5
    fmt.Printf("%+d %+d", i, j)
}

A. -5 +5
B. +5 +5
C. 0  0
```

```go
答案：
%+d 是什么动词？ 猜B

// 参考答案及解析：A。%d表示输出十进制数字，+表示输出数值的符号。这里不表示取反。
```

### 29. 结构体嵌套之同名方法屏蔽

```go
下面这段代码输出什么？

type People struct{}

func (p *People) ShowA() {
    fmt.Println("showA")
    p.ShowB()
}
func (p *People) ShowB() {
    fmt.Println("showB")
}

type Teacher struct {
    People
}

func (t *Teacher) ShowB() {
    fmt.Println("teacher showB")
}

func main() {
    t := Teacher{}
    t.ShowB()
}
```

```go
答案：
"teacher showB"

// 参考答案及解析：teacher showB。知识点：结构体嵌套。
// 在嵌套结构体中，People 称为内部类型，Teacher 称为外部类型；
// 通过嵌套，内部类型的属性、方法，可以为外部类型所有，就好像是外部类型自己的一样。
// 此外，外部类型还可以定义自己的属性和方法，甚至可以定义与内部相同的方法，这样内部类型的方法就会被“屏蔽”。// 这个例子中的 ShowB() 就是同名方法。
```

### 30. 全局字符变量定义

```go
定义一个包内全局字符串变量，下面语法正确的是（）

A. var str string
B. str := ""
C. str = ""
D. var str = ""
```

```go
答案：
AD

//参考答案及解析：AD。B 只支持局部变量声明；C 是赋值，str 必须在这之前已经声明
```

### 31. defer函数注册时入参值传递

```go
下面这段代码输出什么?

func hello(i int) {
    fmt.Println(i)
}
func main() {
    i := 5
    defer hello(i)
    i = i + 10
}
```

```go
答案：
5
//参考答案及解析：5。这个例子中，hello() 函数的参数在执行 defer 语句的时候会保存一份副本，在实际调用 hello() 函数时用，所以是 5
```

### 32. 结构体嵌套之内部方法

```go
下面这段代码输出什么？

type People struct{}

func (p *People) ShowA() {
    fmt.Println("showA")
    p.ShowB()
}
func (p *People) ShowB() {
    fmt.Println("showB")
}

type Teacher struct {
    People
}

func (t *Teacher) ShowB() {
    fmt.Println("teacher showB")
}

func main() {
    t := Teacher{}
    t.ShowA()
}
```

```go
答案：
showA
showB

//知识点：结构体嵌套。这道题可以结合第 12 天的第三题一起看，Teacher 没有自己 ShowA()，所以调用内部类型 People 的同名方法，需要注意的是第 5 行代码调用的是 People 自己的 ShowB 方法
```

### 33. 字符串不可修改

```go
func main() {
    str := "hello"
    str[0] = 'x'
    fmt.Println(str)
}

A. hello
B. xello
C. compilation error
```

```go
答案：
C。字符串变量一经定义不可修改

//参考代码及解析：C。知识点：常量，Go 语言中的字符串是只读的
```

### 34. 指针操作

```go
下面代码输出什么？

func incr(p *int) int {
    *p++
    return *p
}

func main() {
    p :=1
    incr(&p)
    fmt.Println(p)
}

A. 1
B. 2
C. 3
```

```go
答案：
B。incr()函数已将该值修改

//参考答案及解析：B。知识点：指针，incr() 函数里的 p 是 *int 类型的指针，指向的是 main() 函数的变量 p 的地址。第 2 行代码是将该地址的值执行一个自增操作，incr() 返回自增后的结果。
```

### 35. 可变函数

```go
对 add() 函数调用正确的是（）

func add(args ...int) int {

    sum := 0
    for _, arg := range args {
        sum += arg
    }
    return sum
}

A. add(1, 2)
B. add(1, 3, 7)
C. add([]int{1, 2})
D. add([]int{1, 3, 7}…)
```

```go
答案：
ABD

//参考答案及解析：ABD。知识点：可变函数
```

### 36. 切片的nil值

```go
下面代码下划线处可以填入哪个选项使得程序走yes分支？

func main() {
    var s1 []int
    var s2 = []int{}
    if __ == nil {
        fmt.Println("yes nil")
    }else{
        fmt.Println("no nil")
    }
}

A. s1
B. s2
C. s1、s2 都可以
```

```go
答案：
A

//参考答案及解析：A。
//知识点：nil 切片和空切片。nil 切片和 nil 相等，一般用来表示一个不存在的切片；
//空切片和 nil 不相等，表示一个空的集合。
```

### 37. 整数直接转换为字符串

```go
下面这段代码输出什么？

func main() {
    i := 65
    fmt.Println(string(i))
}

A. A
B. 65
C. compilation error
```

```go
答案：
A。直接转换成string，是将整数的bit表示转换为byte表示，再得到相应的单字符字符串。
想要得到字符串“65”，使用 strconv.ItoA(int)

//参考答案及解析：A。UTF-8 编码中，十进制数字 65 对应的符号是 A
```

### 38. 接口变量方法调用

```go
下面这段代码输出什么？

type A interface {
    ShowA() int
}

type B interface {
    ShowB() int
}

type Work struct {
    i int
}

func (w Work) ShowA() int {
    return w.i + 10
}

func (w Work) ShowB() int {
    return w.i + 20
}

func main() {
    c := Work{3}
    var a A = c
    var b B = c
    fmt.Println(a.ShowA())
    fmt.Println(b.ShowB())
}
```

```go
答案：
13
23

//参考答案及解析：13 23。
//知识点：接口。一种类型实现多个接口，结构体 Work 分别实现了接口 A、B，所以接口变量 a、b 调用各自的方法 ShowA() 和 ShowB()，输出 13、23。
```

### 39. 切片容量

```go
切片 a、b、c 的长度和容量分别是多少？

func main() {

    s := [3]int{1, 2, 3}
    a := s[:0]
    b := s[:2]
    c := s[1:2:cap(s)]
}
```

```go
答案：
0，0
2，2
1，3

//参考答案及解析：a、b、c 的长度和容量分别是 0 3、2 3、1 2。
//知识点：数组或切片的截取操作。
//截取操作有带 2 个或者 3 个参数，形如：[i:j] 和 [i:j:k]，
//假设截取对象的底层数组长度为 l。
//在操作符 [i:j] 中，如果 i 省略，默认 0，如果 j 省略，默认底层数组的长度，
//截取得到的切片长度和容量计算方法是 j-i、l-i。
//操作符 [i:j:k]，k 主要是用来限制切片的容量，但是不能大于数组的长度 l，
//截取得到的切片长度和容量计算方法是 j-i、k-i。
```

### 40. map初始化与安全读

```go
下面代码中 A B 两处应该怎么修改才能顺利编译？

func main() {
    var m map[string]int        //A
    m["a"] = 1
    if v := m["b"]; v != nil {  //B
        fmt.Println(v)
    }
}
```

```go
答案：
A.  m := make(map[string]int)
B.  if v, ok := m["b"]; ok {

//在 A 处只声明了map m ,并没有分配内存空间，不能直接赋值，需要使用 make()，都提倡使用 make() 或者字面量的方式直接初始化 map。
//B 处，v,k := m["b"] 当 key 为 b 的元素不存在的时候，v 会返回值类型对应的零值，k 返回 false。
```

### 41. 接口的静态类型

```go
下面代码输出什么？

type A interface {
    ShowA() int
}

type B interface {
    ShowB() int
}

type Work struct {
    i int
}

func (w Work) ShowA() int {
    return w.i + 10
}

func (w Work) ShowB() int {
    return w.i + 20
}

func main() {
    c := Work{3}
    var a A = c
    var b B = c
    fmt.Println(a.ShowB())
    fmt.Println(b.ShowA())
}

A. 23 13
B. compilation error
```

```go
答案：
B

//参考答案及解析：B。知识点：接口的静态类型。/
//a、b 具有相同的动态类型和动态值，分别是结构体 work 和 {3}；
//a 的静态类型是 A，b 的静态类型是 B，
//接口 A 不包括方法 ShowB()，接口 B 也不包括方法 ShowA()，
//编译报错。看下编译错误：

//a.ShowB undefined (type A has no field or method ShowB)
//b.ShowA undefined (type B has no field or method ShowA)
```

### 42. 变量声明

```go
下面代码中，x 已声明，y 没有声明，判断每条语句的对错。

1. x, _ := f()
2. x, _ = f()
3. x, y := f()
4. x, y = f()
```

```go
答案：
14错，其余对

//参考答案及解析：错、对、对、错。知识点：变量的声明。1.错，x 已经声明，不能使用 :=；2.对；3.对，当多值赋值时，:= 左边的变量无论声明与否都可以；4.错，y 没有声明。
```

### 43*. defer与函数返回值

```go
下面代码输出什么？

func increaseA() int {
    var i int
    defer func() {
        i++
    }()
    return i
}

func increaseB() (r int) {
    defer func() {
        r++
    }()
    return r
}

func main() {
    fmt.Println(increaseA())
    fmt.Println(increaseB())
}

A. 1 1
B. 0 1
C. 1 0
D. 0 0
```

```go
答案：
A

//参考答案及解析：B。知识点：defer、返回值。
//注意一下，increaseA() 的返回参数是匿名，increaseB() 是具名。

该题与题45、46一起看。

相关文章: https://mp.weixin.qq.com/s/Hm8MdrqYgCQPQ4A1nrv4sw

defer 是 Go 语言提供的一种用于注册延迟调用的机制，
每一次 defer 都会把函数压入栈中，当前函数返回前再把延迟函数取出并执行。

defer 语句并不会马上执行，而是会进入一个栈，
函数 return 前，会按先进后出（FILO）的顺序执行。
也就是说最先被定义的 defer 语句最后执行。
先进后出的原因是后面定义的函数可能会依赖前面的资源，自然要先执行；
否则，如果前面先执行，那后面函数的依赖就没有了。

使用 defer 最容易采坑的地方是和带命名返回参数的函数一起使用时。
defer 语句定义时，对外部变量的引用是有两种方式的，分别是作为函数参数和作为闭包引用。
作为函数参数，则在 defer 定义时就把值传递给 defer，并被缓存起来；
作为闭包引用的话，则会在 defer 函数真正调用时根据整个上下文确定当前的值。

避免掉坑的关键是要理解这条语句：

return xxx
这条语句并不是一个原子指令，经过编译之后，变成了三条指令：

1. 返回值 = xxx
2. 调用 defer 函数
3. 空的 return

1,3 步才是 return 语句真正的命令，第 2 步是 defer 定义的语句，这里就有可能会操作返回值。

所以，第二小题的实际执行代码可描述为：
func increaseB() (r int) {
    // 1.赋值
    r = 0
    // 2.闭包引用，r++
    defer func() {
        r++
    }()
    // 3.空 return
    return
}

第一小题中函数 increaseA() 是匿名返回值，返回局部变量，同时 defer 函数也会操作这个局部变量。
对于匿名返回值来说，可以假定有一个变量存储返回值，
比如假定返回值变量为 anony，上面的返回语句可以拆分成以下过程：
annoy = i
i++
return
由于 i 是整型，会将值拷贝给 anony，所以 defer 语句中修改 i 值，对函数返回值不造成影响，所以返回 0 。
```

### 44. 类型断言

```go
下面代码输出什么？

type A interface {
    ShowA() int
}

type B interface {
    ShowB() int
}

type Work struct {
    i int
}

func (w Work) ShowA() int {
    return w.i + 10
}

func (w Work) ShowB() int {
    return w.i + 20
}

func main() {
    var a A = Work{3}
    s := a.(Work)
    fmt.Println(s.ShowA())
    fmt.Println(s.ShowB())
}

A. 13 23
B. compilation error
```

```go
答案：
A
//参考答案及解析：A。知识点：类型断言。
```

### 45*. defer与返回值

```go
f1()、f2()、f3() 函数分别返回什么？

func f1() (r int) {
    defer func() {
        r++
    }()
    return 0
}

func f2() (r int) {
    t := 5
    defer func() {
        t = t + 5
    }()
    return t
}

func f3() (r int) {
    defer func(r int) {
        r = r + 5
    }(r)
    return 1
}
```

```go
答案：
0， 5， 6

// 正确答案: 1， 5， 1

反思:
将三个函数的实际执行重写如下:

1.
func f1() (r int) {     // 具名变量返回
    r = 0
    defer func() {
        r++     // 闭包引用外部变量r，r值修改为1
    }()
    return      // 空return，仅表示退出函数
}

2.
func f2() (r int) {    // 具名变量返回
    t := 5
    r = t
    defer func() {
        t = t + 5   // t的变化和r无关了
    }()
    return
}

3.
func f3() (r int) {   // 具名变量返回
    r = 1
    defer func(r int) {
        r = r + 5       // r作为defer函数参数传入，仅拷贝值，外部r不受defer影响
    }(r)
    return
}
```

### 46*. defer与返回值

```go
下面代码段输出什么？

type Person struct {
    age int
}

func main() {
    person := &Person{28}

    // 1.
    defer fmt.Println(person.age)

    // 2.
    defer func(p *Person) {
        fmt.Println(p.age)
    }(person)

    // 3.
    defer func() {
        fmt.Println(person.age)
    }()

    person.age = 29
}
```

```go
答案：
3. 29
2. 29
1. 28

// 答案是 29  29  28
// 首先定义的局部变量person类型是一个指针
// 其次defer是先进后出结构，故defer执行顺序为3  2  1
// 3是匿名函数使用外部对象，而对象是指针，又在defer中，执行优先级为最低，故最外层代码修改以后，defer则会使用修改后的对象，故29
// 2是函数传递参数进去，因为是指针，同3
// 1看是一条语句，其实可以写成defer func(age int){fmt.Println(age)}(person.age) 因为传递的是执行到此初始person对象的age值，是而在此时age为28因为是值类型传递，所以输出为28

// 参考答案及解析：29 29 28。变量 person 是一个指针变量 。
// 1.person.age 此时是将 28 当做 defer 函数的参数，会把 28 缓存在栈中，等到最后执行该 defer 语句的时候取出，即输出 28；
// 2.defer 缓存的是结构体 Person{28} 的地址，最终 Person{28} 的 age 被重新赋值为 29，所以 defer 语句最后执行的时候，依靠缓存的地址取出的 age 便是 29，即输出 29；
// 3.闭包引用，输出 29；
// 又由于 defer 的执行顺序为先进后出，即 3 2 1，所以输出 29 29 28。

反思：
defer确实会注册当前状态，但一定要注意defer内使用的是指针还是值传递，若是指针传递，则会随程序进行而修改，若是值传递，则值保持当时不变。

本题与题48对比
```

### 47. defer

```go
下面这段代码正确的输出是什么？

func f() {
    defer fmt.Println("D")
    fmt.Println("F")
}

func main() {
    f()
    fmt.Println("M")
}

A. F M D
B. D F M
C. F D M
```

```go
答案：
C

//参考答案及解析：C。被调用函数里的 defer 语句在返回之前就会被执行，所以输出顺序是 F D M。
```

### 48*. defer与返回值

```go
下面代码输出什么？

type Person struct {
    age int
}

func main() {
    person := &Person{28}

    // 1.
    defer fmt.Println(person.age)

    // 2.
    defer func(p *Person) {
        fmt.Println(p.age)
    }(person)

    // 3.
    defer func() {
        fmt.Println(person.age)
    }()

    person = &Person{29}
}
```

```go
答案：
注意和题46的区别在于最后person的指针变了
3. 28
2. 28
1. 28

// 参考答案及解析：29 28 28。这道题在第 19 天题目的基础上做了一点点小改动，前一题最后一行代码 person.age = 29 是修改引用对象的成员 age，这题最后一行代码 person = &Person{29} 是修改引用对象本身，来看看有什么区别。

// 1处.person.age 这一行代码跟之前含义是一样的，此时是将 28 当做 defer 函数的参数，会把 28 缓存在栈中，等到最后执行该 defer 语句的时候取出，即输出 28；

// 2处.defer 缓存的是结构体 Person{28} 的地址，这个地址指向的结构体没有被改变，最后 defer 语句后面的函数执行的时候取出仍是 28；

// 3处.闭包引用，person 的值已经被改变，指向结构体 Person{29}，所以输出 29.

// 由于 defer 的执行顺序为先进后出，即 3 2 1，所以输出 29 28 28。
```

### 49. 切片声明

```go
下面的两个切片声明中有什么区别？哪个更可取？

A. var a []int
B. a := []int{}
```

```go
答案：
A是变量声明，B是声明并初始化为[]int{}。
从值来讲，A中 a=nil; B中 a=[]int{}。
建议使用B，很多时候可以避免自己忘了赋值而产生空指针调用，使用起来更安全。
但是如果是想用 if a == nil {} 这样的方式来判断切片是否初始化，也是可以的。

//A 声明的是 nil 切片；B 声明的是长度和容量都为 0 的空切片。
//第一种切片声明不会分配内存，优先选择。
```

### 50. 函数传参

```go
A、B、C、D 哪些选项有语法错误？

type S struct {
}

func f(x interface{}) {
}

func g(x *interface{}) {
}

func main() {
    s := S{}
    p := &s
    f(s) //A
    g(s) //B
    f(p) //C
    g(p) //D
}
```

```go
答案：
B。s为结构体变量，不能当做空接口指针传入 g()

//参考答案及解析：BD。
//函数参数为 interface{} 时可以接收任何类型的参数，包括用户自定义类型等，即使是接收指针类型也用 interface{}，而不是使用 *interface{}。
//永远不要使用一个指针指向一个接口类型，因为它已经是一个指针。
```

### 51. 函数返回值

```go
下面 A、B 两处应该填入什么代码，才能确保顺利打印出结果？

type S struct {
    m string
}

func f() *S {
    return __  //A
}

func main() {
    p := __    //B
    fmt.Println(p.m) //print "foo"
}
```

```go
答案：
A. &S{"foo"}
B. f()

//参考答案及解析：
//A. &S{"foo"}
//B. *f() 或者 f()
//f() 函数返回参数是指针类型，所以可以用 & 取结构体的指针；
//B 处，如果填 *f()，则 p 是 S 类型；如果填 f()，则 p 是 *S 类型，不过都可以使用 p.m 取得结构体的成员。
```

### 52. 字符串零值

```go
下面的代码有几处语法问题，各是什么？

package main
import (
    "fmt"
)
func main() {
    var x string = nil
    if x == nil {
        x = "default"
    }
    fmt.Println(x)
}
```

```go
答案：
var x string = nil; if x == nil. string的零值不是nil，不符合语法。本质来说，string是个包含有底层字节数组的结构体，不是指针类型，不能赋值为nil，也不能和nil比较

// 参考答案及解析：两个地方有语法问题。golang 的字符串类型是不能赋值 nil 的，也不能跟 nil 比较。
// 只有引用类型（map/channel/interface）可以和nil比较， 指针也可以
// 当然赋值的话，nil可以赋给切片
```

### 53. defer注册

```go
return 之后的 defer 语句会执行吗，下面这段代码输出什么？

var a bool = true
func main() {
    defer func(){
        fmt.Println("1")
    }()
    if a == true {
        fmt.Println("2")
        return
    }
    defer func(){
        fmt.Println("3")
    }()
}
```

```go
答案：
不会。后面那个defer都还没注册（压入栈）
输出为:
2
1

// 参考答案及解析：2 1。defer 关键字后面的函数或者方法想要执行必须先注册，return 之后的 defer 是不能注册的， 也就不能执行后面的函数或方法。
```

### 54. 自切片生成切片

```go
下面这段代码输出什么？为什么？

func main() {

    s1 := []int{1, 2, 3}
    s2 := s1[1:]
    s2[1] = 4
    fmt.Println(s1)
    s2 = append(s2, 5, 6, 7)
    fmt.Println(s1)
}
```

```go
答案：
[1,2,4]
[1,2,4]
s2和s1重叠的部分是共用底层内存的。程序结束时s2 = [2,4,5,6,7]

// 参考答案及解析：

// [1 2 4]
// [1 2 4]
// 我们知道，golang 中切片底层的数据结构是数组。当使用 s1[1:] 获得切片 s2，和 s1 共享同一个底层数组，这会导致 s2[1] = 4 语句影响 s1。
// 要清楚的是 s1 len=3,cap=3
// s2 = s1[1:]  len=3-1=2, cap = 3-1=2
// s2 append之后由于cap不足，新分配了一块内存，就不和s1共用底层内存了

// 而 append 操作会导致底层数组扩容，生成新的数组，因此追加数据后的 s2 不会影响 s1。

// 但是为什么对 s2 赋值后影响的却是 s1 的第三个元素呢？这是因为切片 s2 是从数组的第二个元素开始，s2 索引为 1 的元素对应的是 s1 索引为 2 的元素。
```

### 55. if -else条件语句

```go
下面选项正确的是？

func main() {
    if a := 1; false {
    } else if b := 2; false {
    } else {
        println(a, b)
    }
}
A. 1 2
B. compilation error

```

```go
答案：
A。 if ... else if ... else内局部变量共用

// 参考答案及解析：A。知识点：代码块和变量作用域。
```

### 56. map字面量与遍历

```go
下面这段代码输出什么？

func main() {
    m := map[int]string{0:"zero",1:"one"}
    for k,v := range m {
        fmt.Println(k,v)
    }
}
```

```go
答案：
0 zero
1 one
或者顺序反过来（因为无序遍历）

//参考答案及解析：

// 0 zero
// 1 one
// // 或者
// 1 one
// 0 zero
// map 的输出是无序的。
```

### 57. defer注册

```go
下面这段代码输出什么？

func main() {
    a := 1
    b := 2
    defer calc("1", a, calc("10", a, b))
    a = 0
    defer calc("2", a, calc("20", a, b))
    b = 1
}

func calc(index string, a, b int) int {
    ret := a + b
    fmt.Println(index, a, b, ret)
    return ret
}
```

```go
答案：
a,b都是值类型，这题没有涉及指针传参，所以函数都是值拷贝传入。需要注意的是defer有个入参是calc("10", a, b)和calc("20", a, b)要提前计算好。
所以defer1注册时为 calc("1", 1, 3), defer2注册时为 calc("2", 0, 2)
所以输出为：
10 1 2 3
20 0 2 2
2 0 2 2
1 1 3 4

//参考答案及解析：

// 10 1 2 3
// 20 0 2 2
// 2 0 2 2
// 1 1 3 4
// 程序执行到 main() 函数三行代码的时候，会先执行 calc() 函数的 b 参数，即：calc("10",a,b)，输出：10 1 2 3，得到值 3，因为
// defer 定义的函数是延迟函数，故 calc("1",1,3) 会被延迟执行；

// 程序执行到第五行的时候，同样先执行 calc("20",a,b) 输出：20 0 2 2 得到值 2，同样将 calc("2",0,2) 延迟执行；

// 程序执行到末尾的时候，按照栈先进后出的方式依次执行：calc("2",0,2)，calc("1",1,3)，则就依次输出：2 0 2 2，1 1 3 4。
```

### 58. 内置类型不能增加方法

```go
下面这段代码输出什么？为什么？

func (i int) PrintInt ()  {
    fmt.Println(i)
}

func main() {
    var i int = 1
    i.PrintInt()
}
A. 1
B. compilation error
```

```go
答案：
B。 不能对内置类型添加方法，如需添加方法，需要先对内置类型声明别名

//参考答案及解析：B。基于类型创建的方法必须定义在同一个包内，上面的代码基于 int 类型创建了 PrintInt() 方法，由于 int 类型和方法 PrintInt() 定义在不同的包内，所以编译出错。
//
//解决的办法可以定义一种新的类型：
//
// type Myint int
//
// func (i Myint) PrintInt ()  {
//     fmt.Println(i)
// }
//
// func main() {
//     var i Myint = 1
//     i.PrintInt()
// }
```

### 59. 值不能调用指针方法

```go
下面这段代码输出什么？为什么？

type People interface {
    Speak(string) string
}

type Student struct{}

func (stu *Student) Speak(think string) (talk string) {
    if think == "speak" {
        talk = "speak"
    } else {
        talk = "hi"
    }
    return
}

func main() {
    var peo People = Student{}
    think := "speak"
    fmt.Println(peo.Speak(think))
}
A. speak
B. compilation error
```

```go
答案：
B。实现方法的是指针，却使用结构体（值）去调用方法。（指针能调值方法，能调指针方法； 值能调值方法，但不能调指针方法）

//参考答案及解析：B。编译错误 Student does not implement People (Speak method has pointer receiver)，值类型 Student 没有实现接口的 Speak() 方法，而是指针类型 *Student 实现该方法。
```

### 60. iota的用法

```go
下面这段代码输出什么？

const (
    a = iota
    b = iota
)
const (
    name = "name"
    c    = iota
    d    = iota
)
func main() {
    fmt.Println(a)
    fmt.Println(b)
    fmt.Println(c)
    fmt.Println(d)
}
```

```go
答案：
0
1
0
1

//参考答案及解析：0 1 1 2。知识点：iota 的用法。

// iota 是 golang 语言的常量计数器，只能在常量的表达式中使用。

// iota 在 const 关键字出现时将被重置为0，const中每新增一行常量声明将使 iota 计数一次。

// 推荐阅读：
// https://studygolang.com/articles/2192

反思：
一个const组只要出现过1次iota，其值就是从第一行开始递增的！
```

### 61. 接口nil值

```go
下面这段代码输出什么？为什么？

type People interface {
    Show()
}

type Student struct{}

func (stu *Student) Show() {

}

func main() {

    var s *Student
    if s == nil {
        fmt.Println("s is nil")
    } else {
        fmt.Println("s is not nil")
    }
    var p People = s
    if p == nil {
        fmt.Println("p is nil")
    } else {
        fmt.Println("p is not nil")
    }
}

```

```go
答案：
s is nil
p is not nil
指针类型声明时为nil; 接口值只有当其动态类型和动态类型值均为nil时才为nil

//参考答案及解析：s is nil 和 p is not nil。这道题会不会有点诧异，我们分配给变量 p 的值明明是 nil，然而 p 却不是 nil。记住一点，当且仅当动态值和动态类型都为 nil 时，接口类型值才为 nil。上面的代码，给变量 p 赋值之后，p 的动态值是 nil，但是动态类型却是 *Student，是一个 nil 指针，所以相等条件不成立。
```

### 62. stringer接口

```go
下面这段代码输出什么？

type Direction int

const (
    North Direction = iota
    East
    South
    West
)

func (d Direction) String() string {
    return [...]string{"North", "East", "South", "West"}[d]
}

func main() {
    fmt.Println(South)
}

```

```go
答案：
South
fmt的Println等等输出打印方法会对于实现了stringer接口的类型，会调用其String方法来打印，而不是打印其值
所以这里fmt.Println(South)打印的是 South.String()的返回值，其返回值是内部那个数组的下表为south=2的那一项，也就是"South"

//参考答案及解析：South。知识点：iota 的用法、类型的 String() 方法。

//根据 iota 的用法推断出 South 的值是 2；另外，如果类型定义了 String() 方法，当使用 fmt.Printf()、fmt.Print() 和 fmt.Println() 会自动使用 String() 方法，实现字符串的打印。
```

### 63. map内value不可寻址

```go
下面代码输出什么？

type Math struct {
    x, y int
}

var m = map[string]Math{
    "foo": Math{2, 3},
}

func main() {
    m["foo"].x = 4
    fmt.Println(m["foo"].x)
}
A. 4
B. compilation error
```

```go
答案：
不太确定，B。 map获取到的v似乎是不可寻址的，也就不可以对其进行重赋值，也就是m["foo"].x=4报错

//参考答案及解析：B，编译报错 cannot assign to struct field m["foo"].x in map。错误原因：对于类似 X = Y的赋值操作，必须知道 X 的地址，才能够将 Y 的值赋给 X，但 go 中的 map 的 value 本身是不可寻址的。

有两个解决办法：

1.使用临时变量

type Math struct {
    x, y int
}

var m = map[string]Math{
    "foo": Math{2, 3},
}

func main() {
    tmp := m["foo"]
    tmp.x = 4
    m["foo"] = tmp
    fmt.Println(m["foo"].x)
}

2.修改数据结构

type Math struct {
    x, y int
}

var m = map[string]*Math{
    "foo": &Math{2, 3},
}

func main() {
    m["foo"].x = 4
    fmt.Println(m["foo"].x)
    fmt.Printf("%#v", m["foo"])   // %#v 格式化输出详细信息
}

```

### 64. 不同长度数组不可比较

```go
下面的代码有什么问题？

func main() {
    fmt.Println([...]int{1} == [2]int{1})
    fmt.Println([]int{1} == []int{1})
}
```

```go
答案：
编译错误。两个都不能比较，一个是[1]int和[2]int属于不同类型不能比较，另一个是[]int本身就不可比较

//参考答案及解析：有两处错误

// go 中不同类型是不能比较的，而数组长度是数组类型的一部分，所以 […]int{1} 和 [2]int{1} 是两种不同的类型，不能比较；
// 切片是不能比较的；
```

### 65*. 变量作用域

```go
一道很有代表性的题目，很多老司机都因此翻车！

下面这段代码输出什么？如果编译错误的话，为什么？

var p *int

func foo() (*int, error) {
    var i int = 5
    return &i, nil
}

func bar() {
    //use p
    fmt.Println(*p)
}

func main() {
    p, err := foo()
    if err != nil {
        fmt.Println(err)
        return
    }
    bar()
    fmt.Println(*p)
}
A. 5 5
B. runtime error

```

```go
答案：
不确定，怎么看都像是A选项。既然题目都说老司机也翻车，那估计是B了，不知道为什么

//参考答案及解析：B。知识点：变量作用域。问题出在操作符:=，对于使用:=定义的变量，如果新变量与同名已定义的变量不在同一个作用域中，那么 Go 会新定义这个变量。对于本例来说，main() 函数里的 p 是新定义的变量，会遮住全局变量 p，导致执行到bar()时程序，全局变量 p 依然还是 nil，程序随即 Crash。

正确的做法是将 main() 函数修改为：

func main() {
    var err error
    p, err = foo()
    if err != nil {
        fmt.Println(err)
        return
    }
    bar()
    fmt.Println(*p)
}
这道题目引自 Tony Bai 老师的一篇文章，原文讲的很详细，推荐。
https://tonybai.com/2015/01/13/a-hole-about-variable-scope-in-golang/
```

### 66*. for-range遍历切片

```go
下面这段代码能否正常结束？

func main() {
    v := []int{1, 2, 3}
    for i := range v {
        v = append(v, i)
    }
}

```

```go
答案：
无限循环，退不出来

//参考答案及解析：不会出现死循环，能正常结束。
循环次数在循环开始前就已经确定，循环内改变切片的长度，不影响循环次数。
```

### 67. for-range遍历重用变量

```go
下面这段代码输出什么？为什么？

func main() {

    var m = [...]int{1, 2, 3}

    for i, v := range m {
        go func() {
            fmt.Println(i, v)
        }()
    }

    time.Sleep(time.Second * 3)
}

```

```go
答案：
2 3
2 3
2 3

//参考答案及解析：

// 2 3
// 2 3
// 2 3

// 这道题其实应该说明 runtime.GOMAXPROCS(1) 单核运行。多核的话结果就不确定了
for range 使用短变量声明(:=)的形式迭代变量，需要注意的是，变量 i、v 在每次循环体中都会被重用，而不是重新声明。

各个 goroutine 中输出的 i、v 值都是 for range 循环结束后的 i、v 最终值，而不是各个goroutine启动时的i, v值。可以理解为闭包引用，使用的是上下文环境的值。

两种可行的 fix 方法:

1.使用函数传递

for i, v := range m {
    go func(i,v int) {
        fmt.Println(i, v)
    }(i,v)
}
2.使用临时变量保留当前值

for i, v := range m {
    i := i           // 这里的 := 会重新声明变量，而不是重用
    v := v
    go func() {
        fmt.Println(i, v)
    }()
}
引自：https://tonybai.com/2015/09/17/7-things-you-may-not-pay-attation-to-in-go/
```

### 68. defer机制

```go
下面这段代码输出什么？

func f(n int) (r int) {
    defer func() {
        r += n
        recover()
    }()

    var f func()

    defer f()
    f = func() {
        r += 2
    }
    return n + 1
}

func main() {
    fmt.Println(f(3))
}
```

```go
答案：
9. 两个defer，第一个defer注册的时候有用，再return之前会调用它使r = r + n；
第二个defer由于注册时的f()是函数指针调用的f()，紧接着给f赋值为匿名函数，所以这个也有用
最后return n+1 应该拆解为两步： r = n+1 ; return r
所以 r 的变化流程是： r(default 0) -> r=n+1=4 -> r+=2 = 6 -> r+=n =9

// 实验结果是 7. 应该是 第二个defer无效的原因

// 参考答案及解析：7。根据 5 年 Gopher 都不知道的 defer 细节，你别再掉进坑里！ 提到的“三步拆解法”，第一步执行r = n +1，接着执行第二个 defer，由于此时 f() 未定义，引发异常，随即执行第一个 defer，异常被 recover()，程序正常执行，最后 return。

//此题引自知识星球《Go项目实战》。
```

### 69. for-range副本

```go
下面这段代码输出什么？

func main() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    for i, v := range a {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }
        r[i] = v
    }
    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
}
```

```go
答案：
for-range 切片应该也只是确定了遍历次数或者叫遍历范围，底层实现应该还是基于下标索引。对于遍历期间切片内部元素的变化应该是管不到的。
所以第一次，i=0，直接就使得 a => [1, 12, 13, 4, 5]
r = [1, 12, 13, 4, 5]
a = [1, 12, 13, 4, 5]

//参考答案及解析：

r =  [1 2 3 4 5]
a =  [1 12 13 4 5]
range 表达式是副本参与循环，就是说例子中参与循环的是 a 的副本，而不是真正的 a。就这个例子来说，假设 b 是 a 的副本，则 range 循环代码是这样的：

for i, v := range b {
    if i == 0 {
        a[1] = 12
        a[2] = 13
    }
    r[i] = v
}
因此无论 a 被如何修改，其副本 b 依旧保持原值，并且参与循环的是 b，因此 v 从 b 中取出的仍旧是 a 的原值，而非修改后的值。

如果想要 r 和 a 一样输出，修复办法：

func main() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    for i, v := range &a {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }
        r[i] = v
    }
    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
}
输出：

r =  [1 12 13 4 5]
a =  [1 12 13 4 5]
修复代码中，使用 *[5]int 作为 range 表达式，其副本依旧是一个指向原数组 a 的指针，因此后续所有循环中均是 &a 指向的原数组亲自参与的，因此 v 能从 &a 指向的原数组中取出 a 修改后的值。

引自：https://tonybai.com/2015/09/17/7-things-you-may-not-pay-attation-to-in-go/
```

### 70.

```go
下面这段代码输出什么？

func change(s ...int) {
    s = append(s,3)
}

func main() {
    slice := make([]int,5,5)
    slice[0] = 1
    slice[1] = 2
    change(slice...)
    fmt.Println(slice)
    change(slice[0:2]...)
    fmt.Println(slice)
}


```

```go
答案：


//
```

### 71.

```go
下面这段代码输出什么？

func main() {
    var a = []int{1, 2, 3, 4, 5}
    var r [5]int

    for i, v := range a {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }
        r[i] = v
    }
    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
}
```

```go
答案：


//
```

### 72.

```go

```

```go
答案：


//
```

// 32