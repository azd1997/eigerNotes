---
title: "排序算法整理与实现"
date: 2020-02-23T19:01:46+08:00
draft: false
categories: ["algo"]
tags: ["sort"]
keywords: ["bubble sort", "select sort", "insert sort"]
---

## 0. 排序

注意，评价一种排序算法，主要看以下几种因素：

- 时间复杂度：分最好、最坏、平均时间复杂度(当然还有个摊还时间复杂度)
- 空间复杂度: O(1)空间的排序称作原地排序
- 稳定性: 对于"相等"的元素之间相对位置不改变

一些说明：

- 以下描述中出现的区间$[a:b]$遵循**左闭右开**原则
- 以下排序按升序排序介绍，降序和升序原理一致
- 依赖线性遍历的排序算法可以从左边开始也可以从右边开始，没有定式
- $nums$表示待排序数组，$n$为其长度

## 1. 基础排序算法

基础排序算法介绍冒泡排序。选择排序和插入排序，这三种算法时间复杂度都是$O(n^2)$，并且都是原地排序算法。下面快速过一遍三种排序算法。此外，还介绍希尔排序。

### 1.1 冒泡排序

- 核心思路：多次遍历，相邻者两两比较，若逆序则交换。每一轮遍历都把最大值冒泡到后部的正确位置。
- 实现：

```go
func bubbleSort1(arr []int) []int {
    n := len(arr)
    for i:= n-1; i>=0; i-- {
        for j:=0; j<i; j++ {
            if arr[j] > arr[j+1] {
                arr[j], arr[j+1] = arr[j+1], arr[j]
            }
        }
    }
    return arr
}
```

- 优化：如果整个排序过程中没有交换，提前结束。(也就是某一轮遍历发现没有逆序)

```go
func bubbleSort2(arr []int) []int {
    n := len(arr)
    existExchange := false
    for i:= n-1; i>=0; i-- {
        for j:=0; j<i; j++ {
            if arr[j] > arr[j+1] {
                arr[j], arr[j+1] = arr[j+1], arr[j]
                existExchange = true
            }
        }
        if !existExchange {break}   // 如果该轮遍历不存在交换，那么提前结束
    }
    return arr
}
```

- 冒泡算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(n)$|$O(n^2)$|$O(n^2)$|$O(1)$|可以稳定|

- *可以稳定： 当遇到相等元素，不进行交换，这样可以稳定*
- *在$O(n^2)$时间复杂度的排序算法中，冒泡几乎是最慢的，原因在于其每次比较都进行两两交换(相当于三次赋值操作);*
- *冒泡排序的优点在于: 对于逆序度较低的序列，能够提前通过检测交换发现来决定是否提前结束*

### 1.2 选择排序

- 核心思路：每一轮遍历时选择待排序区间$nums[0:i+1]$的最大值放到$nums[i]$位置。第一次遍历归位$nums[n]$，第二次归位$nums[n-1]$, ...
- 实现

```go
func selectionSort1(arr []int) []int {
    n := len(arr)
    minIdx := 0   // 每一轮遍历中的最小值的下标
    for i:=0; i<n; i++ {
        minIdx = i
        for j:=i+1; j<n; j++ {
                if arr[j] < arr[minIdx] {
                minIdx = j
            }
        }
        arr[i], arr[minIdx] = arr[minIdx], arr[i]
    }
    return arr
}
```

- 选择算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(n)$|$O(n^2)$|$O(n^2)$|$O(1)$|可以稳定|

- *可以稳定： 当遇到相等元素，取最后出现的相等元素放置在区间末尾，这样可以稳定*
- *选择排序提高了冒泡排序的性能，它每遍历一次列表只交换一次数据，即进行一次遍历时找到最大的项，完成遍历后，再把它换到正确的位置。*

### 1.3 插入排序

- 核心思路：每一轮遍历时前面$nums[0:i]$区间已升序排列好，对$nums[i]$进行插入操作: 从$i-1$开始向左遍历至$0$，查找$nums[i]$这个值应该放的位置，找到后将其插入
- 实现

```go
func insertSort1(arr []int) []int {
    n := len(arr)
    curNum, j := 0, 0
    for i:=1; i<n; i++ {
        curNum = arr[i] // 准备找位置插入的数
        j = i
        for ; j>0; j-- {    // 跟前面已排序好的arr[:i]逐一比较
            if curNum < arr[j-1] {
                arr[j] = arr[j-1]   // 将原arr[j-1]的数挪到arr[j]，这里其实是有点像冒泡，把curNum冒到已排序部分合适位置
            } else {    // 找到该放的位置了
                break
            }
        }
        arr[j-1] = curNum   // 把curNum放到前面找到的合适位置上
    }
    return arr
}

```

- 插入算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(n)$|$O(n^2)$|$O(n^2)$|$O(1)$|可以稳定|

- *可以稳定： 向左查找插入位置时，当遇到相等元素，不进行交换，也就是不越过它，这样稳定*
- *快速排序比选择排序快的原因在于**归位**过程，快速排序可以在找到插入位置后停止当前轮遍历，选择排序却必须遍历至尾*
- *插入排序是最常用的$O(^2)$排序算法，经常用于快速排序、归并排序的小数据规模处理*

### 1.4 希尔排序

- 核心思路：将原$nums$每一轮按$gap$分隔成$n/gap$个子序列，对每个子序列进行插入排序，下一轮则$gap = gap/2$继续同样操作。例如，第一轮时$gap = n/2$，则$nums$被分为子序列$\{nums[0], nums[0+gap]\}, \{nums[1], nums[1+gap]\},...$
- 实现

