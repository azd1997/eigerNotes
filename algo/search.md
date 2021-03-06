---
title: "二分查找与二叉搜索树"
date: 2020-02-28T08:56:41+08:00
draft: false
categories: ["algo"]
tags: ["search"]
keywords: ["二分查找", ""]
---

## 0. 说明

- 结点： 在数据结构中的结点的正确表示是`结点`而非`节点`。以下懒得修正

## 1. 二分查找

### 1.1 普通二分查找

一般来讲，二分查找要求序列有序。下面是一个示例：

```go
// 1. 迭代版本

// 返回数组中是否存在目标值，假设元素均不重复/数组升序
func binarySearch(nums []int, target int) bool {
    // 边界检查
    n := len(nums)
    if n == 0 {return false}
    if n == 1 {return nums[0] == target}
    if nums[0] > target || nums[n-1] < target {return false}

    // 二分
    l, r := 0, n-1      // 闭区间 [0,...,n-1]
    for l <= r {    // l==r时就是只有一个元素
        mid := (r-l)/2 + l  // 虽然我觉得正常情况下不可能溢出

        // 找到目标值
        if nums[mid] == target {
            return true
        }

        // 没找到
        if nums[mid] > target {
            r = mid - 1     // [0,...,mid-1]    mid已比较过
        } else {
            l = mid + 1     // [mid+1,...,n-1]
        }
    }

    return false
}


// 2. 递归版本

func binarySearch2(nums []int, target int) bool {
    // 边界检查
    n := len(nums)
    if n == 0 {return false}
    if n == 1 {return nums[0] == target}
    if nums[0] > target || nums[n-1] < target {return false}

    // 递归
    return bs(nums, 0, n-1, target)
}

func bs(nums []int, l,r, target int) bool {
    if l == r {return nums[l] == target}

    mid := (r-l)/2 + l
    if nums[mid] == target {
        return true
    }
    if nums[mid] > target {
        return bs(nums, l, mid-1, target)
    } else {
        return bs(nums, mid+1, r, target)
    }
}

```

- **一些特殊的情况下非有序序列也可以使用二分查找**
  - 按数组下标进行二分查找
  - 按数值大小二分查找

### 1.2 floor与ceil

- floor 与 ceil 函数能够在含有重复元素的有序序列中查找出等于target的那一端数据的左端点和右端点
- 如果没有target，则会返回target最接近的左右两个数

## 2. 二分搜索树

### 2.1 二分搜索树基本实现

以下是一个不含重复元素的二叉搜索树实现

