---
title: "形形色色的树——线段树"
date: 2020-03-11T18:20:07+08:00
draft: false
categories: ["algo"]
tags: ["树"]
keywords: ["线段树"]
---

## 0. 导引

对于一个数组区间$arr$，如果要频繁获取其某个子区间的最大值，需要怎么做？

每次求子区间最大值都对该子区间做一次遍历？  维护$O(1)$查找$O(n)$

维护一颗树（或其他类型的符号表），树中所有可能的子区间都维护好其最大值，用时直接查。但是一旦修改了区间某个元素呢？ 又得从头构建这张符号表。维护$O(n)$查找$O(1)$

处理这样的**区间计算（区间和、最大值、最小值、平均值、中位数...）**并且数据可能会更新，这样的情形下，有这么一种数据结构： **线段树**，将查找和维护的时间复杂度折中到$O(logn)$

## 1. 线段树

线段树的实现可以是基于节点的形式，也可以基于数组构建。 

可以基于数组构建的原因是线段树虽然不是完全二叉树的形式，但浪费的空间也不大，接近于完全二叉树，可以使用数组存储树，对于叶节点不在树最底部的情况，可以给它分配两个虚结点，使得整棵树成为满二叉树。这样，可以根据完全二叉树的数组存储形式的下标转换来访问(以下标从0开始考虑)

$$left_node = 2 * node + 1$$
$$right_node = 2 * node + 2$$
$$parent = (cur-1) / 2 $$


实现：

```go
package tree

import (
	"math"
)

// 线段树

// 基于数组存储
// 这里以整数、区间求和为例，

// 数组长度：以满二叉树形式，取一个较大值，足够存储满二叉树

// MaxLen 最大长度，保证tree能完全存储一颗满二叉树（其实再小些，完全二叉树也行）
const MaxLen = 100

// SegmentTree 基于数组存储的线段树
type SegmentTree struct {
	arr  []int // 原数组
	tree []int // 线段树
}

// BuildTree 根据给定数组生成线段树
func BuildTree(arr []int) *SegmentTree {
	n := len(arr)
	if n == 0 {
		return nil
	}

	st := &SegmentTree{}
	st.arr = arr
	st.tree = make([]int, MaxLen)

	st.build(0, 0, n-1) // 从0这个结点开始（0是树顶）
	return st
}

// node 指当前节点的数组下标
func (st *SegmentTree) build(node, start, end int) {
	if start == end {
		st.tree[node] = st.arr[start] // 直接给当前节点赋值
		return
	}

	mid := (start + end) / 2
	leftnode, rightnode := 2*node+1, 2*node+2
	// 构建左孩子
	st.build(leftnode, start, mid)
	// 构建右孩子
	st.build(rightnode, mid+1, end)
	// 赋值当前结点
	st.tree[node] = st.tree[leftnode] + st.tree[rightnode]
}

// Query 查询
// 根据查询的区间上下界去寻找
func (st *SegmentTree) Query(l, r int) int {
	// 1. 检查区间有效性
	if l > r || l < 0 || r >= len(st.arr) {
		return math.MinInt32
	}

	return st.query(0, 0, len(st.arr)-1, l, r)
}

func (st *SegmentTree) query(node, start, end, l, r int) int {
	if l > end || r < start { // 没有重合
		return 0
	}
	if start >= l && end <= r { // 当前节点处在所求区间的内部，则直接返回当前节点的值就好
		return st.tree[node]
	}

	// 先获取左边部分的总和、右边部分的总和，再相加，返回
	mid := (start + end) / 2
	leftnode, rightnode := 2*node+1, 2*node+2
	sumLeft := st.query(leftnode, start, mid, l, r)
	sumRight := st.query(rightnode, mid+1, end, l, r)
	return sumLeft + sumRight
}

// Update 更新
// 根据idx寻找要修改的路径
func (st *SegmentTree) Update(idx, val int) {
	// 1. 修改arr
	if idx < 0 || idx >= len(st.arr) {
		return // idx无效
	}
	st.arr[idx] = val

	// 2. 更新tree
	st.update(0, 0, len(st.arr)-1, idx, val)
}

func (st *SegmentTree) update(node, start, end, idx, val int) {
	// 递归终止
	if start == end {
		st.tree[node] = val
		return
	}

	// 1. 先将下方的路径修改
	mid := (start + end) / 2
	leftnode, rightnode := 2*node+1, 2*node+2
	// 看idx是在node的左子树还是右子树
	if idx >= start && idx <= mid { // idx有效且在左子树
		st.update(leftnode, start, mid, idx, val)
	} else if idx <= end && idx > mid { // idx有效且在右子树
		st.update(rightnode, mid+1, end, idx, val)
	}
	// 2. 再修改当前节点
	st.tree[node] = st.tree[leftnode] + st.tree[rightnode]
}

```

