---
title: "树问题集锦"
date: 2020-03-08T18:08:11+08:00
draft: false
categories: ["algo"]
tags: ["树"]
keywords: ["树","二叉树","堆","红黑树"]
---

## 0. 树的遍历框架

通常与树相关的问题都是二叉树相关的问题。

二叉树的遍历方式有前序遍历，中序遍历，后序遍历。这三种遍历都属于深度优先遍历。

还有一种是层序遍历，这属于广度优先遍历。

下面针对二叉树，给出结点类型定义：

```go
type TreeNode struct {
    Val int
    Left, Right *TreeNode
}
```

### 0.1 前序遍历

递归版本

```go
func preorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}
    // 先中
    // do something
    // 再左
    preorder(root.Left)
    // 后右
    preorder(root.Right)
}
```

迭代版本：

```go
func preorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}

    stack := make([]*TreeNode, 0)
    stack = append(stack, root) // 根节点入栈

    // 中左右遍历
    // 由于栈是倒序输出，因此需要先将右压入栈中，后将左压入。
    for len(stack) != 0 {   // 栈不为空则继续
        cur := stack[len(stack)-1]
        stack = stack[:len(stack)-1]   // 栈顶元素出栈

        // 对当前元素处理
        // do something

        // 再右入栈
        if cur.Right != nil {
            stack = append(stack, cur.Right)
        }
        // 再左入栈
        if cur.Left != nil {
            stack = append(stack, cur.Left)
        }
    }
}
```

上面的迭代版本其实是完全复刻前序遍历递归实现的，这种递归思路直接转迭代的写法似乎只有前序遍历比较好实现。

下面是更为通用的迭代写法：

```go
func preorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}

    stack := make([]*TreeNode, 0)
    cur := root     // 游标结点

    for len(stack) != 0 || cur != nil {   // 栈不为空或者cur节点不为空
        
        // 左子孙入栈
        for cur != nil {
            // 前序遍历：入栈时处理结点
            // do something
            stack = append(stack, cur)
            cur = cur.Left
        }
        
        // cur移动到右子树
        if len(stack) != nil {
            cur = cur.Right
        }
    }
}
```

一般默认前序遍历就是“中-左-右”遍历，但有时需要先访问右边，再访问左边，只需要将对左子节点的操作与右子节点的操作顺序进行调换。

### 0.2 中序遍历

递归版本

```go
func inorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}

    // 先左
    inorder(root.Left)
    // 再中
    // do something
    // 后右
    inorder(root.Right)
}
```

迭代版本：

```go
func inorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}

    stack := make([]*TreeNode, 0)
    cur := root

    // 中序遍历
    for len(stack) != 0 || cur != nil {   // 栈不为空或者cur不为空则继续

        // cur不为空，则不断将cur的左子孙压入栈中
        for cur != nil {
            stack = append(stack, cur)  // 入栈
            cur = cur.Left
        }

        // 出栈处理当前结点
        cur = stack[len(stack)-1]
        satck = stack[:len(stack)-1]   // 栈顶元素出栈

        // 对当前结点处理
        // do something

        // 再转移至当前节点的右子树
        cur = cur.Right
    }
}
```

注意：在二叉搜索树相关的题目中，考察中序遍历的频率相当之高。

### 0.3 后序遍历

递归版本

```go
func postorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}

    // 先左
    postorder(root.Left)
    // 再右
    postorder(root.Right)
    // 后中
    // do something
}
```

注意：后序遍历同样也有“左-右-中”(默认)和“右-左-中”两种。

迭代版本：

```go
func postorder(root *TreeNode) {
    // 递归终止
    if root == nil {return nil}

    stack := make([]*TreeNode, 0)
    cur := root
    lastVisit := root   // 用来标记右子树也访问完了

    // 后序遍历
    for len(stack) != 0 || cur != nil {   // 栈不为空或者cur不为空则继续

        // cur不为空，则不断将cur的左子孙压入栈中
        for cur != nil {
            stack = append(stack, cur)  // 入栈
            cur = cur.Left
        }

        // 不出栈，peek栈顶
        cur = stack[len(stack)-1]

        // 检查右子节点情况和lastVisit
        // 右子树为空，或者lastVisit=右子节点（说明右子树访问完）
        // 满足这两者，才去处理当前栈顶结点cur
        // 将其出栈、lastVisit移动到cur
        if cur.Right == nil || lastVisit == cur.Right {
            // do something to cur

            satck = stack[:len(stack)-1]   // 栈顶元素出栈
            lastVisit = cur // 标记cur子树已访问过
            cur = nil       // 将cur清掉
        } else {
            // 否则的话继续查看右子树
            cur = cur.Right
        }
    }
}
```