```go
func shellSort1(arr []int) []int {
    n := len(arr)
    gap := n / 2    // 间隔初设为n/2，也就是说把arr一开始分成n/2个长度为2的数列进行插入排序
    for gap>0 {
        for i:=0; i<gap; i++ {
            gapInsertSort(&arr, i, gap)
        }
        gap /= 2
    }
    return arr
}

func gapInsertSort(arr *[]int, start, gap int) {
    n := len(*arr)
    curNum, j := 0, 0
    for i:=start+gap; i<n; i+=gap {
        j = i
        curNum = (*arr)[i]
        for ; j>=0; j-- {
            if curNum < (*arr)[j-1] {
                (*arr)[j] = (*arr)[j-1]
            } else {break}
        }
        (*arr)[j-1] = curNum
    }
}
```

- 希尔排序算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(n)$|$O(n^2)$|$O(n^2)$|$O(1)$|不稳定|

- *希尔排序*

## 2. 高级排序算法

在这里，所谓的高级排序算法指归并排序、快速排序、堆排序这三类高效、常用的$O(nlogn)$级别排序算法，相对于前面介绍的基础排序算法也较难。

### 2.1 归并排序

- 核心思路：归并排序应用了**分治**思想，易于使用递归实现。将$nums[0:n+1]$不断二分，直至每个数组长度都为1（递归的触底递归条件），之后开始两两合并。
- 时间复杂度： 合并两个相邻子区间的操作时间复杂度为$O(n)$(参考力扣[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)双指针法实现)。对于$nums$而言，其总体发生的元素交换只有$O(n)$，再乘上递归的$O(logn)$时间，总体时间复杂度为$O(nlogn)$
- 递归实现(自顶向下)

```go
func mergeSort1(arr []int) []int {
    n := len(arr)
    // 自顶向下，将区间不断分解
    mergeSort(&arr, 0, n-1)
    return arr
}

func _mergeSort(arr *[]int, start, end int) {
    // 退出条件
    if start > end {return}

    // 二分 递归
    mid := start + (end - start) / 2
    _mergeSort(arr, start, mid)
    _mergeSort(arr, mid+1, end)
    // 优化 合并结果
    if (*arr)[mid] > (*arr)[mid+1] {
        _merge(arr, start, mid, end)
    }
}

// 合并两个有序数列 A : arr[start:mid+1] 和 B : arr[mid+1:end+1]
func _merge(arr *[]int, start, mid, end int) {
	// 复制一份数据
	copyArr := make([]int, end-start+1)
	copy(copyArr, (*arr)[start:end+1])

	l := start	// A的指针
	k := mid+1  // B的指针
	pos := start	// 待修改位置

	// 合并两个有序序列
	for pos <= end {
		// 处理pos位置数据如何设置
		if l>mid {
			(*arr)[pos] = copyArr[k-start]
			k++
		} else if k > end {
			(*arr)[pos] = copyArr[l-start]
            l++
            // 下面这两句就是 双指针法的指针移动，前面两句是当其中一个子数组已经遍历完了的情况处理
		} else if copyArr[l-start] <= copyArr[k-start] {
			l++
		} else {
			k++
		}
		// pos后移
		pos++
	}
}
```

- 优化： (1) 当子区间较小时，可以认为序列逆序度较低，可以使用插入排序及时停止比较；(2)在区间二分之前准备好一个空间足够的数组传进去，用于合并操作的数组备份，这样可以避免多次开辟数组空间。(下面的实现并没有应用第二条优化)

```go

func _mergeSort(arr *[]int, start, end int) {
    // 退出条件
    if start > end {return}

    ///////////////////////////////////

    // 如果区间长度<15，数列基本有序的概率较大，插入排序比较适合
	if end-start<=15 {
		_insertSort(arr, start, end); return
    }

    ///////////////////////////////////

    // 二分 递归
    ......
}

/////////////////////////////////////////

func _insertSort(arr *[]int, start, end int) {
    curNum, j := 0, 0
    for i:=start+1; i<=end; i++ {
        curNum, j = (*arr)[i], i
        for ; j>=start+1; j-- {
            if curNum < (*arr)[j-1] {
                (*arr)[j] = (*arr)[j-1]
            } else {break}
        }
        (*arr)[j-1] = curNum
    }
}

/////////////////////////////////////////
```

- 自底向上实现

```go
func mergeSort2(arr []int) []int {
	n := len(arr)
	// size为子区间长度，初始为1
	size := 1

	// 自底向上
	for size <= n {
		for i:=0; i<n-size; i+=size+size {
            // end这么设置是为了处理最后一个分块长度不足的情况
            // _merge函数代码见前面递归实现
			_merge(&arr, i, i+size-1, min(i+size+size-1, n-1))
		}
		size += size
	}

	return arr
}

func min(a, b int) int {
	if a<=b {return a} else {return b}
}
```

- 归并排序算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(nlogn)$|$O(nlogn)$|$O(nlogn)$|$O(n)$|可以稳定|

- *归并排序的时间复杂度比较稳定，各种情况下时间复杂度都为$O(nlogn)$，而用的最多的快速排序在最坏情况下会退化成$O(n^2)$。归并排序没有快速排序使用广泛的原因在于：其不是原地排序算法，对于大数据量的排序，有时不能忍受*
- *递归的空间复杂度不能像递归时间复杂度那样计算，即便不进行空间优化，每次都创建新的数组，总体空间复杂度也是O(n)，因为用完数组就回收了(取决于语言，但分析空间复杂度时可以认为回收了)*

### 2.2 快速排序

- 核心思路：快速排序应用分治思想，是一种原地不稳定排序算法。

### 2.3 堆排序


## 其他参考材料

- [这或许是东半球讲十大排序算法最好的一篇文章](https://zhuanlan.zhihu.com/p/68672733)