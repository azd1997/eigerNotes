---
title: "排序算法整理与实现"
date: 2020-02-23T19:01:46+08:00
draft: false
categories: ["algo"]
tags: ["sort"]
keywords: ["bubble sort", "select sort", "insert sort", "shell sort", "merge sort", "quick sort", "heap sort"]
---

## 0. 排序

### 0.1 度量指标

评价一种排序算法，主要看以下几种因素：

- 时间复杂度：分最好、最坏、平均时间复杂度(当然还有个摊还时间复杂度)
- 空间复杂度: O(1)空间的排序称作原地排序
- 稳定性: 对于"相等"的元素之间相对位置不改变

### 0.2 特别说明

- 以下描述中出现的区间$[a:b]$遵循**左闭右开**原则
- 以下排序按升序排序介绍，降序和升序原理一致
- 依赖线性遍历的排序算法可以从左边开始也可以从右边开始，没有定式
- $nums$表示待排序数组，$n$为其长度

### 0.3 介绍

- 前三种为基础排序算法，包括冒泡排序。选择排序和插入排序，这三种算法时间复杂度都是$O(n^2)$，并且都是原地排序算法。
- 第四种希尔排序，是插入排序的优化版。
- 归并排序、快速排序、堆排序则是最常用的三类排序算法，它们的时间复杂度都能做到$O(nlogn)$
- 特殊场景下，还可以使用计数排序、基数排序、桶排序以获得更高的性能($O(n^2)$)

## 1. 冒泡排序

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

## 2. 选择排序

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

## 3. 插入排序

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

        // [1,2,4], 待插入3
        //               j      arr[j] < arr[j-1] j->j-1到了元素4的位置上
        //      j               arr[j] >= arr[j-1] break
        // 因此，最后应该是 arr[j] = curNum

        arr[j] = curNum   // 把curNum放到前面找到的合适位置上
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

## 4. 希尔排序

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
        for ; j>=start+gap; j-- {   // j要 >= 起始位置
            if curNum < (*arr)[j-1] {
                (*arr)[j] = (*arr)[j-1]
            } else {break}
        }
        (*arr)[j] = curNum
    }
}
```

- 希尔排序算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(n^{3/2})$|$O(n^2)$|$O(n^2)$|$O(1)$|不稳定|

- *希尔排序*

## 4. 归并排序

- 核心思路：归并排序应用了**分治**思想，易于使用递归实现。将$nums[0:n+1]$不断二分，直至每个数组长度都为1（递归的触底递归条件），之后开始两两合并。
- 时间复杂度： 合并两个相邻子区间的操作时间复杂度为$O(n)$(参考力扣[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)双指针法实现)。对于$nums$而言，其总体发生的元素交换只有$O(n)$，再乘上递归的$O(logn)$时间，总体时间复杂度为$O(nlogn)$

### 4.1 递归实现(自顶向下)

```go
func mergeSort1(arr []int) []int {
    n := len(arr)
    // 自顶向下，将区间不断分解
    mergeSort(&arr, 0, n-1)
    return arr
}

func _mergeSort(arr *[]int, start, end int) {
    // 退出条件(区间长度<2) 其实这里写 start == end 也完全ok
    if start >= end {return}

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
            (*arr)[pos] = copyArr[l-start]
			l++
		} else {
            (*arr)[pos] = copyArr[k-start]
			k++
		}
		// pos后移
		pos++
	}
}
```

### 5.2 归并排序优化点

- 优化：
  - (1) 当子区间较小时，可以认为序列逆序度较低，可以使用插入排序及时停止比较；
  - (2) 在区间二分之前准备好一个空间足够的数组传进去，用于合并操作的数组备份，这样可以避免多次开辟数组空间。(下面的实现并没有应用第二条优化)
  - (3) 在合并阶段只有`(*arr)[mid] > (*arr)[mid+1]`才进行merge(不然两个相邻子区间本身合并就是有序的)，上面的实现中已经使用了这一点优化

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
        (*arr)[j] = curNum
    }
}

/////////////////////////////////////////
```

### 5.3 自底向上实现

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

### 5.4  性能指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(nlogn)$|$O(nlogn)$|$O(nlogn)$|$O(n)$|可以稳定|

