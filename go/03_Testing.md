---
title: "单元测试"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["go语言"]
tags: ["go语言", "测试"]
keywords: ["Debug", "Test", "表格驱动测试", "性能测试", "代码覆盖率测试", "pprof可视化性能检查"]

---

### 1.测试介绍

​        尽量测试，避免调试

![1562666185342](/images/1562666185342.png)

### 2. 例子

​		这里使用一道算法题作为调试和测试的例子：

​		求取字符串中最长的含有不重复字符的子字符串的长度。

#### 2.1 开始之前

先了解下Go语言字符串的处理机制

```go
func compareBytesStringRune(s string)  {
   //比较下一个字符串在这三种情况下的存储情况
   println("sBytes： ", []byte(s))
   println("sString： ", s)

   for i, ch := range []byte(s) {
      println("按Byte遍历，第", i, "个元素为：", ch)
   }
   for i, ch := range s {
      println("直接String遍历，第", i, "个元素为：", ch)
   }
   for i, ch := range []rune(s) {
      println("按Rune遍历，第", i, "个元素为：", ch)
   }

}

func main() {
   compareBytesStringRune("azdisgood")
   compareBytesStringRune("艾振东棒棒的")
}
```

输出结果如下：

```
sBytes：  [3/32]0xc000031eb8
sString：  azd
按Byte遍历，第 0 个元素为： 97
按Byte遍历，第 1 个元素为： 122
按Byte遍历，第 2 个元素为： 100
直接String遍历，第 0 个元素为： 97
直接String遍历，第 1 个元素为： 122
直接String遍历，第 2 个元素为： 100
按Rune遍历，第 0 个元素为： 97
按Rune遍历，第 1 个元素为： 122
按Rune遍历，第 2 个元素为： 100
sBytes：  [6/32]0xc000031eb8
sString：  你好
按Byte遍历，第 0 个元素为： 228
按Byte遍历，第 1 个元素为： 189
按Byte遍历，第 2 个元素为： 160
按Byte遍历，第 3 个元素为： 229
按Byte遍历，第 4 个元素为： 165
按Byte遍历，第 5 个元素为： 189
直接String遍历，第 0 个元素为： 20320
直接String遍历，第 3 个元素为： 22909
按Rune遍历，第 0 个元素为： 20320
按Rune遍历，第 1 个元素为： 22909

Process finished with exit code 0
```

得出结论：直接遍历string，其实是按rune进行遍历。当字符串只包含ASCII字符时，三种遍历方法是一样的。rune是Go的可变长度字符表示，表示中文时一个rune代表了三个byte。

#### 2.2 寻找最长不重复子串算法实现

V1版本只考虑英文字符串，V2版本则增加了对中文字符串的支持。

