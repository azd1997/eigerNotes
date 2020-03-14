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

### 0.5 参考

- [我的Github](https://github.com/azd1997/Leetcode-training/tree/master/ltalgo/tree-traverse)
- [二叉树遍历（前序、中序、后序）](https://www.jianshu.com/p/456af5480cee)