```go
package tree

import "reflect"

// Value 必须是可比较的，这里不做检查和约束
// 值。 空接口，类似泛型的方案
type Value interface{}

// compare 辅助函数，用来比较两个Value
// 0 相等
// 1 左大于右
// -1 右大于左
// -2 类型不一致
// -3 类型不可比较
// -4 未知Kind
func compare(v1, v2 Value) int {
	v1Value := reflect.ValueOf(v1)
	v2Value := reflect.ValueOf(v2)

	// 必须检查两个Value类型一致
	if v1Value.Type() != v2Value.Type() {
		return -2
	}

	//  可比较性
	if !v1Value.Type().Comparable() {
		return -3
	}

	// 比较
	switch v1Value.Kind() {
	case reflect.Int:
		if v1Value.Int() > v2Value.Int() {
			return 1
		} else if v1Value.Int() < v2Value.Int() {
			return -1
		} else {return 0}
	default:
		// 不知名的Kind报错
		return -4
	}
}

// Node 二叉搜索树节点
type Node struct {
	Val Value
	Left, Right *Node
}

// 二叉树节点该有的一些方法

// 1. 非空
// 2. 大小
// 3. 插入/更新
// 4. 按值删除
// 5. 包含

// BST 二叉搜索树
type BST struct {
	root *Node
	size int
}

// NewBST 新建空树
func NewBST() *BST {
	return &BST{}
}

// NewBSTFromSlice 根据切片新建二叉搜索树
func NewBSTFromSlice(data []Value) *BST {return nil}

// IsEmpty 判空
func (bst *BST) IsEmpty() bool {
	return bst.size == 0	// bst.root == nil 也可以
}

// Size 元素数量
func (bst *BST) Size() int {
	return bst.size
}

// Add 插入
// 不断将v与树原有节点进行比较，判断是否应该去其左右子树继续寻找（联系二分查找的思想）
func (bst *BST) Add(v Value) {
	// 树空
	if bst.IsEmpty() {bst.root.Val = v}

	// 递归寻找插入位置
	newRoot := add(bst.root, v)
	if newRoot == nil {return}	// 插入失败
	bst.root = newRoot
}

// 返回插入后树的新根（因为根可能会改变）
func add(root *Node, v Value) *Node {
	// 空节点，也就是找到可插入位置
	if root == nil {return &Node{Val:v}}
	// 发现重复元素
	if root.Val == v {return root}

	// 根据比较结果向左右子树试探
	cmp := compare(root.Val, v)
	switch cmp {
	case 1:
		root.Left = add(root.Left, v)
	case -1:
		root.Right = add(root.Right, v)
	default:
		// 其他结果都是不应当的
		return nil	// 应当报错
	}

	return root		// 返回当前根节点
}

// Del 按值删除
// 有三种情况
// 待删除节点为叶节点； 为不完全节点； 为具备两个孩子的完全节点
func (bst *BST) Del(v Value) {
	// 记得接收返回值，root可能更改
	bst.root = findAndDel(bst.root, v)
}

// 删除root子树中等于该v的节点。 返回新root
func findAndDel(root *Node, v Value) *Node {
	// 外层先是遍历框架。 找到待删除节点之后再根据待删除节点的情况去处理

	if root.Val == v {
		// 找到待删除节点.进行删除操作
		return del(root)
	}
	cmp := compare(root.Val, v)
	switch cmp {
	case 1:
		return findAndDel(root.Left, v)
	case -1:
		return findAndDel(root.Right, v)
	default:
		return root		// 返回root，删除失败
	}
}

// 删除当前节点， 返回顶替的节点（后继节点）
func del(cur *Node) *Node {
	// 情况1 cur为叶子节点，直接用nil节点顶替（注意：事实上情况1会被情况2处理掉）
	if cur.Left == nil && cur.Right == nil {return nil}
	// 情况2 cur为不完全节点，用不为空的孩子顶替
	if cur.Left == nil {return cur.Right}
	if cur.Right == nil {return cur.Left}
	// 情况3 cur为完全节点
	//  这种情况下需要找到左子树最大节点或者右子树最小节点来顶替自己
	//  也就是找自己数值相邻的两个之一顶替
	// 	找到后继者之后，返回后继者并且将原后继者位置删除
	// 方便的做法是直接将后继者值赋给cur，然后删除后继者
	// 这里采用右子树最小节点顶替（沿右子树左侧链路到底）
	rightMin := getMinNode(cur.Right)
	cur.Val = rightMin.Val
	// 看似是递归调用，其实由于rightMin是叶子或者不完全节点，一遍就删掉了
	cur.Right = findAndDel(cur.Right, rightMin.Val)
	return cur
}

func getMinNode(root *Node) *Node {
	for root.Left != nil {root = root.Left}
	return root
}

// Contains 包含
func (bst *BST) Contains(v Value) bool {
	return contains(bst.root, v)
}

func contains(root *Node, v Value) bool {
	// 递归终止
	if root == nil {return false}
	if root.Val == v {return true}
	// 否则类似二分，进入相应子树搜寻
	cmp := compare(root.Val, v)
	switch cmp {
	case 1:
		return contains(root.Left, v)
	case -1:
		return contains(root.Right, v)
	default:	// 报错,这里简单的返回false
		return false
	}
}

////////////////////////////////////////////

// AddOne 所有节点加1（假设Value存int，这里只作演示）
//  选择某一种遍历方式，注意使用标记避免重复加1
func (bst *BST) AddOne() {
	addOne(bst.root)
}

// 前序遍历,不要额外标记
func addOne(root *Node) {
	// 边界
	if root == nil {return}
	// 当前+1
	root.Val = root.Val.(int) + 1
	// 左子树
	addOne(root.Left)
	// 右子树
	addOne(root.Right)
}

// Equals 判断两棵树相同
// 同样也是选择一种遍历方式
func (bst *BST) Equals(bst2 *BST) bool {
	return equals(bst.root, bst2.root)
}

func equals(root1, root2 *Node) bool {
	// 都空
	if root1 == nil && root2 == nil {return true}
	// 有一个空
	if (root1 == nil && root2 != nil) || (root1 != nil && root2 == nil) {return false}
	// 都不空时值不等
	if root1.Val != root2.Val {return false}
	// 当前值相等，还需要比较子树
	return equals(root1.Left, root2.Left) && equals(root1.Right, root2.Right)
}

// IsValid 有效性
// 注意这里不能简单的将node/left/right三者的值进行比较
// 因为二叉搜索树的定义是 左子树的值均 < node < 右子树的值
func (bst *BST) IsValid() bool {
	return isValid(bst.root, nil, nil)
}

// 根据传入的最大最小值节点限制，判断root子树是否有效
// min，max为空时说明没有限制
func isValid(root, min, max *Node) bool {
	// 边界
	if root == nil {return true}
	// min限制且root不>left则不对（这里对异常情况统一作无效处理，
	// 是因为若元素都不可比较，自然树是无效的）
	if min != nil && compare(root.Val, min.Val) != 1 {
		return false
	}
	// max有限制且root不<right则不对
	if max != nil && compare(root.Val, max.Val) != -1 {
		return false
	}
	// 如果 left < root < right，还要递归检查左右子树
	return isValid(root.Left, min, root) && isValid(root.Right, root, max)
}


//////////////////////////////////////////////////////////

// BST的遍历框架
// 基本上所有操作都是按这个模板，先找后操作
func traverse(root *Node, target Value) {
	// 1. 找到目标
	if root.Val == target {
		// do something
	}
	// 2. 向左右子树之一递归
	if compare(root.Val, target) == 1 {
		traverse(root.Left, target)
	}
	if compare(root.Val, target) == -1 {
		traverse(root.Right, target)
	}
}

// Tips: 如果当前节点的操作会对其下边的子树产生影响，
// 需要构造辅助函数增长参数列表，借助参数传递信息

```

### 2.2 其他操作

此外，对于一颗二叉搜索树来讲，还应有的API有：

- Minimum() 返回最小值
- Maximum() 返回最大值
- Successor() 返回后继节点 (中序遍历cur)
- Predecessor() 返回前驱节点 （中序遍历pre+cur）
- Floor()  返回比target小的最接近者或者target自身（自身存在）（中序遍历） 
- Ceil() 返回比target大的最接近者或者target自身（自身存在） （中序遍历）
- Rank() 返回target节点排名第几 
  - 每个节点额外记录一个子树节点数的变量
- Select() 排名第几的值是什么 

- 支持重复元素的二分搜索树
  - 插入时$\leq$的放左子节点（或者$\geq$的放右子节点）
  - 当重复元素很多时，这样会占用大量空间，可以改造一下，让节点多记录一个变量：该元素的重复个数

### 2.4 二分搜索树的局限性

