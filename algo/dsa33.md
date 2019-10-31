---
title: "字符串匹配算法"
date: 2019-10-30T07:06:46+08:00
draft: false
categories: ["algo"]
tags: ["字符串匹配"]
keywords: ["BF", "RK", "BM", "KMP"]
---

## 0. 导言

## 1. BF算法

## 2. RK算法

## 3. BM算法

### 3.1 核心思想

### 3.2 原理

#### 3.2.1 坏字符规则

#### 3.2.2 好后缀规则

### 3.2 go语言实现

```go
/*
字符串匹配算法之BM算法。性能比常用的KMP快3~4倍
*/
package ex33_bm

// 长字符串str: 待匹配的目标字符串
// 模式串b
// 例如 str = "abcacabdc", b = "abd" . 看str中是否有b这个子串

const CHAR_SIZE = 256

// generateBC 生成哈希表BC
// 由于模式串中要经常求字符位置，使用字符串遍历的话效率太低，于是使用哈希表。
// 在go中当然可以使用map直接做，但是这里考虑到字符串中字符都是ASII编码，可以用0~255表示，
// 于是可以使用数组来实现一个性能更好的哈希表，键为0~255的数字，也就是数组的下表，值则是每个字符在模式串出现的位置。
// （相同字符的话根据坏字符规则，记靠左的（模式串中下标更小的）的字符的下标）
func generateBC(b string) (bc [CHAR_SIZE]int){
	// 初始化bc，值全为-1，表示不存在
	for i:=0; i<CHAR_SIZE; i++ {
		bc[i] = -1
	}

	// 从前向后遍历模式串，在bc中记录其位置
	var ascii int
	for i:=0; i<len(b); i++ {
		ascii = int(b[i])	// 将ASCII字符（占一个byte）转为int整数
		//fmt.Println("asii=", ascii)
		bc[ascii] = i
	}

	return bc	// 此时bc记录了模式串中每个字符出现的最右位置下标
}

// BMOnlyBadChar 只实现坏字符规则的BM算法，可能会出现模式串向后滑动的情况。
// 返回index是str中为b的子串最左出现的位置
func BMOnlyBadChar(str, b string) (i int) {
	// 两个字符串长度
	n, m := len(str), len(b)

	// 生成模式串字符索引哈希表
	bc := generateBC(b)

	//
	//i = 0	// 模式串最左字符在str中对齐位置的下标
	for i <= n-m {	// i=n-m时模式串就滑动到目标串末尾了
		var j int
		for j=m-1; j>=0; j-- {	// 从右向左一一匹配，发现不同就退出循环
			if str[i+j] != b[j] {
				break
			}
		}
		// 循环结束之后记录有j的位置
		if j < 0 {		// 完全匹配则返回首部对齐位置i
			return i
		}

		// 否则根据坏字符规则向后（右）滑动
		rightBad := bc[int(str[i+j])]		// 坏字符str[i+j] 在bc中存储的索引
		i = i + j - rightBad
	}

	return -1		// 滑到底都没找到匹配的子串，返回-1表示没有匹配子串
}

// generateGS 预处理模式串，生成后缀数组suffix和前缀数组prefix
// suffix存储 好后缀的后缀子串 们 在模式串中除该后缀子串外的其他匹配的子串的 最右位置。并且后缀子串们用数组下标来表示了
// prefix存储 这些后缀子串们是否是 能够匹配 模式串的前缀子串
func generateGS(b string) (suffix []int, prefix []bool) {
	// 例如模式串 b = cabcab, 则其后缀子串、长度、suffix存值， prefix存值分别为：
	//  后缀子串		长度		suffix存值		prefix存值
	//  	 b		1		suffix[1]=2		prefix[1]=false
	// 		ab		2		suffix[2]=1		prefix[2]=false
	//	   cab		3		suffix[3]=0		prefix[3]=true
	//	  bcab		4		suffix[4]=-1	prefix[4]=false
	//	 abcab		5		suffix[5]=-1	prefix[5]=false

	l := len(b)

	// 初始化suffix/prefix
	suffix, prefix = make([]int, l), make([]bool, l)		// 这里第一个位置不用。当然也可以把上边那个表中下标都减1处理。
	for i:=0; i<l; i++ {
		suffix[i], prefix[i] = -1, false
	}

	// 填充suffix,prefix真值
	for i:=0; i<l-1; i++ {		// 遍历模式串的所有前缀子串。 b[0:i]。所谓公共后缀子串就是 b和b[0:i]的公共后缀子串。
								// 如果发现公共后缀子串就是 b[0:i]，那说明此时后缀子串与前缀子串匹配了。
		j := i
		k := 0	// 公共后缀子串的长度
		for j>=0 && b[j]==b[l-1-k] {	// 这种写法其实不好理解，其实就是比较 b[0:i] 的后k个字符与 模式串的后k个字符是否完全一致，
										// 若是，则说明有公共后缀，记录此时公共后缀子串位置j,只是由于还减1了一次，所以存j+1
			j--
			k++
			suffix[k] = j+1
		}
		if j == -1 {
			prefix[k] = true
		}
	}

	// 来手写一遍计算过程：
	// b = "cabcab"
	// i = 0;
		// j=0;k=0 => b[j]=c != b[l-1-k]=b[5]=b => 不进for 也不进if
	// i = 1;
		// j=1;k=0 => b[j]=a != b[l-1-k]=b[5]=b => 不进for 也不进if
	// i = 2;
		// j=2;k=0 => b[j]=b == b[l-1-k]=b[5]=b => 进for
		// => j=1;k=1, suffix[1]=2 => b[j]=a == b[l-1-k]=b[4]=a => 进for
		// => j=0;k=2 => suffix[2]=1 => b[j]=c == b[l-1-k]=b[3]=c => 进for
		// => j=-1;k=3 => j<0 => 不进for => 进if => prefix[3] = true
	// i = 3;
		// j=3;k=0 => b[j]=c == b[l-1-k]=b[5]=b => 不进for
	// i = 4;
		// j=4;k=0 => b[j]=a == b[l-1-k]=b[5]=b => 不进for
	// 与前面结果一致

	return
}

// BM 同时使用坏字符和好后缀两种规则的BM字符串匹配算法。
func BM(str, b string) (i int) {
	// 两个字符串长度
	n, m := len(str), len(b)

	// 预处理模式串，生成模式串字符索引哈希表
	bc := generateBC(b)

	// 预处理模式串， 生成suffix/prefix
	suffix, prefix := generateGS(b)

	//
	//i = 0	// 模式串最左字符在str中对齐位置的下标
	for i <= n-m {	// i=n-m时模式串就滑动到目标串末尾了
		var j int	// 最右出现坏字符 在模式串中最右出现的的下标
		for j=m-1; j>=0; j-- {	// 从右向左一一匹配，发现不同就退出循环
			if str[i+j] != b[j] {
				break
			}
		}
		// 循环结束之后记录有j的位置
		if j < 0 {		// 完全匹配则返回首部对齐位置i
			return i
		}

		// 按照坏字符规则得到的向右滑动距离
		rightBad := bc[int(str[i+j])]		// 坏字符str[i+j] 在bc中存储的索引
		x := j - rightBad

		// 按照好后缀规则得到的向右滑动距离
		y := 0
		if j < m-1 {	// j是最右出现坏字符，j<m-1说明存在好后缀
			y = moveByGS(j, m, suffix, prefix)
		}

		// 真正的滑动距离取x,y最大值，这样可以避免单一坏字符规则产生的向左滑动问题。
		if x >= y {
			i = i + x
		} else {
			i = i + y
		}
	}

	return -1		// 滑到底都没找到匹配的子串，返回-1表示没有匹配子串
}

// moveByGS 按照好后缀规则计算滑动距离
// j 最右坏字符在模式串中最右出现位置下标， m 模式串长度， suffix, prefix分别为模式串预处理得到的后缀子串数组和前缀数组
// y 为计算的向右滑动距离
func moveByGS(j, m int, suffix[]int, prefix []bool) (y int) {
	k := m-1-j // 好后缀长度
	if suffix[k] != -1 {		// 直接查看好后缀本身是否能在模式串找到另一子串
		return j - suffix[k] + 1		// 直接找到，则直接滑到最右这个子串的位置（suffix记录的是每个子串最右出现的位置）
	}
	for r:=j+2; r<=m-1; r++ {	// 遍历好后缀，而且这里考虑的是好后缀本身不是模式前缀子串。所以直接从j+2开始
		if prefix[m-r] == true {	// 好后缀子串从长到短遍历。如果m-r这个长度的子串(好后缀的子串)能匹配到模式串前缀，则向右滑动这个距离
			return r
		}
	}

	// 如果这两个规则都没匹配到，就直接向后滑动模式串长度。
	return m
	// 从这也可以看出，可以直接使用好后缀规则实现BM。
	// 只是好后缀处理比坏字符复杂，只用好后缀的BM性能也会较两种规则结合使用的BM算法要差
}

// BMOnlyGoodSuffix 仅采用好后缀规则。但是当没有好后缀时，程序会陷入无限循环，因为一直原地滑动
func BMOnlyGoodSuffix(str, b string) (i int) {
	// 两个字符串长度
	n, m := len(str), len(b)

	// 预处理模式串， 生成suffix/prefix
	suffix, prefix := generateGS(b)

	//i = 0	// 模式串最左字符在str中对齐位置的下标
	for i <= n-m { // i=n-m时模式串就滑动到目标串末尾了
		var j int // 最右出现坏字符 在模式串中最右出现的的下标
		for j = m - 1; j >= 0; j-- { // 从右向左一一匹配，发现不同就退出循环
			if str[i+j] != b[j] {
				break
			}
		}
		// 循环结束之后记录有j的位置
		if j < 0 { // 完全匹配则返回首部对齐位置i
			return i
		}

		// 只按照好后缀规则得到的向右滑动距离
		y := 0
		if j < m-1 { // j是最右出现坏字符，j<m-1说明存在好后缀
			y = moveByGS(j, m, suffix, prefix)
		}
		i = i + y
	}

	return -1
}
```

## 4. KMP算法
