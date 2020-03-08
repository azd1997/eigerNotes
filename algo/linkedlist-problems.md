---
title: "链表相关题目集锦"
date: 2020-03-08T17:40:37+08:00
draft: false
categories: ["algo"]
tags: ["链表"]
keywords: ["链表","递归"]
---

## 0. 链表问题集锦

在进行链表题目的训练之前，先给出链表结点的定义与链表的遍历框架。

- 链表结点的定义

```go

// 单链表结点
type Node struct {
	Val int
	Next *Node
}

// 双链表结点
type Node struct {
	Val int
	Pre, Next *Node
}

```

- 递归遍历框架

```go
// 传入当前结点，或者说剩余部分链表的头结点
func traverse(node *Node) {
	// 到尾则返回
	if node == nil {return}

	// optional: do something

	// 递去
	traverse(node.Next)

	// optional: do something
}
```

- 迭代遍历框架

```go
func traverse(node *Node) {
	if node == nil {return}

	cur := node
	for cur != nil {
		// do something
	}
}

从两种框架来看，递归遍历易于实现那些需要对链表倒序遍历或者输出的场景，因为它使用了函数调用的系统栈。迭代遍历自然也可以实现倒序访问，但是需要一个辅助栈
```

## 1. 从尾到头打印链表

利用上面的递归遍历框架，这题直接写：

```go
func reversePrint(head *Node) []int {
    res := make([]int, 0)
    reverse(head, &res)    
    return res
}

func reverse(head *Node, res *[]int) {
    if head == nil {return}
    
    // 从尾到头打印，先递去，归来再打印
    reverse(head.Next, res) 
    *res = append(*res, head.Val)
}

```

迭代写法：

```go
func reversePrint(head *Node) []int {
    res := make([]int, 0)

    stack := make([]int, 0)
    cur := head
    for cur != nil {
        stack = append(stack, cur.Val)    
    }
    
    for len(stack) != 0 {
        res = append(res,stack[len(stack)-1] )
        stack = stack[:len(stack)-1]
    }
    
    return res
}
```

## 2. 删除链表的结点

这道题要求的是删除链表中给定值的结点，且链表中所有节点值不相同。

对于删除链表中的结点，最好先构建一个dummyHead，再去做删除工作。
不这么做的话，直接从head开始，需要额外处理head是待删节点的情况。

迭代做法：

```go
func deleteNode(head *Node, val int) *Node {
    dummyHead := &Node{Next: head}
    pre := dummyHead
    for pre.Next != nil {
        if pre.Next.Val == val {
            pre.Next = pre.Next.Next
            break        
        }
        pre = pre.Next
    }
    return dummyHead.Next    
}
```

递归做法：

```go
func deleteNode(head *Node, val int) *Node {
    dummyHead := &Node{Next: head}
    return delete(dummyHead, val).Next
}

func delete(head *Node, val int) *Node {
    // 到链表最后一个节点
    if head.Next == nil {
        return head    
    }    
    if head.Next.Val == val {
        head.Next == head.Next.Next
        return head        
    }
    // 没找到的话继续向后查找
    head.Next = delete(headd.Next, val)
    return head
}

```

## 3. 链表中倒数第k个结点

链表中倒数第k个结点。

注意点：k的有效性

思路：
- 遍历后将所有节点值输出到数组，利用数组下标取倒数第k个结点值
- 一次遍历得到链表长度length，再一次遍历遍历length-k，到达目标节点
- 一次遍历，先右移k次，再用双指针，p1从head出发，p2从当前出发，p2遍历到尾时，p1所指即为所求

一次遍历解法迭代实现：

```go
func getKthFromEnd(head *Node, k int) *Node {
    p1, p2 := head, head

    // 先定位到kthNode
    for i:=0; i<k; i++ {
        if p2 != nil {
            p2 = p2.Next
        } else {
            return nil      // 链表长度<k
        }
    }

    // 再双指针
    for p2 != nil {
        p2 = p2.Next
        p1 = p1.Next
    }

    return p1
}
```


## 4. 反转链表

迭代做法：从左向右交换指针方向
```go
func reverseList(head *Node) *Node {
    // 边界情况
    if head == nil || head.Next == nil {return head}    

    // 反转链表
    var pre, tmp *Node
    cur := head
    for cur != nil {
        tmp = cur.Next
        cur.Next = pre
        pre = cur
        cur = tmp
    }
    return pre
}
```


反转链表，按照链表的递归遍历，在回溯时修改指针指向

```go
func reverseList(head *Node) *Node {
    // 边界情况
    if head == nil || head.Next == nil {return head}
    // 不仅仅是特殊情况处理，也是递归时到链表末尾的边界

    // 递去
    ret := reverseList(head.Next)
    
    // 递归时转向，并把当前节点返回    
    head.Next.Next = head   // 指针转向
    head.Next = nil

    // 返回新的头结点
    return ret
}
```


## 5. 合并两个排序的链表

合并两个排序的链表，其实这个题思路很简单就是构建一个dummyHead，然后按双指针写法将两个链表头部较小者出列接到dummyHead后边。

