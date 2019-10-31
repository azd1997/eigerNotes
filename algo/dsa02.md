---
title: "数据结构与算法（2）——数组"
date: 2019-09-26T22:05:29+08:00
draft: false
categories: ["algo"]
tags: ["数据结构"]
keywords: ["数组"]
---

## 1. 导语

**为什么数组下表从0开始而非1开始？**

## 2. 如何实现随机访问

**数组(array)** 是一种**线性表**数据结构，用一组**连续**的内存空间，存储一组**相同类型**的数据。

1. **线性表:** 数据排成一条线一样的结构，每个线性表上的数据只有前后两个方向。典型的线性表结构有：数组、链表、队列、栈。与线性表相对应的是**非线性表**，如二叉树、堆、图等，它们的数据之间不是简单的前后关系。
   ![dsa02-1](/images/dsa02-1.jpg)
   ![dsa02-2](/images/dsa02-2.jpg)

2. **连续内存空间与相同类型数据：** 这两个限制使得数组可以实现**随机访问**。当然，这两个限制使得数组的一些操作变得非常低效：比如说删除、插入操作，需要做大量的数据搬移工作。

**数组随机访问的原理：**

计算机为每个内存单元分配地址，并根据地址访问内存单元存储的数据。也就是说如果能知道数据的内存地址，那么就可以直接访问该数据。对于数组，其内存寻址公式为：

```go
a[i]_address = base_address + i * data_type_size
```

例如，java中新建一个整型数组`int[] a = new int[10]`，假设说为数组a分配了首地址为1000的连续内存，那么这段内存空间的尾地址就是1000 + 10 × 4 - 1 = 1039，也可以是1000 + 9 × 4 + 3 = 1039。这里显然，如果内存空间不连续，类型不相同（单个数据内存长度不等），那么就没办法像这个公式一样能快速计算内存地址，也就没办法随机访问。

![dsa02-3](/images/dsa02-3.jpg)

**易错问题:**

说法： 链表适合插入、删除，时间复杂度O(1); 数组适合查找，查找时间复杂度O(1)。

纠正：数组适合查找，但即便是排好序的数组，使用二分查找来查找的时间复杂度也是O(logn)。正确的说法是，*数组支持随机访问，根据下表随机访问的时间复杂度是O(1)*。

## 3. 低效的插入与删除

1. 插入

   对于长度为n（或者说数据规模为n）的数组，插入某一个元素，最好情况是在尾部追加，复杂度O(1);最坏情况是插入到头部，其后所有数据都需要顺次往后移一位，时间复杂度O(n)；平均复杂度为(1+...+n)/n = (n+1)/2，为O(n)。

   如果数组数据是有序的，那么插入新数据时必须是后面数据顺次后移，时间复杂度就是O(n);

   但如果数组数据不要求有序。那么插入第k个位置元素，可以**直接将原数据搬至数组尾部，再插入元素**，这样时间复杂度也是O(1)。

   ![dsa02-4](/images/dsa02-4.jpg)

   这个思路在**快速排序**中也有用到

2. 删除

   删除操作和插入类似，为了保证数据连续性后续数据需要顺次前移。时间复杂度也是O(n)。

   在某些情形下，如果不那么追求数据的内存的连续性，选择**将多个待删除数据同时删除，是不是就避免后续数据的多次前移**？

   例如数据`a,b,c,d,e,f,g`，假如要先后删除`a,b,c`三个元素，那么后续元素总共需要搬运6+5+4 = 15次，但是如果同时删除，那么只需要搬运4次（`d,e,f,g`直接前移三个位置）。

   每一次删除操作时，可以对待删除数据进行标记，也就是先后标记`a,b,c`为删除数据，但其实并没有真正删掉。只是从编程语言使用层面访问不到。假如说数组长度为7，现在想再添加一个数据，那么数组空间是不够的，此时会同时执行`a,b,c`三个数据的真正内存删除操作。

   这也正是**JVM标记清除垃圾回收算法**的核心思想。

## 4. 警惕数组越界访问

考虑如下例子：

```c
int main(int argc, char* argv[]){
    int i = 0;
    int arr[3] = {0};
    for(; i<=3; i++){   // i<=3终止条件写错，应该是i<3
        arr[i] = 0;
        printf("hello world\n");
    }
    return 0;
}

// 执行结果： 无限打印“hello world”
```

