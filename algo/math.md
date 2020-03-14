---
title: "编程中的数学操作"
date: 2020-03-12T11:39:11+08:00
draft: false
categories: ["algo"]
tags: ["数学"]
keywords: ["公因数"]
---

## 0. 导语

这篇文章用来记录编程中遇到的数学操作的实现

## 1. 求两数的最大公因数

**辗转相除法**

```go
func gcd(a, b int) int {
    tmp := a
    for tmp != 0 {      // 例如 a=9, b=6 => a=6, b=3 => a=3, b=0(tmp=0) => a就是最大公因数
        tmp = a % b
        a = b
        b = tmp
    }
    return a
}
```

## 2. 求两数的所有公因数

做法：先求最大公因数（最大公约数）$gcd$，再对$i=1~sqrt(gcd)$进行遍历尝试$gcd / i$是否为整数

```go
// 计算所有公因数并降序输出
func calcAllCommonFactors(a, b int) []int {
    // max common factor
    maxCF := gcd(a, b)
    res := make([]int, 0)
    for i:=1; i*i <= maxCF; i++ {
        if maxCF % i == 0 {
            res = append(res, i, maxCF / i)
        }
    }
    
    // 排序
    sort.Slice(res, func(i,j int) bool {
        return res[i] > res[j]
    })

    return res
}
```

## 3. 分式约分

其实就是先求最大公因数，再分子分母同除最大公因数就好。