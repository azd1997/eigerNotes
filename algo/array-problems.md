---
title: "数组类问题集锦"
date: 2020-03-20T12:09:44+08:00
draft: false
categories: ["algo"]
tags: ["数组"]
keywords: ["数组类问题"]
---

## 0. 导语

题目来自LeetCode平台

## 1. 双指针（前后、快慢）

### 1.1 移除零

```go
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:

输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
说明:

必须在原数组上操作，不能拷贝额外的数组。
尽量减少操作次数。

///////////////////////////////////////////

func moveZeroes(nums []int)  {
    // 快慢指针，快指针遍历，慢指针指向去0序列最后一位的后一位
    
    if nums==nil || len(nums)==0 || len(nums)==1 {return}       // 边界
    var i, j int
    for i < len(nums) {
        if nums[i] != 0 {
            nums[j] = nums[i]
            j++
        }
        i++
    }       // j最后指向非零数字的后一位
    
    if j < len(nums) {
        for k:=j; k<len(nums); k++ {
            nums[k] = 0
        }
    }
}
```

### 1.2 移除元素

```go
给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

 

示例 1:

给定 nums = [3,2,2,3], val = 3,

函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,1,2,2,3,0,4,2], val = 2,

函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

注意这五个元素可为任意顺序。

你不需要考虑数组中超出新长度后面的元素。

/////////////////////////////////////////////////

// O(1)空间，原地修改，可以改变元素顺序
// 那么直接双指针，遇到慢指针j指向移除元素后的部分的末尾的后一个，快指针i是当前遍历的，当nums[i]不是待删元素，就和nums[j]交换
func removeElement(nums []int, val int) int {
    n := len(nums)
    if n == 0 {return 0}
    
    j:=0
    for i:=0; i<n; i++ {
        if nums[i] != val {
            //nums[i], nums[j] = nums[j], nums[i]
            nums[j] = nums[i]
            j++
        }
    }
    return j
}
```

### 1.3 删除排序数组的重复项

```go
给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

 

示例 1:

给定数组 nums = [1,1,2], 

函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,0,1,1,1,2,2,3,3,4],

函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。

你不需要考虑数组中超出新长度后面的元素。

///////////////////////////////////////////////////////////////////

func removeDuplicates(nums []int) int {
    // 快慢指针
    // 若重复，快慢交换。 （保持原数据顺序）
    // 把快指针值替换到慢指针后一位
    
    // 1. 边界条件
    l := len(nums)
    if l <= 1 {return l}
    
    // 2. 快慢指针移动
    i, j := 1, 0
    for ; i<l; i++ {
        if nums[i] != nums[j] {
            j++
            nums[j] = nums[i]
        }
    }
    
    // 3. 返回新数组长度
    return j+1
}
```
### 1.4 删除排序数组的重复项II

```go
给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素最多出现两次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

示例 1:

给定 nums = [1,1,1,2,2,3],

函数应返回新长度 length = 5, 并且原数组的前五个元素被修改为 1, 1, 2, 2, 3 。

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,0,1,1,1,1,2,3,3],

函数应返回新长度 length = 7, 并且原数组的前五个元素被修改为 0, 0, 1, 1, 2, 3, 3 。

你不需要考虑数组中超出新长度后面的元素。

///////////////////////////////////////////////////////////

// 快慢指针
func removeDuplicates(nums []int) int {
	n := len(nums)
	if n <= 2 {
		return n
	}

	i, j := 2, 2 // j指向“新数组”的后一位
	for ; i < n; i++ {
		// 如果旧数组当前元素 不同时 与新数组末两位 相同， 那么nums[j] = nums[i]; j++
		if nums[i] != nums[j-1] || nums[i] != nums[j-2] {
			nums[j] = nums[i]
			j++
		} else {
			// 否则舍弃掉该值，继续遍历旧数组的下一个元素
		}
	}
	return j
}
```

## 2. 基础算法思想

### 2.1 快排思想

#### 2.1.1 颜色分类

LeetCode 75