```go
package main

//Leetcode算法题：寻找字符串中连续不重复的最长子串，并返回其长度。

func maxLengthOfNonRepeatSubStrV1(s string) int {
   //遍历字符串，将每个遍历到的字符作为key，其索引作为value，更新入map。

   lastOccuredMap := make(map[byte]int)
   start := 0  //所遍历的字符其开始位置
   maxLength := 0 //最长不重复子串长度

   for i, ch := range []byte(s) {

      //更新字符起始位
      lastI, exists := lastOccuredMap[ch]
      if exists && lastI >= start {
         start = lastI + 1
      }

      //当遍历到的字符距离start的间隔比之前存的maxLength大时，更新maxLength
      //这是因为当遍历到不重复字符时，没有啥操作，此时就应该更新maxLength
      if i-start+1 > maxLength {
         maxLength = i-start+1
      }

      //写入/更新map
      lastOccuredMap[ch] = i
   }

   return maxLength
}

func maxLengthOfNonRepeatSubStrV2(s string) int {
   //遍历字符串，将每个遍历到的字符作为key，其索引作为value，更新入map。

   lastOccuredMap := make(map[rune]int)
   start := 0  //所遍历的字符其开始位置
   maxLength := 0 //最长不重复子串长度

   for i, ch := range []rune(s) {

      //更新字符起始位
      lastI, exists := lastOccuredMap[ch]
      if exists && lastI >= start {
         start = lastI + 1
      }

      //当遍历到的字符距离start的间隔比之前存的maxLength大时，更新maxLength
      //这是因为当遍历到不重复字符时，没有啥操作，此时就应该更新maxLength
      if i-start+1 > maxLength {
         maxLength = i-start+1
      }

      //写入/更新map
      lastOccuredMap[ch] = i

   }

   return maxLength
}

func main() {
   print(maxLengthOfNonRepeatSubStrV1("azdloveazdaawerrz"), " ")  // maxLength -> 7
   print(maxLengthOfNonRepeatSubStrV1("helloworld"), " ")
   print(maxLengthOfNonRepeatSubStrV1("happynewyear"), " ")
   print(maxLengthOfNonRepeatSubStrV1("azdlovezritistrue"), " ")
   print(maxLengthOfNonRepeatSubStrV1("哈哈今天天气真好啊"), " ")
   println()
   print(maxLengthOfNonRepeatSubStrV2("azdloveazdaawerrz"), " ")  // maxLength -> 7
   print(maxLengthOfNonRepeatSubStrV2("helloworld"), " ")
   print(maxLengthOfNonRepeatSubStrV2("happynewyear"), " ")
   print(maxLengthOfNonRepeatSubStrV2("azdlovezritistrue"), " ")
   print(maxLengthOfNonRepeatSubStrV2("哈哈今天天气真好啊"), " ")
}
```

输出结果为：

```
7 5 5 9 11 
7 5 5 9 5 
Process finished with exit code 0
```

### 3. Debug

Goland自带调试工具，用例如下：

![1562674523960](/images/1562674523960.png)

显然Debug需要开发者去不断设置断点找出问题所在。

### 4.传统测试

传统测试其实就是上面代码中的类型，只不过专门写了个测试函数，而测试数据一样被写在测试函数中。

也就是说，**传统测试测试数据和测试逻辑混在一起，出错信息不明确**，而且图中例子一旦有一例报错退出了，后边的数据也不会再执行

![1562675642285](/images/1562675642285.png)

### 5.表格驱动测试

表格驱动测试的特点：

- 测试数据和测试逻辑分离
- 出错信息也很明确
- 也支持部分失败
- Go语言更容易实现表格驱动测试。

![1562676056644](/images/1562676056644.png)

### 6. 测试简单例子

Go语言测试用法为：创建xx_test.go，创建TestXxx(t *testing.T){}，此即为测试函数。而且要注意测试函数名TestXxxx这里Test后跟的第一个字母必须大写。

```go
package main

import "testing"

func TestMaxLengthOfNonRepeatSubStrV2(t *testing.T) {
   tests := []struct{
      s string
      l int
   } {
      	//常规测试
		{"azdloveazdaawerrz", 7},
		{"helloworld", 5},
		{"happynewyear", 3},  //注意这里故意写错一个
		//边缘测试
		{"", 0},
		{"b", 1},
		{"bbbbbbbb", 1},
		{"abcabcabc", 3},
		//中文支持测试
		{"哈哈今天天气真好啊", 5},
		{"可以一起去看看一起去看看流星雨吗", 6},
   }

   for _, tt := range tests {
      calcL := maxLengthOfNonRepeatSubStrV2(tt.s)
      if tt.l != calcL {
         t.Errorf("计算 %s 最长不重复子串错误：应该为 %d， 计算为 %d ", tt.s, tt.l, calcL)
      }
   }
}
```

还可以在命令行使用`go test .` 执行当前目录下所有测试文件中测试函数并输出结果。

上面测试函数的输出结果为：

```
=== RUN   TestMaxLengthOfNonRepeatSubStrV2
--- FAIL: TestMaxLengthOfNonRepeatSubStrV2 (0.00s)
    LongestNonRepeatSubString_test.go:27: 计算 happynewyear 最长不重复子串错误：应该为 3， 计算为 5 
FAIL

Process finished with exit code 1
```