- 同样的一个数列可以用不同的二分搜索树表示。二分搜索树可能退化成链表。
  - 例如$[1,2,3,4,...]$，这样直接构建二叉搜索树会退化成链表
  - 解决方案：
    - 构造二叉树时随机打乱数组
      - 局限性：要求一开始获得全部数据
    - 平衡二叉树
      - 红黑树、2-3树、AVL树、Splay树（伸展树）

### 2.5 与其他数据结构的结合与拓展

- Treap 二分搜索树与堆
- Trie 字典树（前缀树）
  - 主要作用：统计词频、路由（两大类，另一类是基于哈希表的）
  - 进阶： 压缩前缀树

### 2.6 树形问题

有些题目虽然没有创建树，但是是以树的形状进行求解的，这类称树形问题。 且一般能通过递归解决。

- 归并排序和快速排序 （树的前序遍历或后序遍历）
- 搜索问题（搜索树）
  - 一条龙游戏、8数码
  - 每一步找出所有可能，下一步根据这些可能又列举新的所有可能，构成**决策树**，每走一步都是在决策树中往下层走一步。
  - 8皇后
    - 在棋盘上放置8枚皇后，使其满足一些条件不会相互攻击
  - 数独
  - 搬运工

### 2.7 各种各样的树

- KD树
- 区间树（线段树）
- 哈夫曼树
- 树状数组

## 3. 红黑树

红黑树是工程上最常用的平衡搜索树

### 3.1 2-3树

对于第2节所介绍的二叉搜索树，其局限性主要在于，树的平衡性难以保证，操作复杂度有可能退化到$O(n^2)$。

为了保证平衡性，`2-3树`引入了`3结点`的概念，
- `3结点`包含两个键（这里说的键指的就是上面的`v`）和三个结点指针（中间结点指针指向键处于`3节点`两个键中间的那些键的结点）
- `2结点`则指二叉搜索树中的结点概念。

一颗完美的`2-3树`指其所有空指针（或者说链接、引用）到根节点距离完全一致。要注意这样完美的2-3树维护非常困难（需要大量额外代码实现）。

1. 结点定义（只作思路展示）

	```go       
type Node2 struct {
    Key int
    Left,Right *Node2
}

type Node3 struct {
    KeyL, KeyR int
    Left,Mid,Right *Node3
}
```

2. 查找键

在树中查找键并不复杂，思路与二叉搜索树查找键别无二致，只是`3结点`将键划为三段区间而非两端。

3. 向`2结点`插入键

当插入一个键，发现最终其停在一个`2结点`叶子上时，按二叉查找树的思路，需要`cur.Left/Right = new Node(k)`这般插入，但是为了保持完美平衡，这里需要将`2结点`转换为`3结点`，这样可保证此时完美平衡

```go
// 插入5
//
// 1. 插入前
//     1
//    / \
// 
// 2. 变为3结点
//     1   5 
//    /  |  \ 
```

4. 向一个仅含有1个`3结点`的`2-3树`插入新键

将`3结点`临时变为`4结点`，再将其扩展为3个`2结点`

```go
// 插入5
//
// 1. 插入前
//     1   7
//    /  |  \
// 
// 2. 临时4结点
//     1  5  7
//    /  / \  \
// 
// 3. 生长为3个2结点
//        5
//      /   \
//     1     7
//    / \   / \
```

5. 向父节点为`2结点`的`3结点`插入新键


```go
// 插入5
//
// 1. 插入前
//          12
//       /     \
//     1   7    15
//    /  |  \   /\
// 
// 2. 临时4结点
//           12
//         /    \ 
//     1  5  7   15
//    /  / \  \  /\
// 
// 3. 不必生长新结点，而是挪一个到父节点去
// 分裂4结点时选中间键
//         5   12
//        /  |  \ 
//     1     7   15
//    / \   / \   /\
```

6. 向父节点为`3结点`的`3结点`插入新键

按第5条的思路，不断将`3结点`临时转换为的`4结点`向祖宗路径移动，直到找到某个祖宗节点的父节点为`2结点`，按第5条处理。

另外一种情况是，在祖宗路径上没有`2结点`，追溯到根节点（这种情况下根节点也是`3结点`）
那么就需要对根节点进行分解

7. 分解根节点（此时的根节点为临时的`4结点`）

直接按照第4条将根节点分裂为3个`2结点`的根树

8. `2-3树`的插入过程中其实都可以划为上面的操作来实现（但具体考虑的情况较多）。这些分裂或者说变换都是**局部**的，并不对其他结点产生影响，这也是`2-3树`插入的根本

**这些局部变换不会影响全局的平衡性与有序性**

**和二叉搜索树的自顶向下生长不同，2-3树是自底向上生长的**

完美的2-3树尽管保证了绝对平衡，但却相对二叉搜索树引入了太多的代码，这些操作使得其性能虽然很稳定，但却可能比普通的二叉搜索树更慢。

平衡一颗树的目的是**引入少量的代码操作消除最坏情形的发生**，也就是说不必保证完全平衡，毕竟算法的目的是为了提升效率而不是树的绝对平衡好看...

### 3.2 从2-3树到红黑树

`2-3树`并不难理解，实现也不是很难，主要是引入的操作过多。相比之下，`红黑树`仅靠引入少量代码就实现了二叉搜索树的近乎完全平衡，称`红黑二叉搜索树`，简称`红黑树`。

红黑树引入额外信息来表示`2-3树`中的`3结点`，而不是引入两种结点类型（结点类型变多这意味着代码量急剧上升）

新引入的名词：

- 红链接： 红链接将两个`2结点`组合为一个`3结点`

```go
// 红链接用%表示
// 
// 3结点
// 
//        k1    k2
//     /     |    \
//  less    mid  greater
// 
// 红链接与两个2结点
// 
//            k2
//          %   \
//        k1     greater
//     /    \    
//  less    mid  
```

