---
title: "形形色色的树——并查集"
date: 2020-03-17T21:24:52+08:00
draft: false
categories: ["algo"]
tags: ["树"]
keywords: ["并查集"]
---

## 0. 导论

并查集可以非常高效的解决**连接问题**

网络中节点间的连接状态
- 网络是个抽象的概念：
  - 用户之间形成的网络
  - 数据库中音乐、电影、书籍之间的网络
  - 铁路交通

- 数学中的集合类实现：并集

和连接问题相近的一个问题：**路径问题**

- 连接问题比路径问题回答的问题更少，因此可以设计更快的算法实现
  - 有序数组中顺序查找比二分查找慢，就是因为它不仅求出了target的位置，还求出了其他元素的位置
  - 堆之所以高效，也是因为堆只关心最大值最小值，而不关心其他

## 1. 并查集 Union Find

对于一组数据，主要支持两个操作：

- union(p,q)
- find(p)

也可以回答：
- isConnected(p,q)

基本数据表示


## 2. 基础并查集

```go
// UnionFindV1 第一版的并查集。 union操作时间复杂度O(n)有待改进
type UnionFindV1 struct {
	ids   []int // id数组，id相同的元素(注意这里所说的元素指的是数组下标)是“合并的”
	count int
}

// NewUnionFindV1 新建
func NewUnionFindV1(n int) *UnionFindV1 {
	ids := make([]int, n)
	for i := 0; i < n; i++ {
		ids[i] = i // 一开始每个元素对应的id都不相同，也就是都是孤立的
	}
	return &UnionFindV1{
		ids:   ids,
		count: n,
	}
}

// Find 找出元素p（下标p）对应的id O(1)
func (uf *UnionFindV1) Find(p int) int {
	if p < 0 || p >= uf.count {
		return -1 // 报错
	}
	return uf.ids[p]
}

// IsConnected 判断两个元素是否连接在一起 O(1)
func (uf *UnionFindV1) IsConnected(p, q int) bool {
	pf, qf := uf.Find(p), uf.Find(q)
	if pf == -1 || qf == -1 {
		return false
	}

	return pf == qf
}

// Union 把所有与p相连的元素（包括p）都改成与q相连 O(n)
func (uf *UnionFindV1) Union(p, q int) {
	pf, qf := uf.Find(p), uf.Find(q)
	if pf == -1 || qf == -1 {
		return // 异常
	}

	if pf == qf {
		return //已经连接在一起
	}
	// 不等的话，则修改p或者q的区域，这里改p所在的区域
	for i := 0; i < uf.count; i++ {
		if uf.ids[i] == pf {
			uf.ids[i] = qf
		}
	}

	return
}

```

## 3. 使用QuickUnion的并查集

```go

// V1版本的并查集采用的是相互连接的元素拥有相同的id，但是其union操作比较慢
// V2版本的并查集不再记录id，而是记录当前节点的父亲结点，union操作时直接将
// 两个节点的祖宗节点进行挂载（连接）即可（树形结构）
// 即便采用的树形结构，这里仍可以使用数组作为基本存储结构，结点指针即为数组下标

// 既然采用了树形结构，那么就需要考虑树高度的平衡性，这样才能降低时间复杂度。那么请看V3版本

// UnionFindV2 第二版的并查集。 union操作时间复杂度O(logn)，find O(logn)
type UnionFindV2 struct {
	parent []int // parent数组，同一连通区域的结点拥有同一个根节点/公共祖宗
	count  int
}

// NewUnionFindV2 新建
func NewUnionFindV2(n int) *UnionFindV2 {
	parent := make([]int, n)
	for i := 0; i < n; i++ {
		parent[i] = i // 一开始每个元素的parent就是自己，也就是都是孤立的
	}
	return &UnionFindV2{
		parent: parent,
		count:  n,
	}
}

// Find 找出元素p（下标p）对应的id O(1)
func (uf *UnionFindV2) Find(p int) int {
	if p < 0 || p >= uf.count {
		return -1 // 报错
	}
	// 不断向上寻找parent，返回当前节点p的祖宗
	for p != uf.parent[p] {
		p = uf.parent[p]
	}
	return p
}

// IsConnected 判断两个元素是否连接在一起 O(1)
func (uf *UnionFindV2) IsConnected(p, q int) bool {
	pf, qf := uf.Find(p), uf.Find(q)
	if pf == -1 || qf == -1 {
		return false
	}

	return pf == qf
}

// Union 把所有与p相连的元素（包括p）都改成与q相连 O(n)
func (uf *UnionFindV2) Union(p, q int) {
	pf, qf := uf.Find(p), uf.Find(q) // 各自的祖宗
	if pf == -1 || qf == -1 {
		return // 异常
	}

	if pf == qf {
		return //已经连接在一起
	}
	// 不等的话，则将p/q的祖宗合并
	uf.parent[pf] = qf // pf退让一步，让qf做了自己爹...
}

```