前面说过使用表格驱动测试可以支持部分失败，现在添加一行输出信息再看执行结果：

```go
for _, tt := range tests {
	calcL := maxLengthOfNonRepeatSubStrV2(tt.s)
	if tt.l != calcL {
		t.Errorf("计算 %s 最长不重复子串错误：应该为 %d， 计算为 %d ", tt.s, tt.l, calcL)
    } else {
        println("成功")  //新增的测试成功信息
    }
}
```

结果为：

```
=== RUN   TestMaxLengthOfNonRepeatSubStrV2
成功
成功
成功
成功
成功
成功
成功
成功
--- FAIL: TestMaxLengthOfNonRepeatSubStrV2 (0.00s)
    LongestNonRepeatSubString_test.go:27: 计算 happynewyear 最长不重复子串错误：应该为 3， 计算为 5 
FAIL

Process finished with exit code 1
```

### 7. 代码覆盖率

运行测试函数时可以选择`Run 'TestFunc' with Coverage`可以得到测试函数的代码覆盖率信息：

![1562679530853](/images/1562679530853.png)

在源代码中此时也可以显示出代码覆盖情况（绿边为覆盖的范围，红边反之）

![1562679644486](/images/1562679644486.png)

检查代码覆盖率也可以使用Go的命令行工具实现：

```shell
$ go test -coverprofile=c.out  // 将代码覆盖检测情况输出
$ go tool cover -html=c.out  //将c.out以html形式呈现
```

### 8. Benchmark

前面的Test测试指的是测试代码有无漏洞，而Benchmark测试则指性能测试。

```go
func BenchmarkMaxLengthOfNonRepeatSubStrV2(b *testing.B) {
   //Benchmark只需要少量/一个测试数据即可
   testString := "可以一起去看看一起去看看流星雨吗"
   testLength := 6

   for i:=0; i<b.N; i++ {
      calcL := maxLengthOfNonRepeatSubStrV2(testString)
      if testLength != calcL {
         b.Errorf("计算 %s 最长不重复子串错误：应该为 %d， 计算为 %d ", testString, testLength, calcL)
      }
   }
}
```

执行的结果为：

```
goos: windows
goarch: amd64
pkg: go-mastering-lecture/08-test/LongestNonRepeatSubString
BenchmarkMaxLengthOfNonRepeatSubStrV2-4   	 1000000	      1771 ns/op
PASS

Process finished with exit code 0
```

执行1000000遍，每次的平均时间为1771ns。

### 9.使用pprof可视化性能检测

现在思考为什么每次执行花1771ns的时间，时间主要花费在哪里？可以怎么去提升运行效率？

``` shell
$ go test -bench . -cpuprofile=cpu.out  //生成CPU使用数据
$ go tool pprof cpu.out  //进入pprof交互式命令行工具
(pprof) help	//得到help信息
(pprof) web		//最简单常用的一种做法（需要安装graghviz用以生成svg可视化文件）
(pprof) quit    //退出pprof
```

参考博文：https://blog.csdn.net/weixin_42654444/article/details/82108055

我的步骤是：下载安装grapghviz软件，将其执行目录添加到系统`$PATH$`变量。并在windows默认应用设置“按文件类型设置”将svg文件默认设置由chrome浏览器打开（随便选择一个浏览器，但必须指定，不然使用`web`命令时会报错）

注意！ Benchmark测试函数必须是能够PASS的，如果FAIL，cpu.out文件中不会有相关信息，最后也就画不出cpu使用图

得到的结果如下：

![1562724759199](/images/1562724759199.png)

框越大说明占用CPU时间越长。显然这里map操作是最耗时的，其次是rune解码。

### 10. 优化性能

既然已经指出map操作和rune操作耗时较长，想办法优化它：

#### 10.1 优化过程 

先试下用slice替换map