- 黑链接: `2-3树`中的普通链接

这样做的好处是：无需修改，直接使用二叉查找树的`get()`方法

### 3.3 红黑树的定义

红黑树指**含有红黑链接**且满足以下条件的二叉搜索树

- 红链接均为左链接
- 没有一个节点同时和两个红链接相连
- 红黑树是**完美黑色平衡**的（任意空链接到根节点路径上的**黑链接数**相同）

**红黑树保证了从根节点到叶节点的最长路径不超过最短路径的两倍**

### 3.4 红黑树与2-3树的一一对应

- 将红链接画平并将红链接两端的两个键合并，则得到`3结点`，整体来讲就是红黑树变为了`2-3树`。
- 而不将红链接画平，红黑树就是一颗标准的二叉搜索树

红黑树既是二叉搜索树，也是`2-3树`。 他结合了两种结构的优点：BST的简洁高效和2-3树的平衡插入。

### 3.5 红黑链接的代码表示

- 每个节点都只会有一条链接指向自己
- 在节点的`struct`中增加成员变量`isRed bool`，表示指向自己的链接是红链接还是黑链接
- 默认空链接为`false`

```go
// 这里实现的是一个键值均为int的红黑树，从功能上来讲，他是一个基于红黑树的映射

type Node struct {
    Key int
    Val int
    Left, Right *Node
    N int   // 子树中结点数，包含自身
    isRed bool  // 私有
}
```

### 3.6 旋转

在实现一些操作时，可能会出现红链接变成右链接的情况，因此需要在操作结束之前将其旋转回来

```go
// 红右链接
// 
//             %| （红黑都有可能） 
//            k1
//          /   %
//        less   k2     
//              /  \    
//            mid  greater
// 
// 旋转为 红左链接
// 
//            k2
//          %   \
//        k1     greater
//     /    \    
//  less    mid 
//
//
// 代码实现
// 
// 将右红链接转为左红链接，返回新的子树的根
func rotateR2L(root *Node) *Node {
    rightChild := root.Right
    // 改变链接指向
    root.Right = rightChild.Left    
    rightChild.Left = root
    // 修改红黑属性
    rightChild.isRed = root.isRed   
    root.isRed = true
    // 修改子树结点数
    rightChild.N = root.N
    root.N = 1 + root.Left.N + root.Right.N
    // 返回新根
    return rightChild
}
```

与左旋相应，右旋操作如下

```go
// 将左红链接转为右红链接，返回新的子树的根
func rotateL2R(root *Node) *Node {
    leftChild := root.Left
    // 改变链接指向
    root.Left = leftChild.Right    
    leftChild.Right = root
    // 修改红黑属性
    leftChild.isRed = root.isRed   
    root.isRed = true
    // 修改子树结点数
    leftChild.N = root.N
    root.N = 1 + root.Left.N + root.Right.N
    // 返回新根
    return leftChild
}
```

旋转操作保障了插入新键时红黑树与2-3树之间的一一对应关系，也就保障了红黑树的**有序性和完美黑色平衡**

### 3.7 插入操作

- 为了保证 **完美黑色平衡**， 新插入节点总是先标记为红色，也就是与其父节点形成红链接。
- 如果这个红链接是右链接，则需要进行左旋修正

1. 向单个2-结点插入新键

```go
// 一颗只包含一个2-结点的树
//      3
//     / \
// 
// 若插入新键5（>3）
// 直接插入的话变成：
//      3
//     / \
//        5
//       / \
// 为了保证 完美黑色平衡 的性质， 3->5的链接为红色
//      3
//     / %
//        5
//       / \ 
// 显然需要再进行一次左旋操作
// 
// 如果插入新键 1 （1<3），直接左红链接就好
//      3
//     % \
//    1
//   / \
// 
```

2. 向树底部的2-结点插入新键

与上类似，用红链接将新节点与父节点相连，如果是右红链接则进行一次左旋，进行修正

3. 向一个 3-结点 插入新键

3-结点 也就是 一条左红链接 + 两个2-结点 构成的子树（含两个键）

插入新键时有三种情况：

- 新键比子树的两个键都大
- 在中间
- 比子树的两个键都小

```go
// 原3-结点
//      5
//     % \
//    3
//   / \
// 
// 情况1 新键最大（新键为7）
// 直接插入，得到：
//      5
//     % %
//    3   7   
//   / \ / \
// 违背了一个结点最多只能链接一条红链接的性质。 由于新子树已绝对平衡，自然黑色是平衡的，直接将两条红链接变黑，该性质仍不变
//      5
//     / \
//    3   7   
//   / \ / \
//
// 情况2 新键最小（新键为1）
// 直接插入，得到：
//      5
//     % 
//    3      
//   % \ 
//  1
// / \
// 虽然看似没有违背前面定义中提到的三条性质，但显然该子树已经不平衡了，变成链表。
// 考虑到整树近乎平衡这点限制的话，其实红黑树的红链接是不能连续的，最多红黑交替或者连续黑
// 因此这种情况下，将子树最大值(5)进行右旋操作，转为(3)的右结点， 得到
//      3
//     % %
//    1   5   
//   / \ / \
// 同样的，对称两红链接同时转为黑链接
//      3
//     / \
//    1   5   
//   / \ / \
// 
// 情况3 新键中间大（新键为4）
// 直接插入，得到：
//      5
//     % \
//    3      
//   / % 
//      4
//     / \
// 同样的，也产生了连续红链接的情况，
// 这种时候对于三个键就是通过旋转操作得到一个上层一个键，下层两个键的平衡状态。
// 这里显然需要想办法让 (4) 成为新的子树根
// 考虑 (4) 先左旋， 再(5)右旋。 步骤如下
// 先(4)左旋，到(3)和(5)链接中间
//      5
//     % 
//    4      
//   % \ 
//  3
// / \
// 再 (5) 右旋
//      4
//     % %
//    3   5   
//   / \ / \
// 同样的，对称两红链接同时转为黑链接
//      4
//     / \
//    3   5   
//   / \ / \ 
```