- *归并排序的时间复杂度比较稳定，各种情况下时间复杂度都为$O(nlogn)$，而用的最多的快速排序在最坏情况下会退化成$O(n^2)$。归并排序没有快速排序使用广泛的原因在于：其不是原地排序算法，对于大数据量的排序，有时不能忍受*
- *递归的空间复杂度不能像递归时间复杂度那样计算，即便不进行空间优化，每次都创建新的数组，总体空间复杂度也是O(n)，因为用完数组就回收了(取决于语言，但分析空间复杂度时可以认为回收了)*

### 5.5 练习

- [Leetcode 面试题51. 数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

        题目描述：
        在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

        示例 1:
        输入: [7,5,6,4]
        输出: 5
         
        限制：0 <= 数组长度 <= 50000

题目分析：
1. 最直接的做法就是两层遍历统计每个元素所参与的数对的逆序对总数。 $O(n^2)/O(1)$
2. 利用归并排序的思想：

过程与归并排序一致(不需要加上插入排序优化)，需要小心的是，搞明白什么时候加逆序对数，加多少？
   - 方案一： 左右区间均还有待出列元素且右侧区间元素出列时，逆序对数直接加上**左区间未出列**元素数。(下方代码)
   - 方案二： 左右区间均还有待出列元素且左侧区间元素出列时，逆序对数直接加上**右区间已出列**元素数；并且当右区间遍历完全后左区间剩余元素每一次出列，逆序数都要加上右区间长度

```go
// 基于归并排序思想：在归并排序的过程中统计逆序对数 O(nlogn)/O(n)
func reversePairs(nums []int) int {
	// 边界情况
	n := len(nums)
	if n < 2 {return 0}

	// 准备一个数组，给每次合并使用
	numsCopy := make([]int, n)

	return _mergeSort(&nums, &numsCopy, 0, n-1)
}

// 归并排序辅助函数。返回这一轮发现的逆序对数
func _mergeSort(nums, numsCopy *[]int, l, r int) int {
	if l >= r {return 0}

	// 区间二分
	mid := (r-l) / 2 + l
	a := _mergeSort(nums, numsCopy, l, mid)
	b := _mergeSort(nums, numsCopy, mid+1, r)

	// 合并。 合并过程中也会发现逆序对
	c := 0
	if (*nums)[mid] > (*nums)[mid+1] {      // 否则两个区间直接组合就是有序的，不存在逆序对
		c = _merge(nums, numsCopy, l, mid, r)
	}

	return a+b+c    // 返回逆序对总数
}

// merge阶段，返回merge发现的逆序对数
func _merge(nums, numsCopy *[]int, l, mid, r int) int {
	// merge阶段实际是对两个子区间采用双指针的方式来O(n)排序合并

	// NOTICE: 左右区间内部已经是升序状态，不存在逆序对
	// 这个merge阶段是为了求合并时发现的逆序对数

	// 备份当前左右区间
	copy((*numsCopy)[:r-l+1], (*nums)[l:r+1])

	total := 0      // 当前左右区间合并时统计得到的逆序对数

	// 这种情况下从前向后和从后向前移动没区别，这里从前向后
	p := l
	p1, p2 := l, mid+1
	for ; p<=r; p++ {    // p走到r就结束

		// p1先走到底，则后边依次填充右区间剩下的部分，逆序对不需要增加
		if p1 > mid {
			(*nums)[p] = (*numsCopy)[p2-l]
			p2++
		} else if p2 > r {
			(*nums)[p] = (*numsCopy)[p1-l]
			p1++
			// 都没走到底的比较(ps: 针对大量重复元素，还可以利用二分查找优化，快速越过重复地带，这里按下不表)
		} else if (*numsCopy)[p1-l] > (*numsCopy)[p2-l] {
			// 这样排序依然是稳定的，且满足题目要求： (前 > 后) 称为一个逆序对
			(*nums)[p] = (*numsCopy)[p2-l]		// TODO： 右区间每出一个元素，都意味着左区间剩下的还没出列的元素与其构成逆序对。并且当右区间用完后，左区间剩下的元素无需再叠加逆序对数，因为已被统计掉了
			total += mid+1-p1     // 发现一个逆序对
			// 调试用
			for i:=p1; i<=mid; i++ {
				fmt.Printf("total=%d, 新增逆序对 <%d, %d>\n", total, (*numsCopy)[i-l], (*numsCopy)[p2-l])
			}
			p2++
		} else {
			(*nums)[p] = (*numsCopy)[p1-l]
			p1++
		}
	}

	return total
}

```

- 参考题解：<https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/bao-li-jie-fa-fen-zhi-si-xiang-shu-zhuang-shu-zu-b/>

## 6. 快速排序

### 6.1 普通快排

- 核心思路：快速排序应用分治思想，是一种原地不稳定排序算法。
  - **Partition**（分区）:
    1. 每次选择数组中一个元素作为**基数**(暂且取数组第一个元素$nums[0]$作为基数$base$)
    2. 向后遍历时，对于$nums[i]$，小于$base$则放到靠近$base$的那一片(这一片的数都比$base$小)，否则放到保持位置不变(也就是放在靠近$i$远离$base$的一侧)。
    3. 有一个指针$p$记录这两片区域的分界点。当一次遍历完成后，将$base$与$nums[p]$交换位置，这样整个数据就以$base$为界分成了两个区域$Left$和$Right$，$Left$的元素都小于$base$而$Right$都大于$base$
  - 接下来就进一步对$Left$和$Right$进行$Partition$操作。显然递归实现是非常适合的。当区间只剩一个元素或没有元素就递归终止。最终所有元素都排好了序
- 实现：

```go
// 递归实现快速排序
func quickSort(nums []int) {
    _quick(&nums, 0, len(nums)-1)
}

func _quick(nums *[]int, l, r int) {
    // 递归终止(没法再partition)
    if l >= r {return}

    // partition得到分界点
    p := _partition(nums, l, r)
    // 继续对左右进行递归处理
    _quick(nums, l, p-1)
    _quick(nums, p+1, r)
}

// 分区，返回基数最终插入的那个位置
// 在分区最后一步之前，p始终指向小于p的分区的最后一个
// 返回的p保证 nums[l:p] < nums[p] < nums[p+1:r+1]
func _partition(nums *[]int, l, r int) int {
    // 取区间第一个元素(也就是)作为基数

    // 满足 nums[l+1:p+1] < nums[l] <= nums[p+1:r+1]
    p := l
    for i:=l+1; i<=r; i++ {
        // 当前数大于nums[l]则保持原位不动，也就啥也不干
        // 当前数等于基数也划到右边分区

        // 当前数小于nums[l]，则将当前数与p+1位置(第一个大于基数的元素)的数交换，并将p后移
        if (*nums)[i] < (*nums)[l] {
            (*nums)[i], (*nums)[p+1] = (*nums)[p+1], (*nums)[i]
            p++
        }
    }
    (*nums)[l], (*nums)[p] = (*nums)[p], (*nums)[l]
    return p
}

```

### 6.2 随机化快排 + 插入排序优化

1. 在区间缩小到16时(较小区间时)，使用插入排序优化，这有利于在接近有序状态下提前排序结束
2. 另外一个非常重要的优化是**随机选取基数**而不是每次取区间首位元素。这样的快速排序叫**随机化快排**
    - 在归并排序中，之所以能够稳定的保证$O(nlogn)$复杂度，是因为其每次都是二分，递归树的高度保证为$h=log_2{n}$。但是在快速排序中每一次的$Partition$操作并不一定会将原区间二分，大多数情况下会出现$Left$和$Right$一长一短的情况，这种情况下递归树的高度就会大于$log_2{n}$，甚至当区间基数以外其他元素都比基数大或都比它小时，递归树会退化成链表，那么深度就变成$n$，最终的时间复杂度就会变成$O(n^2)$。
    - 已被证明的是：当随机选取基数时，能够确保快速排序的时间复杂度**期望值**为$O(nlogn)$，退化成$O(n^2)$的概率为$1 \times 1/2 \times ... \times 1/n$，近乎为$0$。

下面是优化之后的实现：

```go
// 递归实现快速排序，使用插入排序+随机化基数进行优化
func quickSort(nums []int) {
    _quick(&nums, 0, len(nums)-1)
}

func _quick(nums *[]int, l, r int) {
    // 递归终止(没法再partition)
    if l >= r {return}

    //////////////////////

    // 区间较小时使用插入排序
    if r - l <= 15 {
        _insertSort(nums, l, r)
    }

    //////////////////////

    // partition得到分界点
    p := _partition(nums, l, r)
    // 继续对左右进行递归处理
    _quick(nums, l, p-1)
    _quick(nums, p+1, r)
}

// 分区，返回基数最终插入的那个位置
// 在分区最后一步之前，p始终指向小于p的分区的最后一个
// 返回的p保证 nums[l:p] < nums[p] < nums[p+1:r+1]
func _partition(nums *[]int, l, r int) int {

    ////////////////////////////////////////////////////

    randIdx := rand.Intn(r-l+1) + l
            // rand.Int() % (r-l+1) + l
    (*nums)[randIdx], (*nums)[l] = (*nums)[l], (*nums)[randIdx]

    ////////////////////////////////////////////////////


    // 满足 nums[l+1:p+1] < nums[l] <= nums[p+1:r+1]
    p := l
    for i:=l+1; i<=r; i++ {
        // 当前数大于nums[l]则保持原位不动，也就啥也不干
        // 当前数等于基数也划到右边分区

        // 当前数小于nums[l]，则将当前数与p+1位置(第一个大于基数的元素)的数交换，并将p后移
        if (*nums)[i] < (*nums)[l] {
            (*nums)[i], (*nums)[p+1] = (*nums)[p+1], (*nums)[i]
            p++
        }
    }
    (*nums)[l], (*nums)[p] = (*nums)[p], (*nums)[l]
    return p
}

///////////////////////////

// 插入排序
func _insertSort(nums *[]int, l, r int) {
    // 边界


    // 每次都把当前数向左边查找插入位置并插入
    cur, j := 0, 0
    for i:=l+1; i<=r; i++ {
        cur, j = (*nums)[i], i
        for ; j>=l+1; j-- {
            if (*nums)[j-1] > cur {
                nums[j] = nums[j-1]     // 数据后移
            } else {    // 向左遇到 <=cur 的数就退出内层循环，这样保证了排序的稳定性
                break
            }
        }
        // 现在将nums[j]赋值为cur
        nums[j] = cur
    }
}

///////////////////////////


```

### 6.3 双路快排 (重复元素测例下的分区平衡优化)

- 继续来考虑一种测例： 长度为100000的数组，填充着0~9这10个数。这样的测例中包含了大量的重复元素。这会导致什么呢？
  - 无论是将相等的数挪到$Left$中还是挪到$Right$中（像前面代码那样），当存在大量重复元素时，都会导致$Left$和$Right$非常不平衡（一个很短一个很长），这样就使得快速排序时间复杂度又退化为接近$O(n^2)$甚至$O(n^2)$的程度。
  - 对于归并排序，这种情况可以被提前判断掉并不用继续merge
    ```go
    if (*arr)[mid] > (*arr)[mid+1] {
        _merge(arr, start, mid, end)
    }
    ```
  - 那么快排要怎么解决这个问题呢？
  - 答案是**双路快排**，既然重复元素集中于某一侧容易导致不平衡而退化，那么从首尾两端分两路快排，这样能将重复元素较均匀地分散到两侧。具体做法：
    1. 两个指针$i$, $j$用来指向左右两路快排移动过程中分区点，靠近区间左边界的一侧为$Left$，靠近区间右边界的为$Right$，中间为待处理元素们
    2. 对$i$来说，若$nums[i] < base$，那么$i$继续右移；直至$nums[i] \geq base$停住
    3. 对$j$来说，若$nums[j] > base$，那么$j$继续左移；直至$nums[j] \leq base$停住
    4. 当$i$和$j$都停住时，交换$nums[i]$与$nums[j]$，而后重复步骤2/3/4直至遍历结束($ij$错位)

```go
// NOTICE: 其余代码不变

// 双路快排分区
func _partition2(nums *[]int, l, r int) int {
    // 随机一个元素(也就是)作为基数base
    randIdx := rand.Intn(r-l+1) + l
    (*nums)[randIdx], (*nums)[l] = (*nums)[l], (*nums)[randIdx]
    // 现在base = nums[l]

    ////////////////////////////////////////////
    // 以下为修改过的代码

    // 满足 nums[l+1:i+1] <= nums[l] <= nums[j:r+1]
    i, j := l+1, r
    for {
        // i右移
        for i<=r && (*nums)[i]<(*nums)[l] {i++}
        // j左移
        for j>l && (*nums)[j]>(*nums)[l] {j--}
        // 是否已经遍历结束
        if i>j {break}
        // 交换i,j并继续移动
        (*nums)[i], (*nums)[j] = (*nums)[j], (*nums)[i]
        i, j = i+1, j-1
    }
    // 遍历结束后， i为从左向右看第一个 >=nums[l] 的元素
    // j 为i为从右向左看第一个 <=nums[l] 的元素
    // 因此 nums[l] 应该和 nums[j] 交换
    // 才能继续保证 base 左侧都 <=base， 右侧都 >= base
    (*nums)[l], (*nums)[j] = (*nums)[j], (*nums)[l]
    return j
}
```

### 6.4 三路快排 (重复元素测例下缩减待排序空间的优化)

- 思路
  - 对于前面的双路快排，其确保了即使区间内存在大量重复元素情况下左右分区的平衡性，也就是保证了与归并排序相似的稳定的$O(nlogn)$排序： 无论数组原先含有多少重复元素，都一视同仁，参与到归并的$merge$阶段或者是双路快排的$partition$阶段。
  - 但事实是: 快排的思想是每一轮最终都保证 $nums[l:p] \leq nums[p] \leq nums[p+1:r+1]$，然后再对$nums[l:p]$和$nums[p+1:r+1]$继续进行$partition$。这个结果**等价于**这个结论： **快速排序每一轮partition都将选出的基数$base$放置到了它最终应该在的位置!!!**
  - 应用这个结论，倘若基数存在若干重复，完全可以在$partition$阶段将他们都放到中间**最终应放置的位置**($p1$、$p2$)，从而接下去的递归只需要对$nums[l:p1]$和$nums[p2+1:r+1]$进行$partition$，这样就**缩小了每一次$partition$的待排序区间**（双路快排的$patition$区间长度为$len-1$，而三路快排为$len-(p2-p1+1)$）。
  - **$nums[l:p1] \leq nums[p1:p2+1] \leq nums[p2+1:r+1]$** ($partition$结束)
  - **$base \, \| \, nums[l+1:lt+1] \, \| \, nums[lt+1:i] \, | \, nums[i:gt] \, \| \, nums[gt:r+1]$**($partition$过程中)
  - $base$即为$nums[l]$，$lt$指向$Left$的右端点；$gt$指向$Right$的左端点；$lt+1$为$Mid$左端点；$i-1$为$Mid$右端点，$i$为当前处理的元素下标，$nums[i+1:gt]$为还没有处理的区间（$lt$代表$less \: than$， $gt$代表$greater \: than$）
  - 通常对于大数据集的排序，重复元素一般很多，虽然都是$O(nlogn)$时间复杂度，但利用三路快排可以甩归并和双路快排一条街...
- $partition$做法
  1. 每一次$partition$需要分成三个区间$Left$、$Right$、$Mid$，因此$partition$函数需要返回的是$Mid$区间的左右端点
  2. 初始状态 $lt = l, gt = r+1, i = l+1$ (保证初始时$Left$、$Right$、$Mid$均为空)
  3. 指针 $i$ 从 $l+1$ 开始向右移动
     1. 若$nums[i] > base$，则交换 $nums[i]$ 与 $nums[gt-1]$，$gt$左移， 新交换过来的 $i$ 仍待处理
     2. 若$nums[i] < base$，则交换 $nums[i]$ 与 $nums[lt+1]$，$i$右移，$lt$右移
     3. 若$nums[i] = base$， 则 $i$ 右移
     4. 最终 $i = gt$ 停止
  4. 交换 $nums[l]$ 与 $nums[lt]$。当前轮 $partition$ 结束。 $p1 = lt, p2 = gt-1$
- 实现

```go
// NOTICE: 其余代码一致，只修改 _quick() 和 _partition()

func _quick(nums *[]int, l, r int) {
    // 递归终止(没法再partition)
    if l >= r {return}
    // 区间较小时使用插入排序
    if r - l <= 15 {
        _insertSort(nums, l, r)
    }

    //////////////////////////////////

    // partition得到分界点
    p1, p2 := _partition(nums, l, r)
    // 继续对左右进行递归处理
    _quick(nums, l, p1-1)
    _quick(nums, p2+1, r)

    //////////////////////////////////
}

// 三路快排分区
func _partition3(nums *[]int, l, r int) (int, int) {
    // 随机一个元素(也就是)作为基数base
    randIdx := rand.Intn(r-l+1) + l
    (*nums)[randIdx], (*nums)[l] = (*nums)[l], (*nums)[randIdx]
    // 现在base = nums[l]

    ////////////////////////////////////////////
    // 以下为修改过的代码

    // 满足 nums[l+1:lt+1] < nums[lt+1:i-1] <= nums[gt:r+1]
    // 保证初始时 Left/Right/Mid 均为空
    lt, gt, i := l, r+1, l+1

    for i < gt {    // 如果i与gt相遇，遍历结束. 而且这个限制保证了 i 不会越界
        // 等于base时 i 右移
        if (*nums)[i] == (*nums)[l] {
            i++; continue
        }
        // 小于base i右移lt右移
        if (*nums)[i] < (*nums)[l] {
            (*nums)[i], (*nums)[lt+1] = (*nums)[l+1], (*nums)[i]
            i++; lt++; continue
        }
        // 大于base gt左移, 新交换过来的 i 仍需处理
        if (*nums)[i] > (*nums)[l] {
            (*nums)[i], (*nums)[gt-1] = (*nums)[gt-1], (*nums)[i]
            gt--; continue
        }
    }
    // 交换 l 与 lt
    (*nums)[l], (*nums)[lt] = (*nums)[lt], (*nums)[l]
    // 返回p1,p2. p1=lt, p2=gt-1
    return lt, gt-1
}
```

### 6.5 性能指标

- 快速排序算法的参数指标

|最好时间|最坏时间|平均时间|空间|稳定性|
|:-:|:-:|:-:|:-:|:-:|
|$O(nlogn)$|$O(n^2)$|$O(n^2)$|$O(1)$|不稳定|

### 6.6 分治思想

快速排序和归并排序都利用了分治思想。

- **分而治之**： 将原问题分割为同等结构的子问题，之后将子问题逐一解决，原问题也就得到了解决。
- 归并排序和快速排序代表了分治思想的两种思路：
  - 归并排序**分**是简单的二分，重点在**合**的步骤
  - 快速排序**合**是简单的区间连接(都不需要操作)，重点在于**分**的步骤

### 6.7 练习

- [数组中的第k大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

        在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

        示例 1:

        输入: [3,2,1,5,6,4] 和 k = 2
        输出: 5
        示例 2:

        输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
        输出: 4
        说明:

        你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

- 解答

```go
// 常规解法：
// 1. 排序后倒序遍历k   O(nlogn)/O(1) 快排  O(nlogn)/O(n) 归并 O(nlogk)/O(k) 堆排序
// 2. 二分查找: 先求数组最大值max和最小值min，取数值mid，看数组中比mid大的数有多少，若大于k则说明mid过小，将值区间缩小为[mid,max]继续二分 O(nlogn)/O(1)
// 3. 最优解：利用快速排序，快速排序的核心是每一次partition都将选取的基数放置到了最终位置上。利用这个选出的基数最终放置的位置与k的关系，可以缩减排序区间，实现 O(n)/O(1)的解法
// 解题时直接使用普通随机化快排，就不使用三路快排了。

// 普通快排 + 缩减排序空间
func findKthLargest(nums []int, k int) int {
    n := len(nums)
    if n < k {return -1}

    return _quick(&nums, n, k, 0, n-1)
}

func _quick(nums *[]int, n, k, l, r int) int {
    if l == r {return (*nums)[l]}   // 区间只剩一个元素，只可能是这个了

    p := _partition(nums, k, l, r)

    // 必然会遇到第k大元素，遇到了就没必要继续递归下去了
    if p == (n-k) {return (*nums)[p]}

    // 否则向靠近n-k侧递归
    if p < n-k {
        return _quick(nums, n, k, p+1, r)
    } else {    // > n-k
        return _quick(nums, n, k, l, p-1)
    }
}


func _partition(nums *[]int, k, l, r int) int {
    // 随机选取基数
    randIdx := rand.Intn(r-l+1) + l
    // 交换到最左边
    (*nums)[l], (*nums)[randIdx] = (*nums)[randIdx], (*nums)[l]

    p := l    // p记录Less与Right交界，p为Less右端点，包含在内
    // 向右遍历
    for i:=l+1; i<=r; i++ {
        if (*nums)[i] < (*nums)[l] {
            (*nums)[i], (*nums)[p+1] = (*nums)[p+1], (*nums)[i]
            p++ // p后移
        }
    }
    // 交换基数与Less末位，交换后p为基数所在
    (*nums)[l], (*nums)[p] = (*nums)[p], (*nums)[l]
    return p
}
```

## 7. 堆排序

### 7.1 练习

- 练习一： [数据流中的第k大元素](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)


## 其他参考材料

- [这或许是东半球讲十大排序算法最好的一篇文章](https://zhuanlan.zhihu.com/p/68672733)