```go
给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

注意:
不能使用代码库中的排序函数来解决这道题。

示例:

输入: [2,0,2,1,1,0]
输出: [0,0,1,1,2,2]
进阶：

一个直观的解决方案是使用计数排序的两趟扫描算法。
首先，迭代计算出0、1 和 2 元素的个数，然后按照0、1、2的排序，重写当前数组。
你能想出一个仅使用常数空间的一趟扫描算法吗？

/////////////////////////////////////////////////////////////////////

// 题目提示计数排序，但我没学过计数排序
// 但是这道题其实很简单，因为只有三种颜色，只需要类似快排尤其是三路快排的那种做法。
// 这里：
// 凡是红色(0)交换到左边区间，凡是蓝色(2)交换到右边区间
// 一次遍历就可以排好序了。
//这只用到了三个指针（数组游标），符合题意

func sortColors(nums []int) {
	n := len(nums)
	if n == 0 {
		return
	}

	l, r, i := 0, n-1, 0 // l,r都指向区间右邻/左邻位置，而不是区间的右末位/左末位
	for i <= r {         // i==r时仍然要处理最后这个数
		switch nums[i] {
		case 0: // 红色
			nums[l], nums[i] = nums[i], nums[l]
			l++
			i++
		case 1: // 白
			i++
		case 2: // 蓝
			nums[r], nums[i] = nums[i], nums[r]
			r-- // 注意i不变，因为新交换过来的还没有处理过
		}
	}
}

// ps: 题解中有提及还可以使用基数排序来做。但是由于计数排序、基数排序、桶排序还没学，暂时不管它
// 而且这道题来讲三路快排的思想应该是最优解
```

#### 2.1.2 数组中第k个最大元素

LeetCode 215

```go
在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

示例 1:

输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
示例 2:

输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
说明:

你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

//////////////////////////////////////////////////

// 普通快排 + 缩减排序空间
func findKthLargest(nums []int, k int) int {
    n := len(nums)
    if n < k {return -1}

    return _quick(&nums, n, k, 0, n-1)
}

func _quick(nums *[]int, n, k, l, r int) int {
    if l == r {return (*nums)[l]}   // 区间只剩一个元素，只可能是这个了

    p := _partition(nums, k, l, r)

    // 必然会遇到第k大元素，遇到了就没必要继续递归下去了
    if p == (n-k) {return (*nums)[p]}

    // 否则向靠近n-k侧递归
    if p < n-k {
        return _quick(nums, n, k, p+1, r)
    } else {    // > n-k
        return _quick(nums, n, k, l, p-1)
    }
}


func _partition(nums *[]int, k, l, r int) int {
    // 随机选取基数
    randIdx := rand.Intn(r-l+1) + l
    // 交换到最左边
    (*nums)[l], (*nums)[randIdx] = (*nums)[randIdx], (*nums)[l]

    p := l    // p记录Less与Right交界，p为Less右端点，包含在内
    // 向右遍历
    for i:=l+1; i<=r; i++ {
        if (*nums)[i] < (*nums)[l] {
            (*nums)[i], (*nums)[p+1] = (*nums)[p+1], (*nums)[i]
            p++ // p后移
        }
    }
    // 交换基数与Less末位，交换后p为基数所在
    (*nums)[l], (*nums)[p] = (*nums)[p], (*nums)[l]
    return p
}
```

### 2.2 归并排序

#### 2.2.1 合并两个有序数组

LeetCode 88

其实这道题不能说是利用了归并排序思想，而是这道题的双指针解法就是归并排序中合并的操作。归并操作中是从前向后合并，而本题由于nums1空间足够，可以从后向前合并，不使用额外空间。

```go
给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 num1 成为一个有序数组。

说明:

初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。
你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
 

示例:

输入:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]

///////////////////////////////////////////////////////////

func merge(nums1 []int, m int, nums2 []int, n int)  {
    // 三指针法，从后向前
    
    // 1. 特殊情况是nums2为空
    if n == 0 {return}
    if m == 0 {copy(nums1[:n], nums2)}
    
    // 2. 一般情况
    p1, p2, p := m-1, n-1, m+n-1
    for p1>=0 && p2>=0 {
        if nums1[p1] >= nums2[p2] {
            nums1[p] = nums1[p1]
            p--
            p1--
        } else {
            nums1[p] = nums2[p2]
            p--
            p2--
        }
    }
    
    // p1没到头则不用动，p2没到头，则直接到nums1
    if p2 >= 0 {
        copy(nums1[:p2+1], nums2[:p2+1])
    }
}
```

## 3. 双指针（两端向内）

### 3.1 两数之和II-输入有序数组

LeetCode 167