## 4. 使用size优化的并查集

```go
// 增加维持parent树平衡性的步骤

// UnionFindV3 第三版的并查集。 union操作时间复杂度O(logn)，find O(logn)
// 维护parent树的平衡性
type UnionFindV3 struct {
	parent []int // parent数组，同一连通区域的结点拥有同一个根节点/公共祖宗
	size   []int // 当前子树的结点数量
	count  int
}

// NewUnionFindV3 新建
func NewUnionFindV3(n int) *UnionFindV3 {
	parent := make([]int, n)
	for i := 0; i < n; i++ {
		parent[i] = i // 一开始每个元素的parent就是自己，也就是都是孤立的
	}
	size := make([]int, n)
	for i := 0; i < n; i++ {
		size[i] = 1 // 每个节点所在子树初始都含有自身
	}
	return &UnionFindV3{
		parent: parent,
		size:   size,
		count:  n,
	}
}

// Union 把所有与p相连的元素（包括p）都改成与q相连 O(n)
func (uf *UnionFindV3) Union(p, q int) {
	pf, qf := uf.Find(p), uf.Find(q) // 各自的祖宗
	if pf == -1 || qf == -1 {
		return // 异常
	}

	if pf == qf {
		return //已经连接在一起
	}
	// 不等的话，则将p/q的祖宗合并
	// 将小的（也就矮）接在大的那一片下面
	if uf.size[pf] > uf.size[qf] {
		uf.parent[qf] = pf
		uf.size[pf] += uf.size[qf] // 增加结点数
	} else {
		uf.parent[pf] = qf
		uf.size[qf] += uf.size[pf] // 增加结点数
	}
}

// 基于size(集合大小/子树结点数)的优化还是有些问题。

// 考虑p子树结点数很多，但树趋于扁平；而q子树节点数较少，但树趋于细长
// 按照V3的Union策略，就会使得q挂在p的下面，而这就比较不平衡

// 下一个版本考虑基于层数或者说树高度的优化
```

## 5. 使用rank优化的并查集

```go
// UnionFindV4 第四版的并查集。 union操作时间复杂度O(logn)，find O(logn)
// 维护parent树的平衡性。 基于rank(秩)的优化。 rank[p]表示p子树的高度
type UnionFindV4 struct {
	parent []int // parent数组，同一连通区域的结点拥有同一个根节点/公共祖宗
	rank   []int // 当前子树的高度/当前节点的秩
	count  int
}

// NewUnionFindV4 新建
func NewUnionFindV4(n int) *UnionFindV4 {
	parent := make([]int, n)
	for i := 0; i < n; i++ {
		parent[i] = i // 一开始每个元素的parent就是自己，也就是都是孤立的
	}
	rank := make([]int, n)
	for i := 0; i < n; i++ {
		rank[i] = 1 // 每个节点所在子树初始都含有自身
	}
	return &UnionFindV4{
		parent: parent,
		rank:   rank,
		count:  n,
	}
}

// Union 把所有与p相连的元素（包括p）都改成与q相连 O(n)
func (uf *UnionFindV4) Union(p, q int) {
	pf, qf := uf.Find(p), uf.Find(q) // 各自的祖宗
	if pf == -1 || qf == -1 {
		return // 异常
	}

	if pf == qf {
		return //已经连接在一起
	}
	// 不等的话，则将p/q的祖宗合并
	// 将小的（也就矮）接在大的那一片下面
	if uf.rank[pf] > uf.rank[qf] {
		uf.parent[qf] = pf
	} else if uf.rank[pf] < uf.rank[qf] {
		uf.parent[pf] = qf
	} else { // ==
		uf.parent[pf] = qf // pf让一步，挂在qf下面
		uf.rank[qf]++
	}
}
```

## 6. 路径压缩的并查集