```go
func maxLengthOfNonRepeatSubStrV3(s string) int {
   //元素默认值为0，为0意味着字符还没有出现
   //这里直接拿字符的编码作为切片的下标索引，存储的为lastOccurredPosition + 1
   //lastOccurredPosition字符在给定字符串当前更新的最后出现的位置
   lastOccuredSlice := make([]int, 0xffff)  //中文字rune长度两个字节。
   start := 0  //所遍历的字符其开始位置
   maxLength := 0 //最长不重复子串长度

   for i, ch := range []rune(s) {

      if lastI := lastOccuredSlice[ch]; lastI >= start {
         start = lastI
      }

      if i-start+1 > maxLength {
         maxLength = i-start+1
      }

      lastOccuredSlice[ch] = i + 1
   }
   return maxLength
}
```

漏洞测试：

```
=== RUN   TestMaxLengthOfNonRepeatSubStr
成功
成功
成功
成功
成功
成功
成功
成功
成功
--- PASS: TestMaxLengthOfNonRepeatSubStr (0.00s)
PASS
```

性能测试：

```
goos: windows
goarch: amd64
pkg: go-mastering-lecture/08-test/LongestNonRepeatSubString
BenchmarkMaxLengthOfNonRepeatSubStr-4   	   10000	    110636 ns/op
PASS

Process finished with exit code 0
```

结果居然是耗时远大于使用map

使用pprof来检查一下：

![1562726202101](/images/1562726202101.png)

原因出现GC（垃圾回收）上，每次运行都初始化一片内存作为slice存储，之后又进行了垃圾回收，导致大量的时间花在这方面。

修改如下：将slice初始化语句放到函数体外部。（顺手纠正了下拼写错误）

```go
var lastOccurredSlice = make([]int, 0xffff)
func maxLengthOfNonRepeatSubStrV4(s string) int {
   //对slice清零
   for i, _ := range lastOccurredSlice {
      lastOccurredSlice[i] = 0
   }
   start := 0  //所遍历的字符其开始位置
   maxLength := 0 //最长不重复子串长度

   for i, ch := range []rune(s) {

      if lastI := lastOccurredSlice[ch]; lastI >= start {
         start = lastI
      }

      if i-start+1 > maxLength {
         maxLength = i-start+1
      }

      lastOccurredSlice[ch] = i + 1
   }
   return maxLength
}
```

性能结果：

```
goos: windows
goarch: amd64
pkg: go-mastering-lecture/08-test/LongestNonRepeatSubString
BenchmarkMaxLengthOfNonRepeatSubStr-4   	   50000	     30862 ns/op
PASS

Process finished with exit code 0
```

这次快了很多，但还是远比使用map慢。继续性能检查：

![1562726988993](/images/1562726988993.png)

可以看出最多的时间被消耗在了memclr上（编译器识别出代码中slice清零那一段，并使用memclr来实现）。

现在这个情况慢是因为数组长度太大(0xff)，而前边map方式其实只需要存给定字符串中出现的字符，所以内存开销小得多。

那么，slice方式使用了固定长度的策略，也就是说无论给定的字符串多长多复杂，程序运行的内存开销都一样（至少在创建和清零这两步）

这意味着，slice方式更适合非常长的、各种各样字符都有的 这样的字符串；而map方式则比较适合短字符串。

其实只要字符串非常长时，使用slice就优于map，因为slice的更新比map更新快很多。

现在，将Benchmark函数中字符串给定部分加长：

```go
func BenchmarkMaxLengthOfNonRepeatSubStr_LongStr(b *testing.B) {
   //产生长字符串
   testString := "可以一起去看看一起去看看流星雨吗"
   for i:=0;i<15;i++ {
      testString = testString +testString  //2^i级别增长
   }
   testLength := 10   //变成10
   b.Logf("len(testString) = %d", len(testString))
    
   for i:=0; i<b.N; i++ {
       //修改待测试的函数名
      calcL := maxLengthOfNonRepeatSubStrV4(testString)
      if testLength != calcL {
         b.Errorf("计算 %s 最长不重复子串错误：应该为 %d， 计算为 %d ", testString, testLength, calcL)
      }
   }
}
```