## 3. 相关习题

- [654. 最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

解答：

```go
package lt654

import (
	"fmt"
	"math"
)

// 最大二叉树

type TreeNode struct {
	Val         int
	Left, Right *TreeNode
}

// 思考：
// 核心就是不断找最大值然后递归构建树
// 效率的差异体现在找最大值

// 低效的办法是，每次对区间进行线性遍历
// 高效的办法是线段树

// 1. 线性遍历求最大值 + 递归构建树
func constructMaximumBinaryTree(nums []int) *TreeNode {
	n := len(nums)
	if n == 0 {
		return nil
	}
	return help(nums, 0, n-1)
}

func help(nums []int, l, r int) *TreeNode {
	// 递归终止条件
	if l > r {
		return nil
	}
	if l == r {
		return &TreeNode{Val: nums[l]}
	}

	// 否则的话，构建当前结点及其左右孩子
	maxIdx := maxOfSlice(nums, l, r)
	cur := &TreeNode{Val: nums[maxIdx]}
	cur.Left = help(nums, l, maxIdx-1)
	cur.Right = help(nums, maxIdx+1, r)
	return cur
}

// 线性遍历求最大值，返回最大值的数组下标
func maxOfSlice(arr []int, l, r int) int {
	max := math.MinInt32
	maxIdx := -1
	for i := l; i <= r; i++ {
		if arr[i] > max {
			max = arr[i]
			maxIdx = i
		}
	}
	return maxIdx
}

// 2. 线段树求最大值 + 递归构建树
func constructMaximumBinaryTree2(nums []int) *TreeNode {
	n := len(nums)
	if n == 0 {
		return nil
	}

	// 构建线段树
	tree := buildSegmentTree(nums)

	fmt.Println("构建线段树成功")

	return help2(nums, tree, 0, n-1)
}

func help2(nums []int, tree [][2]int, l, r int) *TreeNode {
	// 递归终止条件
	if l > r {
		return nil
	}
	if l == r {
		return &TreeNode{Val: nums[l]}
	}

	// 否则的话，构建当前结点及其左右孩子
	max := querySegmentTree(nums, tree, 0, 0, len(nums)-1, l, r)
	cur := &TreeNode{Val: max[1]}
	cur.Left = help2(nums, tree, l, max[0]-1)
	cur.Right = help2(nums, tree, max[0]+1, r)
	return cur
}

// 线段树，非常适合对区间进行最大值最小值平均值等情况的快速计算。
// 这里直接以tree数组来表示。
// 这里的线段树存的是最大值的下标和最大值 [2]int
func buildSegmentTree(arr []int) [][2]int {
	n := len(arr)
	if n == 0 {
		return nil
	}

	tree := make([][2]int, 4*n)
	build(arr, tree, 0, 0, n-1)
	return tree
}

func build(arr []int, tree [][2]int, node, start, end int) {
	if start == end {
		tree[node] = [2]int{start, arr[start]}
		return
	}

	mid := (start + end) / 2
	leftnode, rightnode := 2*node+1, 2*node+2
	build(arr, tree, leftnode, start, mid)
	build(arr, tree, rightnode, mid+1, end)
	if tree[leftnode][1] > tree[rightnode][1] {
		tree[node] = tree[leftnode]
	} else {
		tree[node] = tree[rightnode]
	}
}

// 查询区间[l,r]，返回区间的最大值下标和最大值
func querySegmentTree(arr []int, tree [][2]int, node, start, end, l, r int) [2]int {
	fmt.Println(node, start, end)

	if l > end || r < start { // 当前区间在查询区间外
		return [2]int{-1, math.MinInt32}
	}
	if start >= l && end <= r { // 当前区间在查询区间内部
		return tree[node]
	}

	mid := (start + end) / 2
	leftnode, rightnode := 2*node+1, 2*node+2
	maxLeft := querySegmentTree(arr, tree, leftnode, start, mid, l, r)
	maxRight := querySegmentTree(arr, tree, rightnode, mid+1, end, l, r)
	if maxLeft[1] >= maxRight[1] {
		return maxLeft
	}
	return maxRight

}

```

## 4. 其他更深入的参考

- [线段树详解 （原理，实现与应用)](https://www.cnblogs.com/AC-King/p/7789013.html)