在C语言中只要不是访问受限的内存，都是可以通过内存地址访问到的。在这个例子中循环到`i=3`时`arr[3]`会指向内存空间中首地址为 arr_base_address + 3 * 4 的4byte空间。

而由于函数体内的局部变量存在栈上，且是连续压栈。在Linux进程的内存布局中，栈区在高地址空间，从高向低增长。变量i和arr在相邻地址，且i比arr的地址大，所以arr越界正好访问到i。当然，前提是i和arr元素同类型，否则那段代码仍是未决行为。另外一点是：跟编译器分配内存和字节对齐有关 数组3个元素 加上一个变量a 。4个整数刚好能满足8字节对齐 所以i的地址恰好跟着a2后面 导致死循环。。如果数组本身有4个元素 则这里不会出现死循环。。因为编译器64位操作系统下 默认会进行8字节对齐 变量i的地址就不紧跟着数组后面了。

这就导致`arr[3]=0`实际是`i=0`，所以程序陷入了无限循环。

数组越界在C语言中是一种未决行为，并没有规定数组访问越界时编译器如何处理。只要数组通过偏移公式得到的内存地址可用，那么编译器就很可能不报任何错误。很多计算机病毒也正是利用这一点来进行攻击

一些高级语言如java、go，会对内存越界作检查并抛出相应异常。

## 5. 容器能否完全替代数组

在java中为数组提供了ArrayList容器类（很多其他语言也有，比如说C++ STL的Vector类），ArrayList容器类的两大好处：

- **封装了很多数组操作的方法细节，比如删除元素等**
- **支持动态扩容**

动态扩容的原理其实就是在结构体里封装一个底层数组和一个用来标志当前数据长度的变量，当底层数组满时为其另分配一个1.5倍大小的内存空间，并将数据搬移过去。

因此，当容器发生扩容时涉及内存申请和数据搬移，比较耗时，最好能提前知道数据规模，在**创建数组容器时指定大小以避免扩容**

在选用容器还是数组时考虑一下几点：

1. Java ArrayList无法存储基本类型int、long等，需要封装为Integer、Long类，而Autoboxing和Unboxing的过程有一定性能损耗，如果特别关注性能，比如说网络框架开发这类偏底层的开发，选择原生数组。当然，一般的业务开发，使用容器类会更方便。或者想使用基本数据类型，也使用原生数组。
2. 如果数据大小事先已知，且需要用到的操作也比较简单，也可以选用原生数组
3. 表示多维数组时，原生数组会更直观一些`Object[][] array`。容器数组则是`ArrayList<ArrayList> array`

## 6. 数组下标为什么从0开始

数组下标从0开始和从1开始相比，在计算偏移量时节省了一次CPU的加法运算。

更主要的是历史原因，C语言这么搞，后来者（其他的高级语言）为了减少C语言使用者的学习成本，也这么设计了。

## 7. 思考

1. JVM标记清除垃圾回收算法

    大多数主流虚拟机采用**可达性分析算法**来判断对象是否存活，在标记阶段，会遍历所有 GC ROOTS，将所有 GC ROOTS 可达的对象标记为存活。只有当标记工作完成后，清理工作才会开始。

    不足：1.效率问题。标记和清理效率都不高，但是当知道只有少量垃圾产生时会很高效。2.空间问题。会产生不连续的内存空间碎片。

2. 二维数组寻址公式

    对于`m*n`的数组，`a[i][j] (i<m,j<n)`的地址为：

    `address = base_address + (i*n+j) * type_size`

## 8. 练习

1. 实现动态扩容数组
2. 实现固定长度数组，并实现动态增删改查方法
3. 合并两个有序数组到新的有序数组

代码实现：