对maxLengthOfNonRepeatSubStrV2（map版本）和maxLengthOfNonRepeatSubStrV4（slice版本）进行性能测试，结果如下：

```
V2：
goos: windows
goarch: amd64
pkg: go-mastering-lecture/08-test/LongestNonRepeatSubString
BenchmarkMaxLengthOfNonRepeatSubStr_LongStr-4   	      30	  36978810 ns/op
--- BENCH: BenchmarkMaxLengthOfNonRepeatSubStr_LongStr-4
    LongestNonRepeatSubString_test.go:54: len(testString) = 1572864
    LongestNonRepeatSubString_test.go:54: len(testString) = 1572864
PASS

Process finished with exit code 0

V4：
goos: windows
goarch: amd64
pkg: go-mastering-lecture/08-test/LongestNonRepeatSubString
BenchmarkMaxLengthOfNonRepeatSubStr_LongStr-4   	     100	  12193029 ns/op
--- BENCH: BenchmarkMaxLengthOfNonRepeatSubStr_LongStr-4
    LongestNonRepeatSubString_test.go:54: len(testString) = 1572864
    LongestNonRepeatSubString_test.go:54: len(testString) = 1572864
PASS

Process finished with exit code 0
```

显然这里slice方式已经比map方式快了。



现在计算的时间将准备字符串等数据的时间算进去了，应该将其排除出去：

```go
func BenchmarkMaxLengthOfNonRepeatSubStr_LongStr(b *testing.B) {
   //产生长字符串
   testString := "可以一起去看看一起去看看流星雨吗"
   for i:=0;i<15;i++ {
      testString = testString +testString   //2^i级别增长
   }
   testLength := 10  //变成10
   b.Logf("len(testString) = %d", len(testString))

   b.ResetTimer()  //计时器重置

   for i:=0; i<b.N; i++ {
      //修改待测试的函数名
      calcL := maxLengthOfNonRepeatSubStrV4(testString)
      if testLength != calcL {
         b.Errorf("计算 %s 最长不重复子串错误：应该为 %d， 计算为 %d ", testString, testLength, calcL)
      }
   }
}
```

结果：

```
goos: windows
goarch: amd64
pkg: go-mastering-lecture/08-test/LongestNonRepeatSubString
BenchmarkMaxLengthOfNonRepeatSubStr_LongStr-4   	     100	  12532807 ns/op
--- BENCH: BenchmarkMaxLengthOfNonRepeatSubStr_LongStr-4
    LongestNonRepeatSubString_test.go:54: len(testString) = 1572864
    LongestNonRepeatSubString_test.go:54: len(testString) = 1572864
PASS

Process finished with exit code 0
```

这里注意到耗时其实还变长了些，这可能是每次运行时产生的一些偏差，另一方面也说明当处理超长字符串时，前边数据准备的耗时显得微不足道。

继续对V4版本进行性能检查（注意只检查BenchmarkMaxLengthOfNonRepeatSubStr_LongStr  slice版本）：

![1562729315071](/images/1562729315071.png)

可以看出这回时间大部分耗费在字符串的处理上面。但是没有办法，如果要支持中文字符串，只能这么做。

提示：我是通过创建多个函数来进行方法的比较，也可以在一个函数上修改，通过版本控制来进行版本切换。

#### 10.2 感想

分析算法运行性能要根据不同的数据规模来决定；

很多时候性能开销主要在io操作而不在数据操作，所以可以牺牲一些数据操作的性能来换取代码的可读性

### 11. http测试

基于02_UnifiedErrorHandling.md中的http服务器的部分，现在进行http服务器的测试。先把完整代码移过来，并作代码的分离。

http.go：

