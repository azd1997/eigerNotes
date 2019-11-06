---
title: "覃超数据结构与算法7天训练营笔记"
date: 2019-11-04T20:39:10+08:00
draft: false
categories: ["algo"]
tags: ["数据结构", "算法"]
keywords: ["五遍刷题法"]
---

## 0. 前言

### 0.1 学习目标

- 对于算法和数据结构理解达到职业顶尖级别
- 一线互联网公司面试
- Leetcode 300+ 积累

## 0.2 学习方法

参考书籍：《异类：不一样的成功启示录》

三步走：

1. chunk it up 切碎知识点（庖丁解牛、脉络连接）
   - 见sec1
2. Deliberate Practicing 刻意练习
   - 反复练习
   - 过遍数（五毒神掌）: 五遍刷题法
     - 切题四件套（练习和面试时需要的做题过程）
       - clarification 看到题时和面试官多确认几遍，保证自己理解没错
       - possible solutions 想所有可能的解法
         - compare (time/space)
         - optimal (加强)
       - coding (多写)
       - test cases (多测一些测试样例，有始有终)
     - 刷题第一遍：
       - 5~10分钟读题加思考
       - 然后直接看解法。注意！多解法，比较解法优劣
       - 背诵、默写好的解法
     - 刷题第二遍：
       - 马上自己写 -> 提交到leetcode反复debug至通过
       - 多种解法比较、体会 -> 优化。 leetcode有统计执行时间和内存消耗
     - 刷题第三遍
       - 过了一天后，再重复做题
       - 不同解法的熟练程度 -> 专项练习
     - 刷题第四遍
       - 过了一周再反复回来练习，同时对于不熟练的题目再进行专项练习
     - 刷题第五遍
       - 面试前一周恢复性训练
   - 练习弱点
3. Feedback 反馈（主动式、被动式）
   - 即时反馈
   - 主动型反馈（自己去找）
     - 高手代码（Github, Leetcode, etc）
     - 第一视角直播
   - 被动式反馈（高手给你指点）
     - code review

## 1. 总览

### 1.1 数据结构

- 一维
  - 基础： 数组array(string), 链表linked list
  - 高级：栈stack, 队列queue, 双端队列deque, 集合set, 映射map, etc
- 二维
  - 基础： 树tree, 图graph
  - 高级： 二叉搜索树binary search tree (red-black tree, AVL), 堆heap, 并查集disjoint set, 字典树trie, etc
- 特殊
  - 位运算 Bitwise, 布隆过滤器bloom filter
  - LRU Cache

### 1.2 算法

所有算法都可以使用下列三种运算操作组合表示而成

- if-else, switch -> branch
- for, while loop -> iteration
- 递归（recursion） (Divide&Conquer, Backtrace)

高级算法：

- 搜索search, 深度优先搜索Depth first search, 广度优先搜索Broad first search, A*, etc
- 动态规划 Dynamic Programming
- 二分查找 binary search
- 贪心 greedy
- 数学 math, 几何 geometry

注意：在头脑中各种算法的思想和代码模板

### 1.3 脑图

![tc01](/images/tc01.svg)

## 2. 训练准备与复杂度分析

### 2.1 训练环境设置

推荐 VSCode + Leetcode插件

### 2.2 编码技巧

刻意练习常用编辑器、IDE的快捷键操作

### 2.3 编程风格

参考《clean code》

- 自顶向下。

就是说每一个代码文件，要像写杂志一般，把最核心最重要的代码放在前面，细节则放到末尾。比如说一个命令行程序，可以将总的处理命令行参数的函数放在前头，而具体的各个命令所调用的函数在后，工具函数则放在文件最末。

### 2.4 时间复杂度

#### 2.4.1 常见时间复杂度

- O(1)
- O(logn)
- O(n)
- O(n^2)
- O(n^3)
- O(2^n)
- O(n!)

只看最高时间复杂度的运算。

经典的斐波那契数列的例子：

```java
int fib(int n) {
    if (n<=2) return n;
    return fib(n-1) + fib(n-2);
}

// 时间复杂度 O(k^n)
```

**Tip:** 编程时一定要对自己写的代码分析时间空间复杂度，要养成习惯！！！

#### 2.4.2 算法的意义

例子：

```java
// 1. 从1到n循环累加
y = 0
for i=1 to n:
    y += i

// 2. 利用等差数列求和公式
y = n * (n+1) / 2

显然采用算法2时间复杂度为常数级
```

#### 2.4.3 更复杂的情况——递归

```java
int fib(int n) {
    if (n<=2) return n;
    return fib(n-1) + fib(n-2);
}
```

直接用递归时间复杂度为指数级，性能极差。例如求 f(6), 因为在递归的过程中，f(5),f(4), ..., f(1), f(0)被多次重复计算。

那么优化策略就很明显了： 缓存中间结果， 或者直接使用循环而非递归实现

#### 2.4.4 主定理

对于递归代码，其时间复杂度有**主定理**进行描述和证明。具体理论这里略去。

直接给出常用结论：

|算法|递归关系|运行时间复杂度|
|----|----|----|
|binary search|T(n)=T(n/2)+O(1)|O(logn)|
|binary tree traversal|T(n)=2T(n/2)+O(1)|O(n)|
|optimal sorted matrix search|T(n)=2T(n/2)+O(logn)|O(n)|
|merge sort|T(n)=2T(n/2)+O(n)|O(nlogn)|

#### 2.4.5 思考题

- 二叉树遍历-前序、中序、后序： 时间复杂度多少？    O(n)
- 图的遍历： 时间复杂度多少     O(n)
- 搜索算法： DFS、BFS时间复杂度多少     O(n)
- 二分查找： 时间复杂度多少？       O(logn)

## 3. 数组、链表和跳表

### 3.1 数组与链表

- 数组支持常数级别的按下标访问（随机访问），插入和删除复杂度为O(n)
- 链表在找到目标结点之后插入和删除为常数级，查找某个结点需要遍历，复杂度O(n)

|operation|array|linkedlist|
|-|-|-|
|prepend|O(n)，某些情况下可以O(1)|O(1)|
|apppend|不扩容，O(1)|不计算查找，O(1)|
|lookup|按下标，O(1)|O(n)|
|insert|O(n)|O(不计算查找，O(1))|
|delete|O(n)|不计算查找， O(1)|

### 3.2 跳表

Redis、LevelDB使用跳表。

跳表是为了优化链表的查找性能的，