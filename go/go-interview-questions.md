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


// 看到第13天。