顺带提一句， 像上面对称双红链接所连接的部分其实对应2-3树的4-结点。

4. 转换颜色

在前面的操作中，对于4-结点（也就是对称红链接）经常需要进行颜色的转换，这里给出辅助函数。
该辅助函数将两根红链接转红，并将根向上的链接置红（这样置红与新加节点的红链接无异，这是为了不断向上进行处理，红链接的向上传递）

```go
// 4-结点
//      |% (红或黑)
//      5
//     % %
//    3   7   
//   / \ / \
// 
// 转换颜色之后(或者说红色向上传递后)，变成
//      %
//      5
//     / \
//    3   7   
//   / \ / \
// 
// flip为蹦的意思，相当于两个子节点的红色蹦到了父节点上去
func flipColor(root *Node) {
    root.isRed = true
    root.Left.isRed = false
    root.Right.isRed = false
} 
```

5. 根节点始终为黑色链接

在第3点向3-结点插入新键的过程中需要颜色转换，但颜色转换之后使子树的根其向上的链接变成红色。
要记住一点： **红黑树整树的根其向上链接（或者说根节点颜色）必须是黑的**

因此：
- 每次插入操作之后，都需要对树的根节点置黑
- 根节点每次由红变黑，都意味着树中黑链接的高度+1

6. 向树底部的3-结点插入新键

操作其实就是第3点所说的，但是引入颜色转换操作之后，要意识到子树的根结点颜色变红了，所以要继续向上处理（旋转及转换颜色），直到满足红黑树定义

举例如下：

```go
// 考虑红黑树如下：
//             5
//          /    \
//        3       9   
//       % \     % \
//      2       7
//     / \     / \
//
// 插入新键6，按二叉搜索树的插入规则，6将插入到7的左侧
//             5
//          /    \
//        3       9   
//       % \     % \
//      2       7
//     / \     % \
//            6
//           / \
//
// 出现两条左红链接
// （其实两条连续左链接必然是两条连续左红链接，因为结点是一个一个插入的，
// 每次新插都是红链接，而原先左子节点本身就是红链接）
// 因此需要对 (9) 进行右旋
//             5
//          /    \
//        3       7   
//       % \     % %
//      2       6   9
//     / \     / \ / \
//  
// 出现两条对称的红链接（4-结点），转换颜色
//             5
//          /    %
//        3       7   
//       % \     / \
//      2       6   9
//     / \     / \ / \
// (7)变红了，相当于新插入的结点，因此需要继续考虑（4）（3）（7）组成的子树，
// 要将其重新调整为3-结点。 因此要对结点(5)锁在子树执行左旋修正，使(7)成为新根
//				        7
//			        %   \
//            5      9
//          /  \    / \  
//        3     6     
//       % \   / \            
//      2       
//     / \     
```

7. 红链接的向上传递

上面已经描述过，向一个`3-结点`插入新键后其子树根节点最后变红，相当于红链接向上传递。直至遇到一个`2-结点`或者`根节点`则停止向上传递。

基本上可以说，插入操作由 左旋、右旋、转换颜色 三中操作保证。

下面是总结：

- 若 右子结点为红 而 左子节点为黑， 则进行左旋修正
- 若 左子节点为红 且 左子节点的左子节点也为红（即连续左红），则右旋修正
- 若左右子结点均为红，则转换颜色，将红色向上传递

8. 插入实现

```go
// 先定义一颗树
type RBT struct {
	root *Node		// 前面定义好的红黑树根结点
}

// 插入操作
func (rbt *RBT) Put(k, v int) {
	rbt.root = put(rbt.root, k, v)		// 插入
	rbt.root.isRed = false				// 置黑
}

// 向root节点为根的子树插入新键，返回新根
func put(root *Node, k,v int) *Node {
	// 前面是标准的二分查找树递归插入操作
	if root == nil {
		return &Node{k,v,nil,nil,1,true}	// 新插入结点先置红
	}

	if root.Key < k {
		root.Right = put(root.Right, k, v)
	} else if root.Key > k {
		root.Left = put(root.Left, k, v)
	} else {	// root.key = k 更新值
		root.Val = v
	}

	// 接下来是检查当前子树红链接情况（分别对应第7条总结点的三个情况）
	if root.Right.isRed && !root.Left.isRed {
		root = rotateR2L(root)	// 左旋
	}
	if root.Left.isRed && root.Left.Left.isRed {
		root = rotateL2R(root)  // 右旋
	} 
	if root.Left.isRed && root.Right.isRed {
		flipColors(root)		// 转换颜色
	}

	// 在返回当前节点之前，注意子树可能发生了变化，当前结点的N自然要更新
	// 要警惕的是，不能使用 root.N += 1
	root.N = root.Left.N + root.Right.N + 1
	return root
}
```

ps: 虽然看上去没有警惕空指针引用的情况，但其实边界条件已经限制了不会空指针引用。

### 3.8 删除操作

删除操作比插入操作更为复杂。

因为插入操作只需要先以二叉搜索树的方式找到新键的位置，再**自底向上**进行左旋、右旋、转换颜色，来保持红黑树。

而删除操作需要先找到待删结点，再构造临时`4-结点`沿着查找路径向下进行变换，还要在删完之后分解`4-结点`时沿查找路径向上进行变换。

1. 自顶向下的2-3-4树的插入算法