双指针解法：

```go
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    dummyHead := &ListNode{}
    p, p1, p2 := dummyHead, l1, l2
    for p1 != nil && p2 != nil {
        if p1.Val <= p2.Val {
            p.Next = p1
            p = p.Next
            p1 = p1.Next    
        } else {
            p.Next = p2
            p = p.Next
            p2 = p2.Next
        }
    }

    if p1 != nil {
        p.Next = p1
    } else {
        p.Next = p2
    }

    return dummyHead.Next
}
```

递归解法：

递归解法的思路则不是构造伪头结点，而是以l1或者l2作为新链表头结点

```go
//  返回两个链表合并后的链表的头结点
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    if l1 == nil {return l2}    
    if l2 == nil {return l1}

    if l1.Val <= l2.Val {
        l1.Next = mergeTwoLists(l1.Next, l2)        
        return l1
    } else {
        l2.Next = mergeTwoLists(l1, l2.Next)        
        return l2
    }
}
```

## 6. 两个链表的第一个公共结点

常规思路的话，是使用哈希表/集合记录一条链表的所有节点，再遍历另一条链表，
第一个在哈希集合中遇到的结点就是第一个公共结点。

想要优化空间，则需要想办法在遍历两条链表的时候能够在公共结点相遇，这样能找到公共结点
甚至是第一个公共结点。

但是两条链表长度并不一定一样

```go
// 图示：
// 链表A前半段              a1 -> a2 ->
//  公共段                                              c1 -> c2 -> c3
// 链表B前半段   b1 -> b2 -> b3 ->
// 
// 假设双指针(p1, p2)，每个指针都走一遍两条链表，路径如下：
// p1路径： a1 -> a2 -> c1 -> c2 -> c3  =>  b1 -> b2 -> b3 ->  c1 -> c2 -> c3
// p2路径： b1 -> b2 -> b3 ->  c1 -> c2 -> c3  =>  a1 -> a2 -> c1 -> c2 -> c3
// 如果两条链表没有交集，那么这样走一遍后是不会相遇的
// 像上面图示中，p1,p2会在"c1"处相遇，这就是相交点

func getIntersectionNode(headA, headB *ListNode) *ListNode {
    // 边界    
    if headA == nil || headB == nil {return nil}
    // 双指针
    p1, p2 := headA, headB
    for {
        // 如果双方同时到尾，说明走完了
        if p1 == nil && p2 == nil {return nil}

        // 换一条“跑道”继续移动
        if p1 == nil {p1 = headB}    
        if p2 == nil {p2 = headA}
        
        // 找到第一个交点
        if p1 == p2 {return p1}  

        // 否则向后移动
        p1, p2 = p1.Next, p2.Next  
    }
    // 返回nil
    return nil
}
```


## 7. 圆圈中最后剩下的数字

0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

这道题是一个约瑟夫环形问题，与环形链表密切相关。

当然做题的时候并不一定需要构建环形链表

首先，脑海里要抽象出（或者在纸上画出）一个结点数量为n的一个环，删除第m个数字，那么就是删除
第m%n个数字。

其次，假如说不使用数据结构记录删除一些数之后数列的状态，那么是没办法解这道题的。

因此需要采用数组来存储0~n-1。使用数组有个好处是可以方便的通过`m%n`来索引某个元素

但是因为需要不断删除元素，数组需要不断的缩小，这意味着比较多的数据搬迁。

使用环形链表删除元素优化了，但是获取下一个元素却需要遍历。

两种结构存储都可以，但各有优劣。

下面使用数组记录数据，解决这道题


```go
func lastRemaining(n int, m int) int {
    // 初始环
    ring := make([]int, n)
    for i:=0; i<n; i++ {
        ring[i] = i
    }    

    //  循环删除
    idx := 0
    for len(ring) > 1 {
        idx = (idx+m)%len(ring)
        ring = append(ring[:idx], ring[idx+1:]...)
    }
    return ring[0]
}
```

事实上，提交后，无论是使用数组还是使用链表来模拟进行删除步骤，都是会超时的。

能够通过的解法是数学解法，

把问题考虑成n个人成环的问题，序号分别为 ：
$$0,1,2,...,n-1$$
考虑第一次从0开始找到第m个人出圈(m可能比n大，所以使用$k=(m-1)\%n$)，现在序号变成了：
$$0,1,2,..., k-1, k+1, ... ,n-1$$
现在出发点为k+1这号，把k+1后面的移到序列前部，得到：
$$k+1, ... ,n-1,0,1,2,..., k-1$$
显然，可以将之映射为：
$$0,1,2,..., (n-1)-(k+1), n-k+-1, ... ,n-2$$
记原始数字为x，映射后的数字为y，两者的转换：
$$x = (y + (k+1)) \% n$$

对于题目的n个数字形成的圆圈，以及不断删除第m个数字，因此把最后剩下的数字记作$f(n,m)$

那么假设n个数字删掉一个变成n-1个后，最后剩下的那个数字就可以表示为$f(n-1,m)$

那么把该符号代到x,y的转换公式，得到最后剩下的原始数字为$(f(n-1,m) + (k+1)) \% n$