```go
给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。

函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。

说明:

返回的下标值（index1 和 index2）不是从零开始的。
你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
示例:

输入: numbers = [2, 7, 11, 15], target = 9
输出: [1,2]
解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

///////////////////////////////////////////////////////////////////

// 1. 一遍线性 双指针
func twoSum(numbers []int, target int) []int {
	// 特殊
	n := len(numbers)
	if n < 2 {return []int{-1,-1}}

	// 双指针
	l, r := 0, n-1
	for l + 1 <= r {	// 至少有两个元素
		sum := numbers[l] + numbers[r]
		if sum == target {
			return []int{l+1, r+1}
		} else if sum < target {
			l++
		} else {	// >
			r--
		}
	}
	return []int{-1,-1}	// 没找到
}
```

### 3.2 验证回文串

LeetCode 125

```go
给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

示例 1:

输入: "A man, a plan, a canal: Panama"
输出: true
示例 2:

输入: "race a car"
输出: false

///////////////////////////////////////////////////////////

func isPalindrome(s string) bool {
    // 边界处理
    n := len(s)
    if n==0 || n==1 {return true}
    
    // 双指针
    l, r := 0, n-1
    for l<r {
        // 跳过杂字符
        if s[l]<'0' || (s[l]>'9' && s[l]<'A') || (s[l]>'Z' && s[l]<'a') || s[l]>'z' {l++; continue}
        if s[r]<'0' || (s[r]>'9' && s[r]<'A') || (s[r]>'Z' && s[r]<'a') || s[r]>'z' {r--; continue}
        // 数字的比较
        if s[l]>='0' && s[l]<='9' {
            if s[l] != s[r] {return false}
        }
        // 字母的比较
        if s[l]>='A' && s[l]<='Z' {
            if s[l] != s[r] && s[l] != s[r]-32 {return false}   // 'a'-'A'=32
        }
        if s[l]>='a' && s[l]<='z' {
            if s[l] != s[r] && s[l] != s[r]+32 {return false}   // 'a'-'A'=32
        }
        // 指针正常更新
        l++; r--
    }
    return true
}
```

### 3.3 验证回文字符串II

LeetCode 680

```go
给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。

示例 1:

输入: "aba"
输出: True
示例 2:

输入: "abca"
输出: True
解释: 你可以删除c字符。
注意:

字符串只包含从 a-z 的小写字母。字符串的最大长度是50000。

///////////////////////////////////////////////////////

// 思考：
// 粗暴的解法：直接先两端向内双指针，判断是否是回文，不是的话，则线性遍历s，以每一个字符作为删除字符，检查删后是否是回文
// 这显然不是较好的做法
// 其实仍然只需要一次遍历就可以：
// 两端向内，假如遇到一对不相等的，尝试删这而这之一，只要有其中一种删法能保证最后是回文，就可以

func validPalindrome(s string) bool {
	n := len(s)
	if n <= 1 {
		return true
	}
	// 两端向内双指针
	l, r := 0, n-1
	for l <= r {
		if s[l] != s[r] { // 第一次不相等，删除其中之一试试
			return help(s, l+1, r) || help(s, l, r-1)
		}
		// 否则指针后移
		l++
		r--
	}
	return true
}

// 为了能处理两种选择，使用辅助函数
func help(s string, l, r int) bool {
	for l <= r {
		if s[l] != s[r] {
			return false
		}
		l++
		r--
	}
	return true
}

// 可以将两段双指针写到一个迭代内而不需要辅助函数，但可读性变差
```

### 3.4 反转字符串中的元音字母

LeetCode345

```go
编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

示例 1:

输入: "hello"
输出: "holle"
示例 2:

输入: "leetcode"
输出: "leotcede"
说明:
元音字母不包含字母"y"。

/////////////////////////////////////////////////

// 这题说的不清楚，
// 它的意思是要将所有元音字母所组成的序列进行反序。
// 另一点很坑的是，没说大写也算

// 元音字母
// A O E U I
// a o e u i

// 解题：
// 其实就是很简单的双指针
// 左右指针都移动到元音字母上时才交换

func reverseVowels(s string) string {
	n := len(s)
	if n == 0 {
		return s
	}

	// 双指针 两端向内
	slice := []byte(s) // 字符串不可修改
	l, r := 0, n-1
	for l <= r { // 所有字母都要检查
		// 定位到元音字母
		if !isVowel(slice[l]) {
			l++
			continue
		}
		if !isVowel(slice[r]) {
			r--
			continue
		}
		// 交换两个元音
		slice[l], slice[r] = slice[r], slice[l]
		l++
		r--
	}
	return string(slice)
}

func isVowel(char byte) bool {
	return char == 'A' || char == 'a' ||
		char == 'E' || char == 'e' ||
		char == 'I' || char == 'i' ||
		char == 'O' || char == 'o' ||
		char == 'U' || char == 'u'
}

```

