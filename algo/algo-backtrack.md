---
title: "回溯算法"
date: 2020-03-13T19:28:20+08:00
draft: false
categories: ["algo"]
tags: ["回溯"]
keywords: ["回溯"]
---

## 0. 导语

## 1. 回溯思想及应用

## 2. 回溯框架

解决一个回溯问题的过程其实是在遍历一颗**决策树**。 主要思考三个问题：

1. 路径：也就是已经做出的选择。
2. 选择列表：也就是你当前可以做的选择。
3. 结束条件：也就是到达决策树底层，无法再做选择的条件。

框架：

```go
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

其实回溯的过程就特别像DFS...

## 3. 回溯例题

### 3.1 全排列问题

### 3.2 N皇后问题



## 参考材料

- [回溯算法详解](https://leetcode-cn.com/problems/n-queens/solution/hui-su-suan-fa-xiang-jie-by-labuladong/)