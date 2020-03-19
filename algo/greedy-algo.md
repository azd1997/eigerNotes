---
title: "贪心算法"
date: 2020-03-19T11:55:30+08:00
draft: false
categories: ["algo"]
tags: ["贪心"]
keywords: ["贪心"]
---

## 0. 导语

## 1. 介绍

## 2. 例题

### 2.1 最长回文串

```go
// 给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。

// 在构造过程中，请注意区分大小写。比如 "Aa" 不能当做一个回文字符串。

// 注意:
// 假设字符串的长度不会超过 1010。

// 示例 1:

// 输入:
// "abccccdd"

// 输出:
// 7

// 解释:
// 我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。

/////////////////////////////////////////////
// 其实就是所有字母都向下圆整到偶数个，加在一起得到count个字母
// 如果还有其他的单个字母，随便一个，放在回文的中间，count+1

func longestPalindrome(s string) int {
	// 哈希表
	m := make(map[byte]int)
	for i := 0; i < len(s); i++ {
		m[s[i]]++
	}
	// 统计
	hasLonely := false // 有落单的没
	count := 0
	for _, num := range m {
		if num%2 != 0 {
			hasLonely = true
			count += num - 1
		} else {
			count += num
		}
	}
	if hasLonely {
		return count + 1
	}
	return count
}
```
