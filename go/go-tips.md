---
title: "Go语言猎奇"
date: 2019-11-06T09:33:59+08:00
draft: false
categories: ["go"]
tags: ["Go", "坑"]
keywords: []
---

本篇博客收集遇到的各种各样奇怪的平时难注意到的Go语言使用细节，或者说”坑“。

## 1. 切片打散

```go
nums := []int{0,1,2,3}

// 1. 这个挺容易理解的
nums = append([]int{1,2,3}, nums[:0])
fmt.Println(nums)   // [1 2 3]

// 2. 奇怪的来了
a := nums[4]    // panic: index out of range
nums = append([]int{1,2,3}, nums[4:])  // 编译通过
fmt.Println(nums)   // [1 2 3]
nums = append([]int{1,2,3}, nums[5:])  // panic: index out of range
fmt.Println(nums)   // [1 2 3]
```

在使用 `...` 打散切片时，切片索引可以是切片长度！！但不能更大！