```go
// staticArray.go

/*
不使用length标记数组长度，而通过切片自身长度和容量来实现的固定数组，太折腾了。
重难点在于插入时需要注意一些细节。
此外，实现了反向索引。
一般的数组模拟都是
type StaticArray struct {
    data []int
    length int
}
这样实现起来方便许多
*/

package ex5_array

import (
	"errors"
	"fmt"
)

// StaticArray 使用固定容量的切片来模拟固定长度的数组
type StaticArray struct {
	data []int
}

// NewStaticArray 根据容量生成固定容量的数组
func NewStaticArray(cap uint) *StaticArray {
	if cap == 0 {
		return nil
	}
	return &StaticArray{make([]int, 0, cap)}
}

// Cap 数组容量
func (a *StaticArray) Cap() int {
	return cap(a.data)
}

// Len 数组已使用长度
func (a *StaticArray) Len() int {
	return len(a.data)
}

// Find 根据索引查找元素，支持负数索引
func (a *StaticArray) Find(index int) (res int, err error) {
	l := a.Len()
	err = isIndexOut(index, l, OP_FIND)
	if err != nil {
		return 0, err
	}
	var realIndex int
	realIndex = toRealIndex(index, l)

	return a.data[realIndex], nil
}

// Insert 根据下标插入元素，保持原元素的相对位置。 有一种取巧的插入是把直接把插入位置上的元素搬到末尾，这里不用
func (a *StaticArray) Insert(index int, elem int) (err error) {
	// 检查数组是否已满
	if a.isFull() {
		return errors.New("array is full")
	}

	// 插入到数组所使用部分的末尾。append
	l := a.Len()
	if index == l || index == -1 {
		a.data = append(a.data, elem)
		return nil
	}

	// 一般位置插入(检查越界，转换索引，插入)
	err = isIndexOut(index, l, OP_INSERT)
	if err != nil {
		return err
	}
	var realIndex int
	realIndex = toRealIndex(index, l)

	// 在真正的插入之前  记得要给数组追加一个元素，用来给插入占位置，不然插入会索引越界
	// 并且每次报错时，应该重新将这个位置删除。（这个设计太操蛋了，还是加个length好）
	a.data = append(a.data, 0)

	// 原数据向后挪移
	for i:=l-1; i>=realIndex; i-- {
		a.data[i+1] = a.data[i]
	}
	a.data[realIndex] = elem

	return nil
}

// Del 删数据，后续前移
// 注意：在这样的没有length标记字段的设计里，没办法回收空间，删除只能通过重新分配内存实现
func (a *StaticArray) Del(index int) error {
	l := a.Len()
	var err error
	err = isIndexOut(index, l, OP_DELETE)
	if err != nil {
		return err
	}
	var realIndex int
	realIndex = toRealIndex(index, l)

	// 删除通过拷贝另一个切片实现
	slice := make([]int, l-1, a.Cap())
	for i:=0; i<realIndex; i++ {
		slice[i] = a.data[i]
	}
	for i:=realIndex; i<l-1; i++ {
		slice[i] = a.data[i+1]
	}
	a.data = slice
	return nil
}

// Update 更新元素值
func (a *StaticArray) Update(index int, new int) error {
	l := a.Len()
	var err error
	err = isIndexOut(index, l, OP_DELETE)
	if err != nil {
		return err
	}
	var realIndex int
	realIndex = toRealIndex(index, l)

	a.data[realIndex] = new
	return nil
}

// Print 打印
func (a *StaticArray) Print() {
	fmt.Println(a.data)
}

const (
	OP_INSERT = iota
	OP_DELETE
	OP_UPDATE
	OP_FIND
)

var opMap = map[uint8]string{
	OP_INSERT : "op_insert",
	OP_DELETE : "op_delete",
	OP_UPDATE : "op_update",
	OP_FIND : "op_find",
}

// isIndexOut 判断索引是否越界
func isIndexOut(index int, listLength int, op uint8) (err error) {

	switch op {
	case OP_INSERT:
		if index > listLength || index < -listLength-1 {
			err = errors.New("index out of range")
		}
	case OP_DELETE, OP_UPDATE, OP_FIND:
		if index >= listLength || index < -listLength {
			err = errors.New("index out of range")
		}
	default:
		err = errors.New("unknown operation")
	}

	if err != nil {
		return fmt.Errorf("%s: %s", opMap[op], err)
	}
	return nil
}

// toRealIndex 处理int型索引下标，将其转换为自然数。调用之前需根据不同操作检查索引越界
func toRealIndex(index int, listLength int) (realIndex int) {

	// 0, 1, 2, 3 中间插入4 => 0, 1, 4, 2, 3
	// 现长度4， 插入正索引2， 插入负索引-3
	// 0, 1, 2, 3 中间插入4 => 0, 1, 4, 2, 3
	// 现长度4， 插入正索引4， 插入负索引-1
	if index < 0 {
		realIndex = listLength + index + 1
	} else {
		realIndex = index
	}
	return realIndex
}

// isFull 判断数组是否已满
func (a *StaticArray) isFull() bool {
	return a.Len() == a.Cap()
}
```