### 0.4 层序遍历

这里没有真的使用队列，但道理是一样的。

```go
func levelOrder(root *TreeNode) [][]int {
    if root == nil {return nil}
    res := make([][]int, 0)
    queue := make([]*TreeNode, 1)
    queue[0] = root
    for len(queue) != 0 {
        newQ := make([]*TreeNode, 0)
        curLayer := make([]int, 0)
        for _, node := range queue {
            // 处理当前
            curLayer = append(curLayer, node.Val)
            // 将下一层入队
            if node.Left != nil {
                newQ = append(newQ, node.Left)
            }
            if node.Right != nil {
                newQ = append(newQ, node.Right)
            }
        }
        res = append(res, curLayer)
        queue = newQ
    }
    return res
}
```

### 0.5 自顶向下与自底向上

在使用递归解决树或树型问题时，递归解法一般有两种思路：自顶向下和自底向上。

- 自顶向下： 可以看作是一种前序遍历；
- 自底向上： 可以看做是一种后序遍历

树问题两个思考：
- 能确定一些参数从当前节点自身解决出发寻找答案吗？
- 能使用这些参数和节点本身的值来决定给它的子节点传递什么参数吗？

如果能，考虑自顶向下，否则考虑自底向上。当然有时两种做法都可以实现目标。

### 0.6 参考