```go

// 前面V2/V3/V4都是在不断优化union操作，但其实find操作也是可以优化的
// 在V4版本的树形结构下，find操作需要从当前结点不断向上寻找到祖宗节点
// 如果途中没有其他分支或分支较少，也就是说树趋于细长，
// 那么相当于白向上跑这么久，没有分支，就意味着不需要检查这么多嘛

// 例如
// 4 -> 3 -> 2 -> 1 -> 0 |
// find(4)需要向上找四步
// 但其实我们可以想像要是每次能多走几步并且将细长的树变得矮胖就好了

// 对于4，先向上“跳两步”接在2下面，与3成为兄弟；
// 接着再将4的这颗子树向上跳2步，接在了0下面。
// 这样树变成了：

// 			0
// 		   / \
//        1   4
//           / \
//          3   2

// 树的高度就变矮了

// 这个策略叫做路径压缩
// 核心在于每次向上跳两步，跳三/四/更多步当然也是可以的。
// 因为最后祖宗节点一直都指向自己，所以向上跳不会跳出子树范围。

// UnionFindV5 第五版的并查集。增加路径压缩的优化，只需要在find时增加一句代码
type UnionFindV5 struct {
	parent []int // parent数组，同一连通区域的结点拥有同一个根节点/公共祖宗
	rank   []int // 当前子树的高度/当前节点的秩
	count  int
}

// Find 找出元素p（下标p）对应的id O(1)
func (uf *UnionFindV5) Find(p int) int {
	if p < 0 || p >= uf.count {
		return -1 // 报错
	}
	// 不断向上寻找parent，返回当前节点p的祖宗
	for p != uf.parent[p] {
		// find的同时将细长的树拉得矮胖
		uf.parent[p] = uf.parent[uf.parent[p]] // 向上跳两步，并将树变矮
		p = uf.parent[p]
	}
	return p
}

// 既然能将树的高度变矮，为什么不直接在find的过程中将所有结点都接在子树的根节点下面呢？
// 例如

// 			0
// 		  /| |\
//       1 2 3 4

// 这样就需要递归操作了，见V6版本
```

## 7. 进一步路径压缩的并查集

```go

// UnionFindV6 第六版的并查集。增加路径压缩的优化，只需要在find时增加一句代码
type UnionFindV6 struct {
	parent []int // parent数组，同一连通区域的结点拥有同一个根节点/公共祖宗
	rank   []int // 当前子树的高度/当前节点的秩
	count  int
}

// Find 找出元素p（下标p）对应的id O(1)
func (uf *UnionFindV6) Find(p int) int {
	if p < 0 || p >= uf.count {
		return -1 // 报错
	}
	// 不断向上寻找parent，返回当前节点p的祖宗
	// for p != uf.parent[p] {
	// 	// find的同时将细长的树拉得矮胖
	// 	uf.parent[p] = uf.parent[uf.parent[p]] // 向上跳两步，并将树变矮
	// 	p = uf.parent[p]
	// }
	// return p

	// 新的路径压缩做法：递归的将当前节点的父亲不断上移，
	// 直至当前节点的父亲结点为原来的祖宗节点
	if p != uf.parent[p] {
		uf.parent[p] = uf.Find(uf.parent[p])
	}
	return uf.parent[p]
}
```

## 8. 最终版本的并查集完整代码

```go

// UnionFindV6 第六版的并查集。增加路径压缩的优化，只需要在find时增加一句代码
type UnionFindV6 struct {
	parent []int // parent数组，同一连通区域的结点拥有同一个根节点/公共祖宗
	rank   []int // 当前子树的高度/当前节点的秩
	count  int
}

// NewUnionFindV6 新建
func NewUnionFindV6(n int) *UnionFindV6 {
	parent := make([]int, n)
	for i := 0; i < n; i++ {
		parent[i] = i // 一开始每个元素的parent就是自己，也就是都是孤立的
	}
	rank := make([]int, n)
	for i := 0; i < n; i++ {
		rank[i] = 1 // 每个节点所在子树初始都含有自身
	}
	return &UnionFindV6{
		parent: parent,
		rank:   rank,
		count:  n,
	}
}

// Find 找出元素p（下标p）对应的id O(1)
func (uf *UnionFindV6) Find(p int) int {
	if p < 0 || p >= uf.count {
		return -1 // 报错
	}

	// 新的路径压缩做法：递归的将当前节点的父亲不断上移，
	// 直至当前节点的父亲结点为原来的祖宗节点
	if p != uf.parent[p] {
		uf.parent[p] = uf.Find(uf.parent[p])
	}
	return uf.parent[p]
}

// IsConnected 判断两个元素是否连接在一起 O(1)
func (uf *UnionFindV6) IsConnected(p, q int) bool {
	pf, qf := uf.Find(p), uf.Find(q)
	if pf == -1 || qf == -1 {
		return false
	}

	return pf == qf
}

// Union 把所有与p相连的元素（包括p）都改成与q相连 O(n)
func (uf *UnionFindV6) Union(p, q int) {
	pf, qf := uf.Find(p), uf.Find(q) // 各自的祖宗
	if pf == -1 || qf == -1 {
		return // 异常
	}

	if pf == qf {
		return //已经连接在一起
	}
	// 不等的话，则将p/q的祖宗合并
	// 将小的（也就矮）接在大的那一片下面
	if uf.rank[pf] > uf.rank[qf] {
		uf.parent[qf] = pf
	} else if uf.rank[pf] < uf.rank[qf] {
		uf.parent[pf] = qf
	} else { // ==
		uf.parent[pf] = qf // pf让一步，挂在qf下面
		uf.rank[qf]++
	}
}
```

## 9. 复杂度

最终版本的并查集时间复杂度分析很困难。直接给出结论：

- Find/Union/IsConnected操作时间复杂度**近乎O(1)**