2-3-4树指的是在2-3树中存在`4-结点`的树（`2-3树`只是某些操作的中间可能会产生`4-结点`）。

对`2-3-4树`进行插入操作需要沿查找路径向下进行变换，使得路径上没有`4-结点`（有的话要分解掉），以便有足够空间插入新键； 而在插入之后则要沿查找路径向上将出现的`4-结点`进行配平（原先拆开的）

向下的变换与`2-3树`中`4-结点`的分解一样，有几种情况需处理：

- 根节点为`4-结点`，则将之分解为三个`2-结点`，树高+1
- 遇到父节点为`2-结点`的`4-结点`，则将`4-结点`分解为两个`2-结点`并将中间的键移至父节点，使父节点构成一个`3-结点` 
- 遇到父节点为`3-结点`的`4-结点`，则将`4-结点`分解为两个`2-结点`并将中间的键移至父节点，使父节点构成一个`4-结点` 

这样的插入过程可以保证不会出现连续的`4-结点`，并且向下查找的过程中，只会在树底遇到`2-结点`或`3-结点`（这样就有位置插入新键，而不破坏平衡性）

**在红黑树中实现这个算法**

- 将`4-结点`表示为3个`2-结点`构成的平衡的子树，并且根节点与两个孩子节点都红链接
- 向下查找的过程中，不断分解所有`4-结点`并进行颜色转换
- 和插入操作一样，在**向上**的过程中用旋转将`4-结点`配平

这个过程和上面红黑树插入算法实现中几乎一样，唯一的区别在于，将`flipColors()`移至null测试和比较操作之间。

这个算法比2-3树插入算法要好一些，尤其是在多进程操作这颗红黑树时。因为`2-3-4树`插入算法只需要操作一条或两条链接。而2-3树则可能需要不断向上修改。（操作少可以更快结束当前，从而释放锁让其他进程可以使用树）

2. 2-3树中**删除最小键**

从`2-3树`中删除最小键，则必然是从树底删除，可能是`3-结点`也可能是`2-结点`。

- 如果是`3-结点`，直接删除最小键，让该结点蜕变成`2-结点`
- 若是`2-结点`则不能直接删（回不平衡）。

也就是说，删除最小键，需要保证最小键在的结点不是`2-结点`。（可能是`3-结点`或者临时`4-结点`）。因此向下查找的过程中需要对结点或者子树进行变换。假如访问到cur结点

- 若cur是`2结点`且左右孩子也都是，则将之合并为`4-结点`
- 不是上面这种情况的话需要保证cur的左孩子不能是个`2-结点`，必要时cur的左孩子可以向其兄弟借一个键过来

因此，在沿着左链接向下的过程中，必须保证以下几点之一：

- cur的左孩子不是`2-结点`的话，完成
- cur的左孩子是`2-结点`而左孩子的亲兄弟有不是`2-结点`的，则借一个键过来
- 如果cur的左孩子和左孩子的亲兄弟都是`2-结点`，那么，左右孩子 + cur中的最小的键构成`4-结点`，使父节点由`4-结点`变成`3-结点`或者由`3-结点`变成`2-结点`。

在查找最小键的路径上执行上面描述的变换，则可以保证：最小键一定在一个`3-结点`或`4-结点`上，这样就可以直接删除最小键。

然后，再回头向上分解所有的临时的`4-结点`

3. 红黑树的删除操作

**在查找待删键的路径上执行前面删除最小键一样的变换操作，可以保证路径上任意当前结点均不是`2-结点`**

- 如果待删键是树底部位置，则可以直接删除
- 不是的话，需要像二叉查找树删除操作一般，找到待删节点的后继结点并交换，再删后继节点。
  - 待删为cur，cur为根的子树中，用cur.Right为根的子树中的最小值作为后继结点（将之赋给cur），现在问题转化为删除cur.Right子树的最小键（交换之后其实仍是最小键，即便不是也不影响）
- 删除之后再向上进行回溯，不断分解所有临时`4-结点`

代码实现

**删除最小键**

```go
// 将根节点颜色置黑，左右孩子置红。删除时调用这个函数
func flipDownColors(root *Node) {
	root.isRed = false
	root.Left.isColor = true
	root.Right.isColor = true
}

// 将红链接移到左边
func moveRedLeft(root *Node) *Node {
	// 假设结点root为红，左右孩子均为黑色，则将左孩子或左孩子的孩子之一变红

	// 1. 颜色转换
	flipDownColors(root)

	// 2. 若右孩子的左孩子也是红色（是说明出现连续红链接）
	if root.Right.Left.isRed {
		root.Right = rotateL2R(root.Right)	// 右旋修正
		// 这中间似乎缺少了插入操作过程中的flipColors()
		root = rotateR2L(root)				// 左旋修正
	}
	return root
}

// 平衡子树，返回新根
func balance(root *Node) *Node {
	// 右旋修正
	if root.Right.isRed {
		root = rotateR2L(root)
	}

	// 检查三种情况，实现平衡 （这几句和put方法后面几句一样）
	if root.Right.isRed && !root.Left.isRed {
		root = rotateR2L(root)	// 左旋
	}
	if root.Left.isRed && root.Left.Left.isRed {
		root = rotateL2R(root)  // 右旋
	} 
	if root.Left.isRed && root.Right.isRed {
		flipColors(root)		// 转换颜色
	}

	// 在返回当前节点之前，注意子树可能发生了变化，当前结点的N自然要更新
	// 要警惕的是，不能使用 root.N += 1
	root.N = root.Left.N + root.Right.N + 1
	return root
}

// 删除最小键
func (rbt *RBT) DelMin() {
	// 1. 如果左右孩子都是黑，先将红黑树根置为红
	if !rbt.root.Left.isRed && !rbt.root.Right.isRed {
		rbt.root.isRed = true
	}
	// 2. 删除最小键，返回新根
	rbt.root = delMin(rbt.root)
	// 3. 如果红黑树不为空的话，始终记得将根节点置黑
	if rbt.root != nil {
		rbt.root.isRed = false
	}
}

// 删除最小键
func delMin(root *Node) *Node {
	// 1. 左孩子为空，则返回空
	if root.Left == nil {return nil}
	// 2. 左孩子以及左孩子的左孩子都为黑，则从右边挪一个红链接过来
	if !root.Left.isRed && !root.Left.Left.isRed {
		root = moveRedLeft(root)
	}	
	// 3. 递归
	root.Left = delMin(root.Left)
	newRoot := balance(root)
	return newRoot
}
```