```go
package main

import (
	"go-mastering-lecture/08-test/httpServerTest/handler"
	"net/http"
)

type userErrorInterface interface {
	error  //组合自error接口
	Message() string
}
func main() {
	// 127.0.0.1:8080/list/07-errors/fib.txt
	http.HandleFunc("/", handler.ErrWrapperV4(handler.HandleListFileV4))
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		panic(err)
	}
}
```
handler/handler.go

```go
package handler

import (
   "fmt"
   "io/ioutil"
   "net/http"
   "os"
   "strings"
)

type appHandler func(http.ResponseWriter, *http.Request) error

func ErrWrapperV4(handler appHandler) func(http.ResponseWriter, *http.Request) {
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
         if userErr, ok := err.(userError); ok {
            //直接把自定义的错误返回给用户，因为是需要他们知道这些信息
            http.Error(w, userErr.Message(), http.StatusBadRequest)
         }

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

const PREFIX  = "/list/"

type userError string
func (e userError) Error() string {
   return e.Message()
}
func (e userError) Message() string {
   return string(e)
}

func HandleListFileV4(w http.ResponseWriter, r *http.Request) error {
   //增加对URL Path的检查,检查Path中这个"list"是否是出现在最前面
   if strings.Index(r.URL.Path, PREFIX) != 0 {
      //自定义错误
      return userError("path必须以 "+ PREFIX + " 开头")
   }
   path := r.URL.Path[len(PREFIX):]   //也可以使用strings中的去除前缀方法
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

#### 11.1 errWrapper函数测试

创建errWrapper_test.go，准备编写测试代码。

编写errWrapper函数的测试函数如下：

```go
package main

import (
   "fmt"
   "github.com/pkg/errors"
   "io/ioutil"
   "net/http"
   "net/http/httptest"
   "os"
   "strings"
   "testing"
)

/*自定义错误事件appHandler*/

func errPanic(w http.ResponseWriter, r *http.Request) error {
   panic(123)
}

//创建自定义类型，继承自http服务器文件中的UserErrorInterface
type testingUserError string
func (e testingUserError) Error() string {
   return e.Message()
}
func (e testingUserError) Message() string {
   return string(e)
}

func errUserError(w http.ResponseWriter, r *http.Request) error {
   return testingUserError("user error")
}
func errNotFound(w http.ResponseWriter, r *http.Request) error {
   return os.ErrNotExist
}
func errNoPermission(w http.ResponseWriter, r *http.Request) error {
   return os.ErrPermission
}
func errUnknown(w http.ResponseWriter, r *http.Request) error {
   return errors.New("unknown error")
}

func noError(w http.ResponseWriter, r *http.Request) error {
   fmt.Fprintln(w, "no error")
   return nil
}

func TestErrWrapper(t *testing.T) {
   tests := []struct{
      h appHandler
      code int
      msg string
   } {
      {errPanic, 500, "Internal Server Error"},
      {errUserError, 400, "user error"},
      {errNotFound, 404, "Not Found"},
      {errNoPermission, 403, "Forbidden"},
      {errUnknown, 500, "Internal Server Error"},
      {noError, 200, "no error"},
   }

   for _, tt := range tests {
      f := errWrapperV4(tt.h)
      //使用httptest包。
      //httptest.ResponseRecorder实现了http.ResponseWriter
      response := httptest.NewRecorder()
      request := httptest.NewRequest(
         http.MethodGet,
         "http://www.imooc.com",  //这里是随便写的
         nil,
         )

      f(response, request)

      b, _ := ioutil.ReadAll(response.Body)
      //因为body会末尾有换行，把它去掉
      body := strings.Trim(string(b), "\n")

      if response.Code != tt.code || body != tt.msg {
         t.Errorf("期望获得 （%d， %s）， 但是却得到：（%d， %s）",
            tt.code, tt.msg, response.Code, body)
      }

   }
}
```

执行结果：

```
=== RUN   TestErrWrapper
panic: 123
出错： user error
出错： file does not exist
出错： permission denied
出错： unknown error
--- FAIL: TestErrWrapper (0.00s)
    errWrapper_test.go:76: 期望获得 （400， user error）， 但是却得到：（400， user error
        Internal Server Error）
