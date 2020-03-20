---
title: "排序问题集锦"
date: 2020-03-20T10:51:13+08:00
draft: false
categories: ["algo"]
tags: ["排序"]
keywords: ["排序问题"]
---

## 0. 排序算法

见 **排序算法整理与实现**

## 1. 快排思想类

求第k小(大)，前k小(大)问题能实现O(n)复杂度。

### 1.1 数组中的第k大元素

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

### 1.2 最小的k个数

[LeetCode 《剑指offer》 面试题40](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof)

```go
// 输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

 

// 示例 1：

// 输入：arr = [3,2,1], k = 2
// 输出：[1,2] 或者 [2,1]
// 示例 2：

// 输入：arr = [0,1,2,1], k = 1
// 输出：[0]
 

// 限制：

// 0 <= k <= arr.length <= 10000
// 0 <= arr[i] <= 10000

/////////////////////////////////////////////


// 思考：
// 1. 先整体排序，再取前k小 O(nlogn)。
// 解法1作了额外的工作，因此可以进一步优化
// 2. 使用基于快排的分治解法，只将前k小的数拍好位置，其他不管。 O(nlogk)，
// 解法2还是作了额外功，题目只要求找前k小，不要求前k小有序
// 3. 快排思路找第k小，并且同时也已经找到了比第k小还小的所有数。 O(n)
// 解法3应该是最优解了
//
// 还有一些其他思路：
// 4. 堆 O(nlogk)
// 5. 对元素上下限进行二分法。O(nlogw)，w为最大值最小值差值

func getLeastNumbers(arr []int, k int) []int {
	n := len(arr)
	if n < k || k == 0 { // 测例有k=0的情况
		return nil
	}
	// 前k小元素
	return quick(arr, 0, n-1, k)
}

func quick(arr []int, l, r, k int) []int {
	// 快排思想
	pivot := rand.Intn(r-l+1) + l
	arr[l], arr[pivot] = arr[pivot], arr[l] // 基准交换至开头
	p := partition(arr, l, r)
	if p == k-1 { // 第k小，意味着排好序后位置是k-1。 并且第k小左边都小于第k小
		return arr[:k]
	} else if p > k-1 {
		return quick(arr, l, p-1, k)
	} else { // p<k
		return quick(arr, p+1, r, k)
	}
}

// 将arr分区，返回分区点下标。（由于是升序处理，分区点下标考虑为Less区间末尾）
func partition(arr []int, l, r int) int {
	p := l                        // 返回的分界点
	for i := l + 1; i <= r; i++ { // 循环体内代码可以简写，现在只是为了让意思清晰而已
		if arr[i] >= arr[l] { // 不动
			continue
		}
		if arr[i] < arr[l] { // 同arr[p+1]交换，并且i后移，p后移
			arr[i], arr[p+1] = arr[p+1], arr[i]
			p++
		}
	}
	// 交换arr[l]和arr[p]
	arr[l], arr[p] = arr[p], arr[l]
	return p
}

```