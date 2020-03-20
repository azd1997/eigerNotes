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