FAIL

Process finished with exit code 1
```

这不符合我们的预期，输出显示对自定义错误的处理上出了问题，其网页返回不仅有"user error"还有"Internal Server Error"，这说明我们前面在errWrapperV4中对自定义错误的处理出了问题，修复如下：

```go
if userErr, ok := err.(userErrorInterface); ok {
   //直接把自定义的错误返回给用户，因为是需要他们知道这些信息
   http.Error(w, userErr.Message(), http.StatusBadRequest)
   return  //新增。记得return，不然会继续输出http.Error(w, http.StatusText(code), code)这一段
}
```

在运行下测试函数：

```
=== RUN   TestErrWrapper
panic: 123
出错： user error
出错： file does not exist
出错： permission denied
出错： unknown error
--- PASS: TestErrWrapper (0.00s)
PASS

Process finished with exit code 0
```

这回没有问题了，说明我们的错误包装器正确，测试没有问题。

#### 11.2 开启服务器测试

上一节模拟了url请求（使用假的Request和Response），这一节则通过开启httpserver的方式来实现这个测试。这两种方法间测试的粒度不同，前一种方法只测试函数体内的这些代码，而启用服务器做测试则测试了整个服务器。前者更快，更像个单元测试。

```go
var tests = []struct{
   h appHandler
   code int
   msg string
} {
   {errPanic, 500, "Internal Server Error"},
   {errUserError, 400, "user error"},
   {errNotFound, 404, "Not Found"},
   {errNoPermission, 403, "Forbidden"},
   {errUnknown, 500, "Internal Server Error"},
   {noError, 200, "no error"},
}

func TestErrWrapperInServer(t *testing.T) {
	for _, tt := range tests {
		f := errWrapperV4(tt.h)
		server := httptest.NewServer(http.HandlerFunc(f))		//将f转为Server (interface)
		response, _ := http.Get(server.URL)

		b, _ := ioutil.ReadAll(response.Body)
		body := strings.Trim(string(b), "\n")

		//response.StatusCode
		if response.StatusCode != tt.code || body != tt.msg {
			t.Errorf("期望获得 （%d， %s）， 但是却得到：（%d， %s）",
				tt.code, tt.msg, response.StatusCode, body)
		}
	}
}
```

执行结果：

```
=== RUN   TestErrWrapperInServer
panic: 123
出错： user error
出错： file does not exist
出错： permission denied
出错： unknown error
--- PASS: TestErrWrapperInServer (0.02s)
PASS

Process finished with exit code 0
```

### 12. 生成文档及示例代码

#### 12.1 使用go doc生成及查看代码文档

准备好queue.go：

```go
package queue

type Queue []int

func (q *Queue) Push(v int) {
   *q = append(*q, v)
}

func (q *Queue) Pop() int {
   head := (*q)[0]
   *q = (*q)[1:]
   return head
}

func (q *Queue) IsEmpty() bool {
   return len(*q) == 0
}
```

现在利用go doc在终端查看我的queue中的帮助文档（自动生成）：

输入 `go doc`，将会为当前目录下所有代码生成文档。

继续输入`go doc queue`，显示`package queue`中的导入及定义类型信息。

输入go doc SYMBOL可以查看文档中SYMBOL的说明

**注意！当要查看某个包的具体文档时，需要切换到包目录下面。**

```shell
E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue
>go doc
package queue // import "go-mastering-lecture/08-test/GenerateDocFo
rYourCode/queue"

type Queue []int

E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue
>go doc queue
package queue // import "go-mastering-lecture/08-test/GenerateDocFo
rYourCode/queue"

type Queue []int

E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue
>go doc Queue
type Queue []int

func (q *Queue) IsEmpty() bool
func (q *Queue) Pop() int
func (q *Queue) Push(v int)

E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue
>go doc IsEmpty
func (q *Queue) IsEmpty() bool