而这个数字也就是最开始的$f(n,m)$，于是代入下式：
$$f(n,m) = (f(n-1,m) + (k+1)) \% n$$
得到：
$$f(n,m) = (f(n-1,m) + k +1) \% n$$
得到：
$$f(n,m) = (f(n-1,m) + (m-1)\%n +1) \% n$$
根据数学性质：
$$(a\%n+b)\%n = (a+b)\%n$$
所以上式继续得到：
$$f(n,m) = (f(n-1,m) + m-1 +1) \% n$$
即
$$f(n,m) = (f(n-1,m) + m) \% n$$
并且初始有 $f(1,m)=0$

好了，到这，其实就是个动态规划，这个动态规划可以用迭代或者递归实现。


```go
func lastRemaining(n int, m int) int {
    last := 0   // 初始位是0,(f(1,m)=0)
    for i:=2; i<=n; i++ {
        last = (last + m) % i
    }
    return last
}
```

## 8. 二叉搜索树与双向链表

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

节点的left指针作双向链表的prev，right作next。

并且返回的循环双向链表的head(虚拟头结点的next指向原搜索树最小值)

思路：中序遍历

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/

// 中序遍历
class Solution {

    // 全局变量
    private Node pre;
    private Node head, tail;

    public Node treeToDoublyList(Node root) {
        // 特殊情况
        if (root == null) return null;

        // 中序遍历，将中间处理好，还剩下head.left和tail.right没处理
        inorder(root);

        head.left = tail;
        tail.right = head;

        return head;
    }

    // 中序遍历，对root子树作中序遍历
    private void inorder(Node root) {
        if (root == null) return;   // 递归终止：到了叶节点以下

        // 左子树遍历
        inorder(root.left);

        // 处理当前

        root.left = pre;     // 先指向pre，接下来要修改pre.right,需要分情况
        if (pre == null) {
            // 那说明是head
            head = root;
        } else {
            // 说明不是head
            pre.right = root;
        }

        pre = root;     // pre后移
        tail = root;    // tail也后移。 （其实pre,tail只要一个就可以，这里只是方便阅读）

        // 右子树遍历
        inorder(root.right);
    } 
}
```

迭代做法（借助辅助栈进行中序遍历）

```java
class Solution {

    public Node treeToDoublyList(Node root) {
        // 特殊情况
        if (root == null) return null;

        // 辅助变量
        Node pre = null, head = null, tail = null;

        // 基于栈的中序遍历
        Stack<Node> stack = new Stack<Node>(); 
        Node cur = root;
        while (!stack.isEmpty() || cur != null) {
            // 如果cur不为空，将cur所有的左子孙入栈
            while (cur != null) {
                stack.push(cur);
                cur = cur.left;
            }

            // 出栈
            cur = stack.pop();
            cur.left = pre;
            if (pre == null) {
                head = cur;
            } else {
                pre.right = cur;
            }
            pre = cur;
            tail = cur;

            // 向右子树找
            cur = cur.right;
        }

        head.left = tail;
        tail.right = head;
        return head;
    }
}
```

## 9. 复杂链表的复制

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

思考：

由于是具备两个指针的复杂链表，问题的麻烦在于，深度拷贝之后，加入根据next指针，拷贝了其next后继，但是其random后继也需要生成
这会导致重复生成两套链表。
为了解决这个问题，必须将复制的链表结点与原先的链表形成映射，使用一个哈希表解决之，Java里边哈希表使用HashMap 

也就是 迭代（按next迭代） + 哈希表

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return head;

        // 原链表结点 -> 新链表结点
        HashMap<Node, Node> map = new HashMap<>();
        for (Node cur = head; cur != null; cur = cur.next) {
            map.put(cur, new Node(cur.val));
        }

        // 重新构建指针链接
        for (Node cur = head; cur != null; cur = cur.next) {
            if (cur.next != null) map.get(cur).next = map.get(cur.next);
            if (cur.random != null) map.get(cur).random = map.get(cur.random);
        }
        // 返回新的链表头
        return map.get(head);        
    }
}
```

此外，由于有两个指针，相当于图中每个节点有两个方向可以前进，也就是说可以使用BFS或者DFS来做这道题，当然还是得使用哈希表。

这里再使用DFS解一遍：

```java
class Solution {
    public Node copyRandomList(Node head) {
        // 原链表结点 -> 新链表结点
        HashMap<Node, Node> map = new HashMap<>();
        return dfs(head, map);        
    }

    // dfs遍历，返回拷贝后的头结点
    private Node dfs(Node cur, HashMap<Node, Node> map) {
        if (cur == null) return null;

        // 拷贝当前结点
        if (map.containsKey(cur)) return map.get(cur);     // 哈希表已存在
        // 否则新建节点，加入到哈希表，再修改新结点的后继指针
        Node tmp = new Node(cur.val);
        map.put(cur, tmp);
        tmp.next = dfs(cur.next, map);
        tmp.random = dfs(cur.random, map); 
        // 返回深拷贝后的结点tmp
        return tmp;
    }
}
```