**删除最大键**

```go

// 将红链接移到右边
func moveRedLeft(root *Node) *Node {
	// 假设结点root为红，左右孩子均为黑色，则将右孩子或右孩子的孩子之一变红

	// 1. 颜色转换
	flipDownColors(root)

	// 2. 若左孩子的左孩子也是黑色
	if ！root.Left.Left.isRed {
		root = rotateL2R(root)	// 右旋修正
	}
	return root
}

// 删除最大键
func (rbt *RBT) DelMax() {
	// 1. 如果左右孩子都是黑，先将红黑树根置为红
	if !rbt.root.Left.isRed && !rbt.root.Right.isRed {
		rbt.root.isRed = true
	}
	// 2. 删除最小键，返回新根
	rbt.root = delMax(rbt.root)
	// 3. 如果红黑树不为空的话，始终记得将根节点置黑
	if rbt.root != nil {
		rbt.root.isRed = false
	}
}

// 删除最大键
func delMax(root *Node) *Node {
	// 1. 左孩子为红色，则右旋
	if root.Left == nil {
		root = rotateL2R(root)
	}
	// 2. 右孩子为空则返回空
	if root.Right == nil {return nil}
	// 3. 右孩子以及右孩子的左孩子都为黑，则从左边挪一个红链接过来
	if !root.Right.isRed && !root.Right.Left.isRed {
		root = moveRedRight(root)
	}	
	// 4. 递归
	root.Right = delMax(root.Right)
	newRoot := balance(root)
	return newRoot
}
```

**删除任意键**

```go
func (rbt *RBT) Del(k int) {
	// 1. 如果左右孩子都是黑，先将红黑树根置为红
	if !rbt.root.Left.isRed && !rbt.root.Right.isRed {
		rbt.root.isRed = true
	}
	// 2. 删除k，返回新根
	rbt.root = del(rbt.root, k)
	// 3. 如果红黑树不为空的话，始终记得将根节点置黑
	if rbt.root != nil {
		rbt.root.isRed = false
	}
}


func del(root *Node, k int) *Node {
	// 如果当前节点键 > k
	if root.Key > k {
		// 如果左孩子和左孩子的左孩子都是黑色，借一个红过来
		if !root.Left.isRed && !root.Left.Left.isRed {
			root = moveRedLeft(root)
		}	
		// 递归删除
		root.Left = del(root.Left, k) 
	} else {
		// 当前节点的键 <= k

		// 左孩子若为红 
		if root.Left.isRed {
			root = rotateL2R(root)	// 右旋	
		}
		// 如果键相等且右结点为空
		if root.Key == k && root.Right == nil {
			root = moveRedRight(root)
		}
		// 如果键相等
		if root.Key == k {
			// 这句其实就是选择右子树的最小键作为接替者，现将两者键值交换
			// （这里没有实现，但查询类方法与二叉搜索树一模一样）
			minRight := getMin(root.Right)
			root.Key = minRight.Key
			root.Val = get(root.Right, minRight.Key)
			// 然后再删除右子树的最小键
			root.Right = deleteMin(root.Right)
		}
		// 否则的话 当前节点键 < k
		root.Right = delete(root.Right, k)
	}

	// 平衡
	newRoot := balance(root)
	return newRoot
}
```

### 3.9 查找类操作

- 查找类操作（getMin/getMax/floor/ceil/rank/select）与二叉搜索树完全一致，因为不涉及颜色问题。

### 3.10 红黑树性质

- 红黑树近乎完美平衡
  - 树高不超过 $2lgN$（而且这个数字都是比较保守的）

### 3.11 各种符号表实现的性能比较

|算法|(最坏)查找|(最坏)插入|(平均)查找|(平均)插入|是否支持有序相关操作|
|-|-|-|-|-|-|
|无序链表|$N$|$N$|$n/2$|$N$|否|
|有序数组|$lgN$|$N$|$lgN$|$N/2$|是|
|二叉搜索树|$N$|$N$|$1.39lgN$|$1.39lgN$|是|
|红黑树|$2lgN$|$2lgN$|$1.00lgN$|$1.00lgN$|是|

### 3.12 搜索树的思考

在快速排序中，为了保证递归树的平衡，使用了随机基准的优化，此外，为了处理重复元素导致的失衡，引入了双路快排和三路快排的优化。

而在二叉搜索树（这里指不存重复元素的搜索树）中，也存在着因为入树顺序带来的失衡问题，在入树序列升序或降序时，二叉搜索树退化为链表。

为了解决这个问题，各种平衡二叉树被设计出来。

`2-3树`保证了树的绝对平衡，但代价较高；
红黑树延续了`2-3树`的思想，在树的平衡与引入的额外操作复杂度之间做了平衡，借助颜色转换、旋转操作实现了树的近似完全平衡，并且所有查找类方法在二叉搜索树的基础上完全不需要修改。

## 4. 哈希表