### 3.5 盛最多水的容器

LeetCode 11

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

![](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

    示例：

    输入：[1,8,6,2,5,4,8,3,7]
    输出：49

```go
/////////////////////////////////////////////////////////

func maxArea(height []int) int {
    n := len(height) 
    if n < 2 {return 0}
    
    // 双指针，两端向内， 带贪心思想。长度从最长开始变短，因此两根柱子的最小高度必须变高才有可能找到更大的面积
    l, r, maxarea := 0, n-1, 0
    for l < r {     // 最起码要有两根柱子
        // 更新最大面积
        maxarea = max(maxarea, (r-l) * min(height[l], height[r]))
        // 短的那一根柱子向内移
        if height[l] < height[r] {
            l++
        } else {
            r--
        }
    }
    return maxarea
}

func max(a, b int) int {
    if a >= b {
        return a
    }
    return b
}

func min(a, b int) int {
    if a <= b {
        return a
    }
    return b
}
```

## 4. 双指针（滑动窗口）

### 4.1 长度最小的子数组

LeetCode 209

```go
给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的连续子数组。如果不存在符合条件的连续子数组，返回 0。

示例: 

输入: s = 7, nums = [2,3,1,2,4,3]
输出: 2
解释: 子数组 [4,3] 是该条件下的长度最小的连续子数组。
进阶:

如果你已经完成了O(n) 时间复杂度的解法, 请尝试 O(n log n) 时间复杂度的解法。

/////////////////////////////////////////////////////////////////

// 双指针 滑动窗口
func minSubArrayLen(s int, nums []int) int {
	n := len(nums)
	if n == 0 {
		return 0
	} // 不存在符合条件的子数组

	// 滑动窗口
	l, r := 0, 0 // 注意，每次r定位到sum([l:r]) > s时，要尝试将l右移，这样才能不漏掉解
	sum := 0     // 子数组的和
	minL := 1 << 31
	for l <= r && r < n {
		if sum == s { // ==s时r右移
			minL = min(minL, r-l+1)
			if r < n-1 {
				r++
				sum += nums[r]
			}
			continue
		}
		if sum > s { // >s时l右移
			minL = min(minL, r-l+1)
			sum -= nums[l]
			l++
			continue
		}
		// 否则，就 sum < s， 直接r右移
		if r < n-1 {
			r++
			sum += nums[r]
		} else { // r==n-1 && sum<s ， 可以提前退出
			break
		}
	}
	return minL
}

func min(a, b int) int {
	if a <= b {
		return a
	}
	return b
}

// 上面的解法思路是对的，但实现上出错了。当r==n-1时而恰好sum([l:r])==s，这时l无法移动，会陷入无限循环
// 例如示例 s=7, nums=[2,3,1,2,4,3]。 问题出在 if sum==s这段里。

// 把 sum==s的情况归类到sum>=s中可以避免这种情况，因为会l移动，从而使得遍历能继续下去

// 此外，如果找不到和大于等于s的子区间最后就会minL = 1<<31， 要检查这种情况

// 正确实现如下：
// 双指针 滑动窗口
func minSubArrayLen2(s int, nums []int) int {
	n := len(nums)
	if n == 0 {
		return 0
	} // 不存在符合条件的子数组

	// 滑动窗口
	l, r := 0, 0   // 注意，每次r定位到sum([l:r]) > s时，要尝试将l右移，这样才能不漏掉解
	sum := nums[0] // 子数组的和
	minL := 1 << 31
	for l <= r {
		//fmt.Println(minL, sum, l, r)
		if sum >= s { // >=s时l右移
			minL = min(minL, r-l+1)
			sum -= nums[l]
			l++
			continue
		}
		// 否则，就 sum < s， 直接r右移
		if r < n-1 {
			r++
			sum += nums[r]
		} else { // r == n-1 时，同时又sum<s，则可以结束了
			break
		}
	}
	if minL != 1<<31 {
		return minL
	}
	return 0 // 不存在
}

```