```

#### 12.2 使用go doc查看标准库文档

用法基本一样，而且不需要注意路径问题，只要在GOPATH下即可。例如：

```shell
E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue
>go doc fmt.Println
func Println(a ...interface{}) (n int, err error)
    Println formats using the default formats for its operands and writes to standard output. Spaces are always added between operands and a newline is appended. It returns the number of bytes written and any write error encountered.
```

#### 12.3 使用godoc查看文档

godoc不仅支持前面go doc类似的命令行文档显示，有很多显示文档的方式，`GOPATH`下输入`godoc -help`即可查看其信息及命令介绍。

最常用的是`godoc -http :6060`， 端口可自定义。浏览器访问可查看本机GOPATH下所有包包括标准库的html文档。

![1563010911803](/images/1563010911803.png)

![1563011125195](/images/1563011125195.png)

![1563010973865](/images/1563010973865.png)

![1563011346205](/images/1563011346205.png)

![1563011372720](/images/1563011372720.png)

#### 12.4 添加注释完善文档

前面得到的文档都没有介绍，只有类型或者定义。要想有介绍，需要自己在包内类型、函数定义前添加注释。

例如：

```go
package queue

// 先进先出队列
type Queue []int

// 将元素压入栈底
func (q *Queue) Push(v int) {
   *q = append(*q, v)
}

// 将栈顶元素弹出
func (q *Queue) Pop() int {
   head := (*q)[0]
   *q = (*q)[1:]
   return head
}

// 判断队列是否为空
func (q *Queue) IsEmpty() bool {
   return len(*q) == 0
}
```

重新`go doc`

```shell
E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue>go
 doc Queue
type Queue []int
    先进先出队列

func (q *Queue) IsEmpty() bool
func (q *Queue) Pop() int
func (q *Queue) Push(v int)

E:\GO\src\go-mastering-lecture\08-test\GenerateDocForYourCode\queue>go
 doc IsEmpty
func (q *Queue) IsEmpty() bool
    判断队列是否为空
```

注释换行再看看？

```go
// 将元素压入栈底
// e.g. q.Push(123)
func (q *Queue) Push(v int) {
   *q = append(*q, v)
}
```

![1563012411236](/images/1563012411236.png)

```go
// 将元素压入栈底
//        e.g. q.Push(123)
func (q *Queue) Push(v int) {
   *q = append(*q, v)
}
```

![1563012480271](/images/1563012480271.png)

这个功能很适合给函数添加示例，并易于阅读。

#### 12.5 生成示例代码

将示例代码直接写到注释中是可以的，只不过不适合阅读（如果示例代码很长的话）。现在来介绍如何生成示例代码。更好用的一种方法：

创建queue_test.go （没错，就是用来创建测试代码的文件）

```go
package queue

import (
   "fmt"
   "testing"
)

func ExampleQueue_Push() {
   q := Queue{1}
   fmt.Println(q)
   q.Push(2)
   fmt.Println(q)

   // Output:   //(必须是OutPut:)
   // [1]
   // [1 2]
}

func TestQueue_Push(t *testing.T) {
   q := Queue{1}
   fmt.Println(q)
   q.Push(2)
   fmt.Println(q)
}
```

Example函数其实也是可以执行的，只不过需要以上面所示的加//Output注释的形式。当不确定具体输出如何时，可以随便写点什么：

```go
   // Output:
   // 3
```

执行后会得出正确答案，再把正确答案粘贴至源代码即可。（你做错了它才给你改，你不做它就不理会）

再去查看文档可得到：

![1563014702200](/images/1563014702200.png)

这就是我们想要的文档示例了。

#### 12.6 总结

注意！想要让注释和示例能被识别，必须注意格式

**标准注释格式： `// + SPACE + [注释]`**

**注释中添加e.g示例： `// + SPACE + TAB + [e.g. 示例注释]`**	

**示例代码添加输出：`// + SPACE + Output:`**