基于哈希表的查找主要分两步进行：
1. 用哈希函数将键转化为数组索引
2. 处理索引的碰撞冲突
   - 拉链法
   - 线性探测法

### 4.1 散列函数

**散列函数和键的类型有关**，对于每种类型的键，都需要有一种与之对应的散列函数。

下面是一些简单散列函数的例子：

- 正整数为键： **除留余数法**   $i = k \% M$
  - $M$最好是素数（非素数可能无法利用键的全部信息）
- 浮点数为键： 
  - 若键为0/1之间的实数，可乘以$M$并四舍五入得到一个$[0,M-1]$的索引值
    - 四舍五入后低位信息对散列结果没有影响
  - 更好的做法是：表示为二进制数再使用除留余数法。 （Java就是这么做）   
- 字符串为键：
  - 除留余数法（将字符串看作大数）
  - Horner方法，也是将字符串映射到$[0,M-1]$
```go
// R是一个比所有字符都大的数，M是除留余数法里的那个除数，选择一个较小的素数
// Java String的默认实现使用了该方法
hash := 0
for i:=0; i<len(s); i++ {
	hash = (R * hash + s[i]) % M
}
```
- 组合键： 例如键是一个结构体，里面有多个整型变量
```go
// 例如 Date额类型包含day,month,year三个整型
hash := (((day * R + month) % M) *R + year) % M
// 也是映射到[0,M-1]
// 注意：R的选取需足够小保证乘式不会溢出；
// M选取合适的素数值例如32，可以省去括号内的 %M 操作
```

- 自定义散列函数
  - Java中为类自定义散列函数需要重写hashCode()和equals()方法
  - 对于计算比较耗时的散列函数，可以在第一次计算之后，将计算结果(hash)存到键中（要求将键改造为结构体），起到**软缓存**效果

- 优秀的散列函数需满足：
  - **一致性**：等价的键产生相同的散列值
  - **高效性**
  - **均匀性**：分布空间均匀

**在有性能要求时谨慎使用散列函数**

### 4.2 基于拉链法的哈希表

常见的解决冲突碰撞的方法有拉链法和线性探测法。拉链法思路简单也比较常用。

其思路是维护一个数组，每个数组元素都是一个链表的头。

查找键值对时，根据键映射到其中一条链表，再顺序去查找这条链表。

因此，也经常会看到`bucket`这个词，数组每个位置就像一个桶，查询需要先定位到某个桶，再去桶里寻找

一个简单实现如下：

```go
// 链表结点定义
type Node struct {
	Key, Val int
	Next *Node
}

// 拉链哈希表定义
// SCHST SeperateChainingHashSearchTable
type SCHST struct {
	buckets []*Node
	m int	// m个桶，设为素数
	size int
}

func NewSCHST(numBuckets int) *SCHST {
	return &SCHST{
		buckets: make([]*Node, numBuckets),
		m: numBuckets,
	}
}

// 哈希映射得到桶序号
func (t *SCHST) bucket(key int) int {
	return key % t.m
}

// 增. 如存在则更新
func (t *SCHST) Add(key, val int) *Node {
	bid := t.bucket(key)
	// 若桶空，新建桶
	if t.buckets[bid] == nil {
		t.buckets[bid] = &Node{Key:key, Val:val}
	} else {
		pre := &Node{Next:t.buckets[bid]}
		for pre.Next != nil {
			if pre.Next.Key == key {
				pre.Next.Val = val	// 更新
				return
			}	
			pre = pre.Next
		}	// 最后pre为最后一个节点
		pre.Next = &Node{Key:key, Val:val}
	}

	t.size++
}

// 删 (异常则返回math.MinInt32报错)并返回删除的值
func (t *SCHST) Del(key int) int {
	bid := t.bucket(key)
	// 若桶空，新建桶
	if t.buckets[bid] == nil {
		return math.MinInt32
	} else {
		pre := &Node{Next:t.buckets[bid]}
		for pre.Next != nil {
			if pre.Next.Key == key {
				ret := pre.Next.Val
				pre.Next = pre.Next.Next	// 删除
				t.size--
				return ret
			}	
			pre = pre.Next
		}	// 最后pre为最后一个节点
		return math.MinInt32
	}
}

// 查
func (t *SCHST) Get(key int) int {
	bid := t.bucket(key)
	// 若桶空，新建桶
	if t.buckets[bid] == nil {
		return math.MinInt32
	} else {
		pre := &Node{Next:t.buckets[bid]}
		for pre.Next != nil {
			if pre.Next.Key == key {
				return pre.Next.Val
			}	
			pre = pre.Next
		}	// 最后pre为最后一个节点
		return math.MinInt32
	}
}
```

ps : 
- 其实拉链可以考虑使用类似LRU的思路，认为访问/修改的多的元素有更大几率被访问，应当移到链表头部位置
- Java中，当拉链长度大于8后会将链转为红黑树
- 初始$M$值是一个调优参数，但不是那么重要，拉链法总是能够在空间与时间当中得到一个折中。
- 一个优化方案是使用动态扩展的数组，来保证链表较短

### 4.3 基于线性探测法的哈希表

相比于数组+链式结构，另外一种哈希表实现是使用纯数组结构。使用大小为$M$的数组来存储$N$个键值对（$M>N$），利用数组中的空位来解决冲突碰撞问题。基于这种策略的哈希表称为**开放地址散列表**



## 参考材料

- [二叉搜索树操作集锦](https://github.com/labuladong/fucking-algorithm/blob/master/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E7%B3%BB%E5%88%97/%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E6%93%8D%E4%BD%9C%E9%9B%86%E9%94%A6.md)
- 《算法》第四版
- [30张图带你彻底理解红黑树](https://mp.weixin.qq.com/s/w5_-V_7V5yOQAcd7UEiEvQ)