测试：

```go
// staticArray_test.go


package ex5_array

import (
	"fmt"
	"testing"
)

func TestStaticArray(t *testing.T) {
	s := NewStaticArray(5) // len = 0, cap = 5}
	fmt.Printf("len = %d, cap = %d\n", s.Len(), s.Cap())

	var err error
	//if err = s.Insert(2, 99); err == nil {
	//	t.Error("本应出错")
	//}

	// 正常插入五个数
	if err = s.Insert(0, 90); err != nil {
		t.Error(err)
	}
	fmt.Printf("len = %d, cap = %d\n", s.Len(), s.Cap())
	s.Print()
	if err = s.Insert(1, 91); err != nil {
		t.Error(err)
	}
	fmt.Printf("len = %d, cap = %d\n", s.Len(), s.Cap())
	s.Print()
	if err = s.Insert(2, 92); err != nil {
		t.Error(err)
	}
	fmt.Printf("len = %d, cap = %d\n", s.Len(), s.Cap())
	s.Print()
	if err = s.Insert(0, 93); err != nil {
		t.Error(err)
	}
	fmt.Printf("len = %d, cap = %d\n", s.Len(), s.Cap())
	s.Print()
	if err = s.Insert(3, 94); err != nil {
		t.Error(err)
	}
	fmt.Printf("len = %d, cap = %d\n", s.Len(), s.Cap())
	s.Print()

	// 试试满了还插
	if err = s.Insert(0, 90); err == nil {
		t.Error("本应出错")
	}
	s.Print()

	// 更改
	if err = s.Update(0, 99); err != nil {
		t.Error(err)
	}
	s.Print()

	// 删除
	if err = s.Del(3); err != nil {
		t.Error(err)
	}
	s.Print()

	return
}
```

测试输出：

```shell
=== RUN   TestStaticArray
len = 0, cap = 5
len = 1, cap = 5
[90]
len = 2, cap = 5
[90 91]
len = 3, cap = 5
[90 91 92]
len = 4, cap = 5
[93 90 91 92]
len = 5, cap = 5
[93 90 91 94 92]
[93 90 91 94 92]
[99 90 91 94 92]
[99 90 91 92]
--- PASS: TestStaticArray (0.00s)
PASS

Process finished with exit code 0
```

基本上没看出啥问题。（不过不保证有没有潜在的bug）

下面再附上一个常规的数组模拟实现。

```go
type Array struct {
	data   []int
	length uint
}

//为数组初始化内存
func NewArray(capacity uint) *Array {
	if capacity == 0 {
		return nil
	}
	return &Array{
		data:   make([]int, capacity, capacity),
		length: 0,
	}
}

func (this *Array) Len() uint {
	return this.length
}

//判断索引是否越界
func (this *Array) isIndexOutOfRange(index uint) bool {
	if index >= uint(cap(this.data)) {
		return true
	}
	return false
}

//通过索引查找数组，索引范围[0,n-1]
func (this *Array) Find(index uint) (int, error) {
	if this.isIndexOutOfRange(index) {
		return 0, errors.New("out of index range")
	}
	return this.data[index], nil
}

//插入数值到索引index上
func (this *Array) Insert(index uint, v int) error {
	if this.Len() == uint(cap(this.data)) {
		return errors.New("full array")
	}
	if index != this.length && this.isIndexOutOfRange(index) {
		return errors.New("out of index range")
	}

	for i := this.length; i > index; i-- {
		this.data[i] = this.data[i-1]
	}
	this.data[index] = v
	this.length++
	return nil
}

func (this *Array) InsertToTail(v int) error {
	return this.Insert(this.Len(), v)
}

//删除索引index上的值
func (this *Array) Delete(index uint) (int, error) {
	if this.isIndexOutOfRange(index) {
		return 0, errors.New("out of index range")
	}
	v := this.data[index]
	for i := index; i < this.Len()-1; i++ {
		this.data[i] = this.data[i+1]
	}
	this.length--
	return v, nil
}

//打印数列
func (this *Array) Print() {
	var format string
	for i := uint(0); i < this.Len(); i++ {
		format += fmt.Sprintf("|%+v", this.data[i])
	}
	fmt.Println(format)
}
```