- [我的Github](https://github.com/azd1997/Leetcode-training/tree/master/ltalgo/tree-traverse)
- [二叉树遍历（前序、中序、后序）](https://www.jianshu.com/p/456af5480cee)

## 1. 例题

### 1.1 二叉树的最大深度

```go
// 给定一个二叉树，找出其最大深度。

// 二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

// 说明: 叶子节点是指没有子节点的节点。

// 示例：
// 给定二叉树 [3,9,20,null,null,15,7]，

//     3
//    / \
//   9  20
//     /  \
//    15   7
// 返回它的最大深度 3 。

////////////////////////////////////////////////////

// 1. 自顶向下（前序遍历）
func maxDepth(root *TreeNode) int {
    maxdepth := 0
    help(root, &maxdepth, 1)    // 1是当前层深度，深度从1开始
    return maxdepth
}

func help(root *TreeNode, maxdepth *int, curdepth int) {
    if root == nil {return}
    
    // 处理当前
    if curdepth > *maxdepth {*maxdepth = curdepth}
    // 向下一层
    help(root.Left, maxdepth, curdepth+1)
    help(root.Right, maxdepth, curdepth+1)
}

// 2. 自底向上（后序遍历）
func maxDepth(root *TreeNode) int {
    return help(root)    // 统计root这棵树的高度，也就是节点的最大深度
}

func help(root *TreeNode) int {
    if root == nil {return 0}
    
    // 先处理下一层
    left := help(root.Left)   // 左子树高度
    right := help(root.Right) // 右子树高度
    return max(left, right) + 1     // 返回当前子树高度
}

func max(a,b int) int {
    if a>=b {
        return a
    }
    return b
}

// ps: 自底向上写法这里，其实不需要help()
```

### 1.2 对称二叉树

```go
// 给定一个二叉树，检查它是否是镜像对称的。

// 例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

//     1
//    / \
//   2   2
//  / \ / \
// 3  4 4  3
// 但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

//     1
//    / \
//   2   2
//    \   \
//    3    3
// 说明:

// 如果你可以运用递归和迭代两种方法解决这个问题，会很加分。

/////////////////////////////////////////////////////////

// 递归/迭代 前序/后序  // 总共有四种写法
// 核心做法就是左边中左右（或左右中），右边中右左（或右左中），看得到的序列是否相同

// 1. 递归+前序
func isSymmetric(root *TreeNode) bool {
    if root == nil {return true}
    return help(root.Left, root.Right)
}

func help(rootl, rootr *TreeNode) bool {
    if rootl==nil && rootr==nil {
        return true
    }
    if rootl==nil || rootr==nil {
        return false
    }
    if rootl.Val != rootr.Val {return false}
    return help(rootl.Left, rootr.Right) && help(rootl.Right, rootr.Left) 
}

// 2. 迭代+前序
// 迭代自顶向下 中左右/中右左遍历
func isSymmetric(root *TreeNode) bool {
    if root == nil {return true}
    stack := []*TreeNode{root, root}
    for len(stack)!=0 {
        curl, curr := stack[len(stack)-1], stack[len(stack)-2]
        stack = stack[:len(stack)-2]
        
        // 检查当前
        if curl == nil && curr == nil {continue}
        if curl == nil || curr == nil {return false}
        if curl.Val != curr.Val {return false}
        // 否则将其孩子节点入栈
        stack = append(stack, curl.Left, curr.Right, curl.Right, curr.Left)
    }
    
    return true
}

// ps: 这道题用常规的迭代模板来写会比较啰嗦
```

### 1.3 路径总和

```go
// 给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

// 说明: 叶子节点是指没有子节点的节点。

// 示例: 
// 给定如下二叉树，以及目标和 sum = 22，

//               5
//              / \
//             4   8
//            /   / \
//           11  13  4
//          /  \      \
//         7    2      1
// 返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

////////////////////////////////////////////////////////

func hasPathSum3(root *TreeNode, sum int) bool {
	if root == nil {
		return false
	}
	return help3(root, sum)
}

func help3(root *TreeNode, sum int) bool {
	if root.Left == nil && root.Right == nil { // 叶子结点
		return sum-root.Val == 0 // 检查sum是否减为0
	}
	// 处理当前
	sum -= root.Val
	leftok, rightok := false, false
	if root.Left != nil {
		leftok = help3(root.Left, sum)
	}
	if root.Right != nil {
		rightok = help3(root.Right, sum)
	}
	return leftok || rightok
}

// 测例
// tree=[5,4,8,11,null,13,4,7,2,null,null,null,1],sum=22
// tree=[], sum=0
// tree=[1,2], sum=1
// tree=[1], sum=1

```

### 1.4 路径总和II

```go
// 和前面的区别在于：返回所有符合条件的路径

/////////////////////////////////////////

// 回溯解法DFS
func pathSum1(root *TreeNode, sum int) [][]int {
	res := make([][]int, 0)
	path := make([]int, 0)
	help1(root, sum, &path, &res)
	return res
}

func help1(root *TreeNode, sum int, path *[]int, res *[][]int) {

	//fmt.Println(root, sum, *path)

	// 防止root为空，引起panic； 同时也是触底返回
	if root == nil {
		return
	}

	// 先更新path和sum（当前决策）
	*path = append(*path, root.Val)
	sum -= root.Val

	// 检查当前
	if root.Left == nil && root.Right == nil { // 叶子结点
		if sum == 0 { // 找到符合条件的路径
			*res = append(*res, append([]int{}, (*path)...))
			//fmt.Println(*res)
			// 撤销选择
			*path = (*path)[:len(*path)-1]  // 可以和下面合并
			return
		}
		// 撤销选择
		*path = (*path)[:len(*path)-1]
		return
	}

	// 继续处理下一层（下层的决策）
	help1(root.Left, sum, path, res)
	help1(root.Right, sum, path, res)

	// 撤销选择
	*path = (*path)[:len(*path)-1]
}

// ps: 每次做了选择之后return前记得撤销选择
// pps: 警惕slice传参带来的风险
```

### 1.5 路径总和III

### 1.6 从前序与中序遍历序列构造二叉树

```go
根据一棵树的前序遍历与中序遍历构造二叉树。

注意:
你可以假设树中没有重复的元素。

例如，给出

前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

    3
   / \
  9  20
    /  \
   15   7

//////////////////////////////////////////

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func buildTree(preorder []int, inorder []int) *TreeNode {
	// 递归停止
	if len(preorder) == 0 || len(inorder) == 0 {
		return nil
	} // 这两个数组始终等长

	// 当前区间的子树树根的值为 preorder[0]，再线性遍历找到树根在inorder中的位置
	// 据此可以将区间划分为左右子树的区间(先分inorder，然后可确定preorder)，并可递归下去

	root := &TreeNode{Val: preorder[0]}
	rootIdx := findIdx(inorder, root.Val)
    //fmt.Println(preorder, inorder, root.Val, rootIdx)
	root.Left = buildTree(preorder[1:rootIdx+1], inorder[:rootIdx])
	root.Right = buildTree(preorder[rootIdx+1:], inorder[rootIdx+1:])
	return root
}

// 线性遍历找目标值的下标
func findIdx(nums []int, target int) int {
	for i:=len(nums)-1; i>=0; i-- {
		if nums[i] == target {return i}
	}
	return -1
}

```

### 1.7 从中序与后序遍历序列构造二叉树

LeetCode 106

```go
// 题意与上一题相近，这里不再给出

///////////////////////////////////////////////

// 对于后序遍历，头结点位于postorder末尾
// 然后根据这个头结点值 遍历inorder得到头结点位置，进而分出左右子树

func buildTree(inorder []int, postorder []int) *TreeNode {
	if len(inorder) == 0 {
		return nil
	}
	if len(inorder) == 1 { // inorder和postorder长度是一样的
		return &TreeNode{Val: inorder[0]}
	}

	root := &TreeNode{Val: postorder[len(postorder)-1]}
	rootIdx := getIdx(inorder, root.Val)
	root.Left = buildTree(
		inorder[:rootIdx],
		postorder[:rootIdx], // 这种地方就画图，会清晰许多
	)
	root.Right = buildTree(
		inorder[rootIdx+1:],
		postorder[rootIdx:len(postorder)-1],
	)
	return root
}

func getIdx(inorder []int, val int) int {
	for i := 0; i < len(inorder); i++ {
		if inorder[i] == val {
			return i
		}
	}
	return -1
}
```

### 1.8 填充每个节点的下一个右侧节点指针

LeetCode 116

```go
给定一个完美二叉树，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

初始状态下，所有 next 指针都被设置为 NULL。

 

示例：



输入：{"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":null,"right":null,"val":4},"next":null,"right":{"$id":"4","left":null,"next":null,"right":null,"val":5},"val":2},"next":null,"right":{"$id":"5","left":{"$id":"6","left":null,"next":null,"right":null,"val":6},"next":null,"right":{"$id":"7","left":null,"next":null,"right":null,"val":7},"val":3},"val":1}

输出：{"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":{"$id":"4","left":null,"next":{"$id":"5","left":null,"next":{"$id":"6","left":null,"next":null,"right":null,"val":7},"right":null,"val":6},"right":null,"val":5},"right":null,"val":4},"next":{"$id":"7","left":{"$ref":"5"},"next":null,"right":{"$ref":"6"},"val":3},"right":{"$ref":"4"},"val":2},"next":null,"right":{"$ref":"7"},"val":1}

解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。
 

提示：

你只能使用常量级额外空间。
使用递归解题也符合要求，本题中递归程序占用的栈空间不算做额外的空间复杂度。

/////////////////////////////////////////////////

// 明显是层序遍历，使用基于队列的迭代层序遍历实现
// 但是题目要求O(1)的额外空间

// 1. 使用队列类的辅助结构进行层序遍历 O(N)/O(N)
func connect(root *Node) *Node {
	if root == nil {
		return nil
	}

	// 首先设置root的next
	root.Next = nil
	queue := []*Node{root}
	var tmpQ []*Node
	num := 0
	for len(queue) != 0 {
		num = len(queue)
		tmpQ = make([]*Node, 0, 2*num) // 下层有当前层结点数的2倍
		// 考虑结点在队列中是树左边的结点则在队列数组的左边 的这样的存取顺序
		for i := 0; i < num; i++ {
			// 设置Next
			if i < num-1 {
				queue[i].Next = queue[i+1]
			} else {
				queue[i].Next = nil
			}
			// 添加下一层。注意顺序
			if queue[i].Left != nil {
				tmpQ = append(tmpQ, queue[i].Left)
			}
			if queue[i].Right != nil {
				tmpQ = append(tmpQ, queue[i].Right)
			}
		}
		// 更新queue
		queue = tmpQ
	}
	return root
}

// 2. 不使用额外空间的解法
// https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/tian-chong-mei-ge-jie-dian-de-xia-yi-ge-you-ce-j-3/
// 有两种类型的Next需要设置，一种是同一父节点，另一种则是相邻父节点
func connect2(root *Node) *Node {
	if root == nil {
		return nil
	}

	// 游标
	leftmost := root

	for leftmost.Left != nil {
		head := leftmost
		for head != nil {
			// 第1类Next ： 相同父节点
			head.Left.Next = head.Right
			// 第2类Next : 相邻父节点
			if head.Next != nil {
				head.Right.Next = head.Next.Left
			}

			// head在同一层上后移
			head = head.Next
		}
		// leftmost移到下一层
		leftmost = leftmost.Left
	}
	return root
}
```

### 1.9 填充每个节点的下一个右侧结点指针II

LeetCode 117

```go
和上一题相比，不再给出完美二叉树，而是普通的二叉树

//////////////////////////////////////////////////////

// 题目不再给 完美二叉树 ，而是 普通二叉树
// 其实解法和上一题几乎是一样的：
// 使用队列的层序遍历解法代码完全不用修改
// 常量空间解法则需要不停向右寻找非空结点作为Next
// 1. 使用队列类的辅助结构进行层序遍历 O(N)/O(N)
func connect(root *Node) *Node {
	if root == nil {
		return nil
	}

	// 首先设置root的next
	root.Next = nil
	queue := []*Node{root}
	var tmpQ []*Node
	num := 0
	for len(queue) != 0 {
		num = len(queue)
		tmpQ = make([]*Node, 0, 2*num) // 下层有当前层结点数的2倍
		// 考虑结点在队列中是树左边的结点则在队列数组的左边 的这样的存取顺序
		for i := 0; i < num; i++ {
			// 设置Next
			if i < num-1 {
				queue[i].Next = queue[i+1]
			} else {
				queue[i].Next = nil
			}
			// 添加下一层。注意顺序
			if queue[i].Left != nil {
				tmpQ = append(tmpQ, queue[i].Left)
			}
			if queue[i].Right != nil {
				tmpQ = append(tmpQ, queue[i].Right)
			}
		}
		// 更新queue
		queue = tmpQ
	}
	return root
}

// 2. 不使用额外空间的解法
// https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/solution/tian-chong-mei-ge-jie-dian-de-xia-yi-ge-you-ce-j-4/

// 全局游标
var leftmost, prev *Node

func connect2(root *Node) *Node {
	if root == nil {
		return nil
	}

	leftmost, prev = root, nil // 重置全局变量，避免提交时影响到其他测例
	cur := leftmost            // 游标

	for leftmost != nil {

		prev = nil
		cur = leftmost
		leftmost = nil

		for cur != nil {
			processchild(cur.Left)
			processchild(cur.Right)
			cur = cur.Next // 移动到本层的下一个节点
		}

	}
	return root
}

func processchild(child *Node) {
	if child != nil {
		if prev != nil {
			prev.Next = child // 如果本层之前存在节点，也就是prev!=nil，则将prev.Next指向当前child
		} else {
			leftmost = child // 说明这个child是本层第一个非空结点，也就是leftmost
		}

		prev = child
	}
}
```

### 1.10 二叉搜索树的最近公共祖先

LeetCode 235

```go
给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

//////////////////////////////////////////////////////

// 1. 和二叉树的LCA一样的处理
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
	if root == nil || root == p || root == q {
		return root
	}

	left, right := lowestCommonAncestor(root.Left, p, q), lowestCommonAncestor(root.Right, p, q)
	if left == nil {
		return right
	} else if right == nil {
		return left
	} else {
		return root
	}
}

// 2. 利用搜索树的数值排序性质
func lowestCommonAncestor2(root, p, q *TreeNode) *TreeNode {
	if root == nil || root == p || root == q {
		return root
	}

	// 缩减了一些搜索空间
	rv, pv, qv := root.Val, p.Val, q.Val
	if rv > pv && rv > qv { // 比两目标都大，去左子树找
		return lowestCommonAncestor2(root.Left, p, q)
	} else if rv < pv && rv < qv {
		return lowestCommonAncestor2(root.Right, p, q)
	} else {
		return root // 找到LCA
	}
}

```

### 1.11 二叉树的最近公共祖先

LeetCode 236

```go
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

///////////////////////////////////////////////////////

// Definition for a binary tree node.
type TreeNode struct {
	Val   int
	Left  *TreeNode
	Right *TreeNode
}

// 最近公共祖先必然是 自下而上 第一个子树中同时包含两个target 的结点
// 考虑一个辅助的递归函数，每次当其子树中搜索到其中一个target时，就往上返回true
// 当自下而上第一个 Left返回了true，right也返回了true的时候，就找到了 最近公共祖先 LCA
// 这个返回的true/false可以用*TreeNode的nil与非nil来表示
// 这里考虑true时返回内含的某个target(p或q)，false返回nil

func lowestCommonAncestor1(root, p, q *TreeNode) *TreeNode {
	if root == nil {
		return nil
	} // 找到底都没找到target
	if root == p || root == q { // 这里这么写： 因为是后序遍历，所以在下面找到的target一定会先返回
		return root
	} // 找到target之一

	// 当前结点非空，则继续找当前节点的子树（后序遍历）
	left := lowestCommonAncestor1(root.Left, p, q)
	right := lowestCommonAncestor1(root.Right, p, q)
	// 处理当前。
	if left != nil && right != nil {
		return root
	}
	if left != nil {
		return left
	}
	if right != nil {
		return right
	}
	return nil
}

// 2. 使用辅助函数的写法，啰嗦了一些

func lowestCommonAncestor2(root, p, q *TreeNode) *TreeNode {
	lca, _ := help(root, p, q)
	return lca
}

// 当找到LCA，则返回的结点不为空，而是LCA.
// 当LCA!=nil时，布尔值无意义
func help(root, p, q *TreeNode) (*TreeNode, bool) {
	if root == nil {
		return nil, false
	} // 找到底都没找到target

	// 当前结点非空，则继续找当前节点的子树（后序遍历）
	count := 0 // count=2就是得到两个true
	// 左
	lca1, left := help(root.Left, p, q)
	if lca1 != nil {
		return lca1, true
	}
	if left == true {
		count++
	}
	// 右
	lca2, right := help(root.Right, p, q)
	if lca2 != nil {
		return lca2, true
	}
	if right == true {
		count++
	}
	// 中
	if root == p || root == q {
		count++
	}
	if count == 2 {
		return root, true
	}
	if count == 1 {
		return nil, true
	}
	return nil, false
}

// 上面这个解法是正确的，但是很明显可以看出，它在找到LCA时不能及时返回，只能不断回去重新到达根节点处才真正返回给调用者

```

### 1.12 二叉树的序列化与反序列化

```go
序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

示例: 

你可以将以下二叉树：

    1
   / \
  2   3
     / \
    4   5

序列化为 "[1,2,3,null,null,4,5]"
提示: 这与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

说明: 不要使用类的成员 / 全局 / 静态变量来存储状态，你的序列化和反序列化算法应该是无状态的。

//////////////////////////////////////////

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */

type Codec struct {}

func Constructor() Codec {
    return Codec{}
}

// Serializes a tree to a single string.
func (this *Codec) serialize(root *TreeNode) string {
	if root==nil {return ""}

	queue := []*TreeNode{root}
	encoded := ""
	for len(queue) != 0 {	// 队列非空
		root = queue[0]; queue = queue[1:]	// 队头出队
		if root != nil {
			queue = append(queue, root.Left, root.Right)	// 将左右子节点从队尾入队
			encoded += strconv.Itoa(root.Val) + ","
		} else {
			encoded += "null,"
		}
	}

	// 去除右边的"null,"，没必要存储
	encoded = trimRightNulls(encoded)
	// 去除末尾","，加上左右括号
	encoded = "[" + encoded[:len(encoded)-1] + "]"
    fmt.Println(encoded)

	return encoded    
}

func trimRightNulls(data string) string {
	target := "null,"
	size := len(target)	//5
	for len(data)>size && data[len(data)-size:]==target {
		data = data[:len(data)-size]
	}
	return data
}

// Deserializes your encoded data to tree.
func (this *Codec) deserialize(encoded string) *TreeNode {    
    if len(encoded) == 0 {return nil}
    // 去除左右括号
	encoded = encoded[1:len(encoded)-1]
	// 边界判断
	if len(encoded)==0 {return nil}
	// 按","分割字符串
	items := strings.Split(encoded, ",")
	// 构造根节点
	rootVal, _ := strconv.Atoi(items[0])	// 第一个值不可能是null
	root := &TreeNode{Val:rootVal}

	// 构建辅助队列
	queue := []*TreeNode{root}
	i := 0
	for {
		if i++; i >= len(items) {		// i先+1，如果超界，则退出
			break
		}

		node := queue[0]; queue = queue[1:]		// 弹出队头

		if items[i] != "null" {
			leftVal, _ := strconv.Atoi(items[i])
			node.Left = &TreeNode{Val:leftVal}
			queue = append(queue, node.Left)
		}

		if i++; i >= len(items) {		// i先+1，如果超界，则退出
			break
		}
		if items[i] != "null" {
			rightVal, _ := strconv.Atoi(items[i])
			node.Right = &TreeNode{Val:rightVal}
			queue = append(queue, node.Right)
		}
	}

	return root    
}


/**
 * Your Codec object will be instantiated and called as such:
 * obj := Constructor();
 * data := obj.serialize(root);
 * ans := obj.deserialize(data);
 */
```

### 1.13 序列化和反序列化二叉搜索树

