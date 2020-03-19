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

## 4. 求x的n次幂

快速求幂法

```go
// x ^ (2n) = (x^n)^2
// x ^ (2n+1) = (x^n)^2 * x
// 用这种思路求幂的算法叫快速幂算法

func myPow(x float64, n int) float64 {
	if n < 0 { // 负数幂时的处理
		x = 1 / x
		n = -n
	}
	return fastPow(x, n)
}

func fastPow(x float64, n int) float64 {
	if n == 0 {
		return 1.0
	}
	half := fastPow(x, n/2)
	if n%2 == 0 { // n为偶数
		return half * half
	} else { // n为奇数
		return half * half * x
	}
}
```

## 5. 判断n是否为完全平方数

```go
// 给定一个正整数 num，编写一个函数，如果 num 是一个完全平方数，则返回 True，否则返回 False。

// 直接二分查找某个数的平方等于n就好了，贼简单

func isPerfectSquare(num int) bool {
	l, r, mid, mid2 := 1, num, 0, 0
	for l <= r {
		mid = (r-l)/2 + l
		mid2 = mid * mid
		if mid2 == num {
			return true
		} else if mid2 > num {
			r = mid - 1
		} else {
			l = mid + 1
		}
	}
	return false
}

// 通过了提交
// 但是有个问题：mid*mid有可能溢出
// 修正如下：

func isPerfectSquare2(num int) bool {
	l, r, mid := 1, num, 0
	for l <= r {
		mid = (r-l)/2 + l
		if num/mid == mid && num%mid == 0 {
			return true
		} else if (num/mid > mid) || (num/mid == mid && num%mid != 0) {
			l = mid + 1
		} else {
			r = mid - 1
		}
	}
	return false
}

// 牛顿迭代法
// 原问题等价于求 x*x - num = 0的根，这个根是不是个正整数
// https://leetcode-cn.com/problems/valid-perfect-square/solution/you-xiao-de-wan-quan-ping-fang-shu-by-leetcode/
// 先选取初始近似点，然后不断向真正的解逼近
func isPerfectSquare3(num int) bool {
	if num == 1 {
		return true
	}

	x := num / 2 // 初始近似点
	for x*x > num {
		x = (x + num/x) / 2
	}
	return x*x == nums
}
```

## 6. 牛顿迭代法



