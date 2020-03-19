---
title: "二分查找问题集锦"
date: 2020-03-14T12:34:24+08:00
draft: false
categories: ["algo"]
tags: ["二分查找"]
keywords: ["二分查找"]
---

## 0. 导语

强烈推荐labuladong的二分查找模板！！！

## 1. 模板一

模板 #1 用于查找可以通过访问数组中的**单个索引**来确定的元素或条件。

**关键属性**

- 二分查找的最基础和最基本的形式。
- 查找条件可以在不与元素的两侧进行比较的情况下确定（或使用它周围的特定元素）。
- 不需要后处理，因为每一步中，你都在检查是否找到了元素。如果到达末尾，则知道未找到该元素。
 
**区分语法**

- 初始条件：left = 0, right = length-1
- 终止：left > right
- 向左查找：right = mid-1
- 向右查找：left = mid+1

```go
// BinarySearchTp1 二分查找 迭代版本 模板1
// 返回目标索引
func BinarySearchTp1(nums []int, target int) int {
	// 特殊情况
	n := len(nums)
	if len(nums) == 0 {
		return -1
	}

	// 二分
	l, r := 0, n-1
	for l <= r { // 最后只剩一个元素时仍然进入
		mid := (r-l)/2 + l

		if target == nums[mid] {
			return mid
		} else if target > nums[mid] {
			l = mid + 1
		} else { // target < nums[mid]
			r = mid - 1
		}
	}

	return -1 // 没找到。 结束条件 l>r
}

```

### 1.1 x的平方根

```go
// 实现 int sqrt(int x) 函数。
// 计算并返回 x 的平方根，其中 x 是非负整数。
// 由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

// 二分查找，从1~x去二分，找到i满足 i*i<=x && (i+1)*(i+1) > x
func mySqrt(x int) int {
    // 特殊情况
    if x == 0 {return 0}
    if x < 4 {return 1}
    
    // 二分
    l, r := 1, x-1      // 这里左右边界稍微变一下也都是可以的，比如0,x 或者 1,x
    for l <= r {
        mid := (r-l)/2 +l
        if mid*mid <= x && (mid+1)*(mid+1) > x {
            return mid
        } else if mid*mid < x {
            l = mid + 1
        } else if mid*mid > x {
            r = mid - 1
        }
    }
    
    return -1   // 事实上不可能出现-1
}
```

### 1.2 猜数字大小

```go
// 我从 1 到 n 选择一个数字。 你需要猜我选择了哪个数字。
// 每次你猜错了，我会告诉你这个数字是大了还是小了。
// 你调用一个预先定义好的接口 guess(int num)，它会返回 3 个可能的结果（-1，1 或 0）：
// -1 : 我的数字比较小
//  1 : 我的数字比较大
//  0 : 恭喜！你猜对了！
// 示例 :
// 输入: n = 10, pick = 6
// 输出: 6

/** 
 * Forward declaration of guess API.
 * @param  num   your guess
 * @return 	     -1 if num is lower than the guess number
 *			      1 if num is higher than the guess number
 *               otherwise return 0
 * func guess(num int) int;
 */

func guessNumber(n int) int {
    // 特殊情况
    if n < 1 {return -1}    // 由于n一定是正整数，用-1表示异常
    
    // 二分
    l, r := 1, n
    for l <= r {
        mid := (r - l) / 2 + l
        switch guess(mid) {
        case 0:
            return mid
        case 1:
            //r = mid - 1
             l = mid + 1
        case -1:
            //l = mid + 1
             r = mid - 1
        default:
            return -1   // 异常
        }
        fmt.Println(mid, guess(mid), l, r)
    }
    
    return -1 //找不到，异常
}

// 注： 这道题给的API描述似乎反了
```

### 1.3 搜索旋转排序数组

```go
// 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
// ( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
// 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。
// 你可以假设数组中不存在重复的元素。
// 你的算法时间复杂度必须是 O(log n) 级别。

// 示例 1:
// 输入: nums = [4,5,6,7,0,1,2], target = 0
// 输出: 4
// 示例 2:
// 输入: nums = [4,5,6,7,0,1,2], target = 3
// 输出: -1


// 思路：
// 旋转点i特性 满足 nums[i-1] > nums[i] > nums[i+1] ，如果没找到，说明没旋转
//
// 1. 线性遍历。O(n)
// 2. 二分查找。O(logn)

func search(nums []int, target int) int {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return -1
	}

	// 二分. 注意若nums[l] < nums[r]说明这段区间没旋转； 否则旋转了（含有旋转点）
	l, r, mid := 0, n-1, 0
	for l <= r {
		mid = (r-l)/2 + l
		// 关键在于：每次都要检查[l:mid]和[mid:r]那个区间含有旋转点
		// 不管是不是没旋转过的特殊情况，当判断其中一半没有旋转时，默认另一半旋转过了（不影响求解）
		// 而且比较的时候不能只跟mid比，还要和l,r比

		if nums[mid] == target {
			return mid
		}
		// 如果[l:mid]有序
		if nums[l] <= nums[mid] {
			if nums[l] <= target && target <= nums[mid] { // 说明target在[l:mid]中
				return bs(nums, target, l, mid-1) // mid检查过不必再检查
			} else { // 说明target不在这里，则更新l，准备去右区间找
				l = mid + 1
			}
		} else { // 右边区间有序
			if nums[mid] <= target && target <= nums[r] { // 说明target在[mid:r]中
				return bs(nums, target, mid+1, r) // mid检查过不必再检查
			} else { // 说明target不在这里，则更新l，准备去右区间找
				r = mid - 1
			}
		}
	}

	return -1
}

// 对于有序区间使用普通二分
func bs(nums []int, target, l, r int) int {
	for l <= r {
		mid := (r-l)/2 + l
		if nums[mid] == target {
			return mid
		} else if nums[mid] > target {
			r = mid - 1
		} else {
			l = mid + 1
		}
	}
	return -1 // 没找到
}

// 其实没必要单独设一个普通二分，当然设置有序区间普通二分，减少了一些比较操作，对性能是有利的，但代码较长。
// 下面是另一种写法，不单独写一个普通二分查找：

func search2(nums []int, target int) int {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return -1
	}

	// 二分
	l, r, mid := 0, n-1, 0
	for l <= r {
		mid = (r-l)/2 + l
		if nums[mid] == target {
			return mid
		}

		// 左区间有序
		if nums[l] <= nums[mid] {
			// target位于这段有序区间
			if nums[l] <= target && target <= nums[mid] {
				r = mid - 1
			} else {
				l = mid + 1
			}
		} else {
			// 右区间有序
			// target位于这段有序区间
			if nums[mid] <= target && target <= nums[r] {
				l = mid + 1
			} else {
				r = mid - 1
			}
		}
	}
	return -1
}

```

- [参考题解](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/yi-wen-jie-jue-4-dao-sou-suo-xuan-zhuan-pai-xu-s-2/)

### 1.4 搜索旋转排序数组II

```go
// 注意：这题nums可能含有重复元素，返回target是否存在于nums

// 对于旋转数组查找类题目，有一个bug级别的杀器：哈希表，万物皆可哈希...不过需要时间O(n)/空间O(n)

// 参考lt31，使用二分查找的重点在于判断哪一半区间有序

// 但是含有重复元素，意味着即使发生旋转，也可能在两个区间（一个有序，一个含有旋转点）中都含有target
// 例如 nums=[1,3,1,1,1] target=3
// 完全照着lt31写的话， 第一次nums[mid]=1，满足nums[l] <= num[mid]，会使得二分查找会错过target。
// 这意味着不能再用nums[l] <= nums[r]来判断区间[l:r]有序。
// 需要改成：
// nums[l] < nums[r] 判断区间有序
// 如果 nums[l] == nums[r] 只能线性查找，避免漏掉target。

// 错解：完全按照lt31写的二分
func search(nums []int, target int) bool {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return false
	}
	if n == 1 {
		return nums[0] == target
	}

	// 二分
	l, r, mid := 0, n-1, 0
	for l <= r {
		mid = (r-l)/2 + l
		if nums[mid] == target {
			return true
		}

		// 左区间有序
		if nums[l] <= nums[mid] {
			if nums[l] <= target && target <= nums[mid] {
				r = mid - 1
			} else {
				l = mid + 1
			}
		} else { // 右区间有序
			if nums[mid] <= target && target <= nums[r] {
				l = mid + 1
			} else {
				r = mid - 1
			}
		}
	}

	return false
}

// 正确的二分解法：nums[l] == nums[r]时退化成线性查找
func search2(nums []int, target int) bool {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return false
	}
	if n == 1 {
		return nums[0] == target
	}

	// 二分
	l, r, mid := 0, n-1, 0
	for l <= r {
		mid = (r-l)/2 + l
		if nums[mid] == target {
			return true
		}

		// 这里为什么只需要判断nums[l]==nums[mid]？
		// 因为含有重复元素使得nums[l] <= nums[r]无法成为判定[l:mid]有序的判据，
		// 加上这句，就能判断左区间到底有序没序、含不含target。自然右区间相应就知道了。
		if nums[l] == nums[mid] { // 退化成线性查找
			for i := l; i <= r; i++ {
				if nums[i] == target {
					return true
				}
			}
			return false
		} else if nums[l] <= nums[mid] { // 左区间有序
			if nums[l] <= target && target <= nums[mid] {
				r = mid - 1
			} else {
				l = mid + 1
			}
		} else { // 右区间有序
			if nums[mid] <= target && target <= nums[r] {
				l = mid + 1
			} else {
				r = mid - 1
			}
		}
	}

	return false
}

// 上面是一旦出现nums[l]==nums[mid]就线性遍历[l:r]
// 还有一种写法，可能会更好一些（退化成线性查找的概率更低）：
// 当nums[l]==nums[mid]时，l++，相当于去除了nums[l]这个干扰元素
// 代码如下：

func search3(nums []int, target int) bool {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return false
	}
	if n == 1 {
		return nums[0] == target
	}

	// 二分
	l, r, mid := 0, n-1, 0
	for l <= r {
		mid = (r-l)/2 + l
		if nums[mid] == target {
			return true
		}

		// 当nums[l]==nums[mid]时，l++，相当于去除了nums[l]这个干扰元素
		if nums[l] == nums[mid] { // 退化成线性查找
			l++ // 因为nums[l]==nums[mid]，而nums[mid]!= target，因此直接排除
		} else if nums[l] <= nums[mid] { // 左区间有序
			if nums[l] <= target && target <= nums[mid] {
				r = mid - 1
			} else {
				l = mid + 1
			}
		} else { // 右区间有序
			if nums[mid] <= target && target <= nums[r] {
				l = mid + 1
			} else {
				r = mid - 1
			}
		}
	}

	return false
}

```

### 1.5 寻找旋转排序数组中的最小值

```go
// 数组中不含重复元素

// 这道题其实比前两道简单多了
// 二分查找的写法也挺多变化
// 下面给出一种

// 如果旋转过，则旋转点最小；没旋转过，则nums[l]最小

func findMin(nums []int) int {
    n := len(nums)
    if n == 0 {return -1}   // 没有最小值
    if n == 1 {return nums[0]}

    // 二分
    l, r, mid := 0, n-1, 0
    for l<=r {
        // 如果这段区间有序，那么返回nums[l]    // 其实这只会在l,r = 0,n-1时进入，检查是否存在旋转点
        if nums[l] <= nums[r] {     // 取=号时是区间长度为1的时候
            return nums[l]
        }

        // 否则取mid，并检查左右子区间的情况
        mid = (r-l)/2+l
        if nums[l] <= nums[mid] {   // 左区间有序，说明旋转点在右区间，去右区间找
            l = mid + 1 
        } else {
            r = mid // 注意：这里不能排除mid是旋转点的可能
        }
    }
    return -1   // 找不到最小值，这是不可能的
}

// 另一种二分查找，基于labuladong的二分查找框架
// 核心在于利用左右子区间的有序与否，不断逼近旋转点，旋转点也就是最小点
// 不过好像和前面那种是一样的。
// 原因在于 处理区间有序时 处理了 区间长度为1 的情况

// 对于labuladong二分模板找target本身。
// 比较nums[l]和nums[mid]，若左区间有序，则去右；否则去左。最后只剩一个元素，就是旋转点，也就是最小值
func findMin5(nums []int) int {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return -1
	} // 不存在，异常
	if n == 1 {
		return nums[0]
	}

	// 二分 模板2
	l, r, mid := 0, n-1, 0 // 这里同样 r 得是 n-1 ， 避免索引越界
	for l <= r {
		// 注意，先得检查区间是否有序
		if nums[l] <= nums[r] { // 当区间长度为1时取=号
			return nums[l]
		}

		mid = (r-l)/2 + l
		if l == r { // 最后逼近到长度为1，必然是旋转点
			return nums[l]
		}
		if nums[l] <= nums[mid] { // 左区间有序
			l = mid + 1 // mid必然不是旋转点   [mid+1:r]
		} else { // 左区间无序，含有旋转点
			r = mid // [l:mid]
		}
	}

	// 不可能走到这里
	return -1
}

```

### 1.6 寻找旋转排序数组中的最小值II

```go
// 和lt153的区别在于：nums可能存在重复元素
// 同样的，存在重复元素的话，不能用nums[l] <= nums[r] 判断区间[l:r]有序
// 需要在nums[l] == nums[r]时，只能退化成线性遍历了
// 注意，由于是寻找最小值，不能使用l++排除的策略

func findMin(nums []int) int {
    n := len(nums)
    if n == 0 {return -1}   // 没有最小值
    if n == 1 {return nums[0]}

    // 二分
    l, r, mid := 0, n-1, 0
    for l<=r {

        if nums[l] == nums[r] {     // 退化成线性遍历
            min := math.MaxInt32
            for i:=l; i<=r; i++ {
                if nums[i] < min {min = nums[i]}
            }
            return min
        }
        if nums[l] < nums[r] {      // 有序区间直接取最左    
            return nums[l]
        }

        // 否则取mid，并检查左右子区间的情况
        mid = (r-l)/2+l
        if nums[l] <= nums[mid] {   // 左区间有序，说明旋转点在右区间，去右区间找
            l = mid + 1 
        } else {
            r = mid // 注意：这里不能排除mid是旋转点的可能
        }
    }
    return -1   // 找不到最小值，这是不可能的
}
```

## 2. 模板二

模板 #2 是二分查找的高级模板。它用于查找需要访问数组中**当前索引及其直接右邻居索引**的元素或条件。

**关键属性**

- 一种实现二分查找的高级方法。
- 查找条件需要访问元素的直接右邻居。
- 使用元素的右邻居来确定是否满足条件，并决定是向左还是向右。
- 保证查找空间在每一步中至少有 2 个元素。
- 需要进行后处理。 当你剩下 1 个元素时，循环 / 递归结束。 需要评估剩余元素是否符合条件。
 
**区分语法**

- 初始条件：left = 0, right = length
- 终止：left == right
- 向左查找：right = mid
- 向右查找：left = mid+1

**注意：尽管在这个寻找target的例子里 r 可取 n， 但是很多时候如果需要使用nums[mid+1], r 一定要取为 n-1，否则很可能索引越界**

**而且： r 必须取 n 的场景是什么？？**（TODO）

```go
// BinarySearchTp2 二分查找 迭代版本 模板2
// 返回目标索引
func BinarySearchTp2(nums []int, target int) int {
	// 特殊情况
	n := len(nums)
	if len(nums) == 0 {
		return -1
	}

	// 二分
	l, r := 0, n // 注意 r 从 n 开始
	for l < r {  // 区间至少含有2个元素才进入。这意味着最后迭代结束后需要处理最后剩下的那个元素
		mid := (r-l)/2 + l

		if target == nums[mid] {
			return mid
		} else if target > nums[mid] {
			l = mid + 1
		} else { // target < nums[mid]
			r = mid
		}
	}

	// 后处理：处理最后剩余的这个元素nums[l]是不是要找的。注意最后l=r，而r是有可能在迭代结束时还是n的
	if l != n && nums[l] == target {
		return l
	}

	return -1 // 没找到。 结束条件 l>r
}
```

### 2.1 第一个错误的版本

```go

// 你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

// 假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

// 你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

// 示例:

// 给定 n = 5，并且 version = 4 是第一个错误的版本。

// 调用 isBadVersion(3) -> false
// 调用 isBadVersion(5) -> true
// 调用 isBadVersion(4) -> true

// 所以，4 是第一个错误的版本。 

/** 
 * Forward declaration of isBadVersion API.
 * @param   version   your guess about first bad version
 * @return 	 	      true if current version is bad 
 *			          false if current version is good
 * func isBadVersion(version int) bool;
 */

// 对于mid是正确版本而mid+1是错误版本，则返回mid+1
func firstBadVersion(n int) int {
    // 特殊情况
    if n < 1 {return 0}     // 异常
    
    // 二分 模板2
    l, r, mid := 0, n+1, 0  // 注意r初始要设为实际右边界+1
    ver1, ver2 := false, false
    for l < r { // 区间长度至少为2
        mid = (r-l)/2 + l
        ver1, ver2 = isBadVersion(mid), isBadVersion(mid+1)
        if !ver1 && ver2 {return mid+1}
        if !ver2 {
            l = mid+1
        } else {    // ver2 bad 且 ver1 bad
            r = mid
        } 
    }
    
    // 最后剩下的元素必然是第一个错误版本
    return l
}

///////////////////////////////////////////////

// 上面这么写当然是没错的
// 但是利用模板2，最后剩余的那个元素必然就是第一个错误的版本
// 也就是说 if !ver1 && ver2 {} 的判断是不必要的。
// 代码重新修正如下：

func firstBadVersion(n int) int {
    // 特殊情况
    if n < 1 {return 0}     // 异常
    
    // 二分 模板2
    l, r, mid := 0, n+1, 0  // 注意r初始要设为实际右边界+1
    for l < r { // 区间长度至少为2
        mid = (r-l)/2 + l
        if !isBadVersion(mid) {     // mid是正确版本，则mid肯定不是
            l = mid+1
        } else {    // mid嫌疑没有排除
            r = mid
        } 
    }
    
    // 最后剩下的元素必然是第一个错误版本
    return l
}
```

### 2.2 寻找峰值

这道题很容易出错...

```go
// 峰值元素是指其值大于左右相邻值的元素。

// 给定一个输入数组 nums，其中 nums[i] ≠ nums[i+1]，找到峰值元素并返回其索引。

// 数组可能包含多个峰值，在这种情况下，返回任何一个峰值所在位置即可。

// 你可以假设 nums[-1] = nums[n] = -∞。

// 示例 1:

// 输入: nums = [1,2,3,1]
// 输出: 2
// 解释: 3 是峰值元素，你的函数应该返回其索引 2。
// 示例 2:

// 输入: nums = [1,2,1,3,5,6,4]
// 输出: 1 或 5 
// 解释: 你的函数可以返回索引 1，其峰值元素为 2；
//      或者返回索引 5， 其峰值元素为 6。
// 说明:

// 你的解法应该是 O(logN) 时间复杂度的。

// 由于下标i=-1或者n视为无穷小，那么假如nums[mid] > nums[mid+1]，则在[0:mid]必然存在峰值。
// 并继续对[0:mid]进行二分
func findPeakElement(nums []int) int {
    // 特殊情况
    n := len(nums)
    if n == 0 {return -1}   // 不存在
    if n == 1 {return 0}
    
    // 二分
    l, r, mid := 0, n-1, 0    // r从n-1开始，这是为了避免nums[mid+1]越界
    for l < r { // 区间长度至少为2
        mid = (r-l)/2 + l
        if nums[mid] > nums[mid+1] {
            r = mid
        } else { // nums[mid] <= nums[mid+1]。 就算是=号，这样也能处理到
            l = mid + 1
        }
    }
    // 最后只剩一个元素，必然是峰值
    return l
}
```

### 2.3 寻找旋转排序数组中的最小值

前面是用了模板1解决这道题，现在使用模板2再实现一遍。

```go
// 对于二分查找迭代模板2。 
// 比较nums[l]和nums[mid]，若左区间有序，则去右；否则去左。最后只剩一个元素，就是旋转点，也就是最小值
func findMin(nums []int) int {
    // 特殊情况
    n := len(nums)
    if n == 0 {return -1}   // 不存在，异常
    if n == 1 {return nums[0]}
    
    // 二分 模板2
    l, r, mid := 0, n-1, 0    // 这里同样 r 得是 n-1 ， 避免索引越界
    for l < r {
        // 注意，先得检查区间是否有序
        if nums[l] <= nums[r] {     // 当区间长度为1时取=号
            return nums[l]
        }
        
        mid = (r-l)/2+l
        if nums[l] <= nums[mid] {   // 左区间有序
            l = mid + 1     // mid必然不是旋转点
        } else {    // 左区间无序，含有旋转点
            r = mid // mid可能是旋转点
        }
    }
    
    // 最后剩余的元素就是最小值(旋转点)
    return nums[l]
}
```

## 3. 模板三

模板 #3 是二分查找的另一种独特形式。 它用于搜索需要访问**当前索引及其在数组中的直接左右邻居索引**的元素或条件。

**关键属性**

实现二分查找的另一种方法。
搜索条件需要访问元素的直接左右邻居。
使用元素的邻居来确定它是向右还是向左。
保证查找空间在每个步骤中至少有 3 个元素。
需要进行后处理。 当剩下 2 个元素时，循环 / 递归结束。 需要评估其余元素是否符合条件。
 

**区分语法**

初始条件：left = 0, right = length-1
终止：left + 1 == right
向左查找：right = mid
向右查找：left = mid

```go
// 二分查找 迭代版本 模板3

// BinarySearchTp3 二分查找 迭代版本 模板3
// 返回目标索引
func BinarySearchTp3(nums []int, target int) int {
	// 特殊情况
	n := len(nums)
	if len(nums) == 0 {
		return -1
	}

	// 二分
	l, r := 0, n-1
	for l+1 < r { // 区间至少含有3个元素才进入。这意味着最后迭代结束后需要处理最后剩下的两个元素
		mid := (r-l)/2 + l

		if target == nums[mid] {
			return mid
		} else if target > nums[mid] {
			l = mid // 尽管mid已经考察过，但是由于 l+1<r 的终止条件，必须将其包含进去
		} else { // target < nums[mid]
			r = mid
		}
	}

	// 后处理 迭代结束时 l+1=r
	if nums[l] == target {
		return l
	}
	if nums[r] == target {
		return r
	}

	return -1 // 没找到。 结束条件 l>r
}
```

### 3.1 在排序数组中查找元素的第一个和最后一个位置

```go
// 给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

// 你的算法时间复杂度必须是 O(log n) 级别。

// 如果数组中不存在目标值，返回 [-1, -1]。

// 示例 1:

// 输入: nums = [5,7,7,8,8,10], target = 8
// 输出: [3,4]
// 示例 2:

// 输入: nums = [5,7,7,8,8,10], target = 6
// 输出: [-1,-1]

///////////////////////////////////////////////

// 就是找左边界和右边界
// 那么其实可以先找出左边界 l
// 再在[l:n]找右边界r

func searchRange(nums []int, target int) []int {
	// 特殊情况
	n := len(nums)
	if n == 0 {
		return []int{-1, -1}
	}

	// 二分查找，找左边界
	leftTargetIdx := bs(nums, target, true)
	// 检查left的有效性
	if leftTargetIdx == n || nums[leftTargetIdx] != target {
		return []int{-1, -1}
	}

	// 接着寻找右边界
	rightTargetIdx := bs(nums, target, false) - 1 // 为什么减一？思考下寻找右边界时bs最终会找到哪个位置：最后一个target的右邻

	return []int{leftTargetIdx, rightTargetIdx}
}

// 这里其实使用的是模板2
// seekLeft标记是寻找target左边界还是右边界
func bs(nums []int, target int, seekLeft bool) int {
	l, r, mid := 0, len(nums), 0 // 注意这里的r
	for l < r {
		mid = (r-l)/2 + l
		if nums[mid] > target || (seekLeft && nums[mid] == target) { // 注意，如果是寻找左边界，即便等于target也要向左继续寻找
			r = mid // mid还没排除嫌疑
		} else {
			l = mid + 1
		}
	}
	// 后处理，剩下最后一个就是左边界。 对于寻找target右边界，l就是右边界
	// 但是要注意的是这里返回的l还有可能是没找到target情况下返回的l，需作检查
	return l
}

```

### 3.2 找到K个最接近的元素

使用第4节labuladong二分查找框架解。

```go
// 给定一个排序好的数组，两个整数 k 和 x，从数组中找到最靠近 x（两数之差最小）的 k 个数。返回的结果必须要是按升序排好的。如果有两个数与 x 的差值一样，优先选择数值较小的那个数。

// 示例 1:

// 输入: [1,2,3,4,5], k=4, x=3
// 输出: [1,2,3,4]
//  

// 示例 2:

// 输入: [1,2,3,4,5], k=4, x=-1
// 输出: [1,2,3,4]
//  

// 说明:

// k 的值为正数，且总是小于给定排序数组的长度。
// 数组不为空，且长度不超过 104
// 数组里的每个元素与 x 的绝对值不超过 104

//////////////////////////////////////////////////////////

// 思路：
// 先二分查找找到target的左边界idx（注意不要直接按模板，如果nums[l]!=target返回l）
// 再从idx向两边寻找比较。这时要使用双指针，来比较哪边的差值更小。差值一样的话左边排前面

func findClosestElements(arr []int, k int, x int) []int {
	// 特殊情况
	n := len(arr)
	if n == 0 || n < k {
		return nil
	}

	// 二分，找到target左边界或左邻
	idx := bsl(arr, x)
	fmt.Println("idx=", idx)
	if idx == n {
		return arr[n-k:]
	}
	if idx == 0 {
		return arr[:k]
	}
	// 现在以idx为界，分为两半，右侧以idx为起点，左侧以idx-1为起点， 向两边双指针
	res := make([]int, k)
	k1 := 0
	l, r := idx-1, idx
	for k1 < k {
		// 两端都有剩余元素
		if l >= 0 && r < n {
			if x-arr[l] <= arr[r]-x { // 注意=时取左边的（更小）
				res[k1] = arr[l]
				l--
			} else {
				res[k1] = arr[r]
				r++
			}
			k1++
			continue
		}
		// 一端没有
		if l >= 0 {
			res[k1] = arr[l]
			l--
			k1++
		}
		if r < n {
			res[k1] = arr[r]
			r++
			k1++
		}
	}

	// 要求res升序排列
	sort.Ints(res)

	return res
}

func bsl(arr []int, target int) int {
	l, r, mid := 0, len(arr), 0
	for l <= r {
		mid = (r-l)/2 + l
		if arr[mid] >= target {
			r = mid - 1 // 向左搜索
		} else {
			l = mid + 1 // 向右搜索
		}
	}
	// l 可能有哪些可能？
	// target比arr都小，则不断向左缩，最后arr[0]还是比target大，则r=-1，但是l=0. 也就是说target比arr都小，l=0
	// target比arr都大，不断向右，最后arr[n-1]还是比target小，则l=n
	// target存在于arr，则l=0~n-1
	// target在arr区间范围内但不等于任何一个数，最后一次区间可能是target左邻也可能是右邻。
	//    如果是左邻，那么l会增大到target右邻位置; 如果是右邻，则l就落在右邻位置

	// 在本题，其实都可以直接返回
	return l
}

/////////////////////////////////////////////////////

// 解法二： 根据差值直接排序
func findClosestElements2(arr []int, k int, x int) []int {
	// 特殊情况
	n := len(arr)
	if n == 0 || n < k {
		return nil
	}

	// 根据差值排序
	sort.Slice(arr, func(i, j int) bool {
		a, b := int(math.Abs(float64(arr[i]-x))), int(math.Abs(float64(arr[j]-x)))
		if a == b {
			return arr[i] < arr[j]
		}
		return a < b
	})

	// res升序
	res := arr[:k]
	sort.Ints(res)

	return res
}

// https://leetcode-cn.com/problems/find-k-closest-elements/solution/pai-chu-fa-shuang-zhi-zhen-er-fen-fa-python-dai-ma/
// 这篇题解提供了两种更好的解法
```

## 4. labuladong的二分查找模板

吐槽： 上面的模板3坑我，LeetCode探索中给出的练习题明显也不会最后留两个数出来，浪费时间不是...

看了labuladong的二分查找，我又觉得可以了...

核心是： **明确搜索区间及边界的更新**

直接给上结论框架（推导步骤见参考）：

为了好记，我只使用**两端闭区间**进行二分查找。例如对于数组`nums`二分查找，则有`l, r = 0, len(nums)-1`

### 4.1 寻找target的下标

这个其实好理解，也就是前面的模板1

```go
func bs1(nums []int, target int) int {
	l, r := 0, len(nums)-1 // 两端闭
	for l <= r {           // 终止条件 l > r
		mid := (r-l)/2 + l
		if nums[mid] == target {
			return mid
		} else if nums[mid] > target {
			r = mid - 1 // mid已排除	// 搜索区间 [l, mid-1]
		} else { // nums[mid] < target
			l = mid + 1 // mid已排除    // 搜索区间 [mid+1, r]
		}
	}
	return -1 // 没找到
}
```

### 4.2 寻找target的左边界

```go
func bs2(nums []int, target int) int {
	l, r := 0, len(nums)-1 // 两端闭
	for l <= r {           // 终止条件 l > r
		mid := (r-l)/2 + l
		if nums[mid] == target {
			r = mid - 1 // 注意：搜索左边界时找到target并不直接返回，而是向左侧区间搜索		[l, mid-1]
		} else if nums[mid] > target {
			r = mid - 1 // mid已排除	[l, mid-1]
		} else { // nums[mid] < target
			l = mid + 1 // mid已排除    [mid+1, r]
		}
	}

	// NOTICE: 如果target不存在，则返回的会是target的右邻
	// l 可能有哪些可能？
	// target比arr都小，则不断向左缩，最后arr[0]还是比target大，则r=-1，但是l=0. 也就是说target比arr都小，l=0
	// target比arr都大，不断向右，最后arr[n-1]还是比target小，则l=n
	// target存在于arr，则l=0~n-1
	// target在arr区间范围内但不等于任何一个数，最后一次区间可能是target左邻也可能是右邻。
	//    如果是左邻，那么l会增大到target右邻位置; 如果是右邻，则l就落在右邻位置


	// 由于for循环退出条件为 l = r + 1 ，因此当target比nums元素都大时，会出现 l=len(nums)的情况
	// l 停住时还要检查是否走到了target左边界，如果target不存在，那么就会nums[l]!=target
	if l >= len(nums) || nums[l] != target {
		return -1 // 没找到
	}

	return l // target左边界
}

// 简化一些

func bs21(nums []int, target int) int {
	l, r := 0, len(nums)-1 // 两端闭
	for l <= r {           // 终止条件 l > r
		mid := (r-l)/2 + l
		if nums[mid] >= target {
			r = mid - 1 // 注意：搜索左边界时找到target并不直接返回，而是向左侧区间搜索
		} else { // nums[mid] < target
			l = mid + 1 // mid已排除
		}
	}

	// 由于for循环退出条件为 l = r + 1 ，因此当target比nums元素都大时，会出现 l=len(nums)的情况
	// l 停住时还要检查是否走到了target左边界，如果target不存在，那么就会nums[l]!=target
	if l >= len(nums) || nums[l] != target {
		return -1 // 没找到
	}

	return l // target左边界
}
```


### 4.3 寻找target的右边界

```go
func bs3(nums []int, target int) int {
	l, r := 0, len(nums)-1 // 两端闭
	for l <= r {           // 终止条件 l > r
		mid := (r-l)/2 + l
		if nums[mid] == target {
			l = mid + 1 // 注意：搜索右边界时找到target并不直接返回，而是向右侧区间搜索		[mid+1, r]
		} else if nums[mid] > target {
			r = mid - 1 // mid已排除	[l, mid-1]
		} else { // nums[mid] < target
			l = mid + 1 // mid已排除    [mid+1, r]
		}
	}

	// 由于for循环退出条件为 l = r + 1 ，因此当target比nums元素都小时，会出现 r=-1的情况
	// r 停住时还要检查是否走到了target右边界，如果target不存在，那么就会nums[r]!=target
	if r < 0 || nums[r] != target {
		return -1 // 没找到
	}

	return r // target右边界， 也就是 l-1
}

// 简化一些

func bs31(nums []int, target int) int {
	l, r := 0, len(nums)-1 // 两端闭
	for l <= r {           // 终止条件 l > r
		mid := (r-l)/2 + l
		if nums[mid] <= target {
			l = mid + 1 // 注意：搜索右边界时找到target并不直接返回，而是向右侧区间搜索
		} else { // nums[mid] > target
			r = mid - 1 // mid已排除
		}
	}

	// 由于for循环退出条件为 l = r + 1 ，因此当target比nums元素都小时，会出现 r=-1的情况
	// r 停住时还要检查是否走到了target右边界，如果target不存在，那么就会nums[r]!=target
	if r < 0 || nums[r] != target {
		return -1 // 没找到
	}

	return r // target右边界， 也就是 l-1
}

```

## 5. 更多二分查找例题

### 5.1 寻找重复数

```go
// 给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

// 示例 1:

// 输入: [1,3,4,2,2]
// 输出: 2
// 示例 2:

// 输入: [3,1,3,4,2]
// 输出: 3
// 说明：

// 不能更改原数组（假设数组是只读的）。
// 只能使用额外的 O(1) 的空间。
// 时间复杂度小于 O(n2) 。
// 数组中只有一个重复的数字，但它可能不止重复出现一次。

////////////////////////////////////////////////

// 思考
// 1. 不能更改原数组，不能使用额外空间。 那么就不能使用排序和哈希表两种方法
// 2. 如果是只重复一次，还可以通过求和然后作差得到。然而这里也不能用
// 3. 也不能通过不断将元素i放到以i-1其为下标的位置上来找出最后重复的元素，这样会改变数组
// 4. 也就是说，只能通过遍历nums数组来得到答案
//      4.1 可以想到的一种做法是循环检测与快慢指针，必然能指向相同元素。也确实复杂度低于O(n2)
//      4.2 另一种可行的办法是二分查找。对于不含重复项的1~n，取中间元素mid后，必然有mid个<=mid的数。假如说统计到count个小于mid的数，如果count>mid，说明重复数的大小小于mid。

// 二分查找 + 抽屉原理
func findDuplicate(nums []int) int {
    n := len(nums)
    if n < 2 {return 0} // 不存在重复
    
    l, r, mid, count := 0, n, 0, 0
    for l < r {
        mid = (r-l)/2 +l 
        count = 0
        for _, v := range nums {
            if v <= mid {count++}
        }
        
        // 二分
        if count > mid {
            r = mid // mid也可能是重复的
        } else {    // <
            l = mid + 1 // mid及比mid小的数不可能是重复数
        }
    }
    
    // 最后l==r，必然是重复数
    return l
}

// 注意！
// 本题的另外一种解法：循环检测与快慢指针。很重要，参见lt287解法3.
// 本质和 求一个有环链表环的入口 是一样的。先检测是否成环（用快慢指针），再用相同的速度，一个从相遇点，一个从起点出发，再次相遇就是重复
```

### 5.2 寻找比目标字母大的最小字母

```go
// 寻找比目标字母大的最小字母

// 给定一个只包含小写字母的有序数组letters 和一个目标字母 target，寻找有序数组里面比目标字母大的最小字母。

// 数组里字母的顺序是循环的。举个例子，如果目标字母target = 'z' 并且有序数组为 letters = ['a', 'b']，则答案返回 'a'。

// 示例:

// 输入:
// letters = ["c", "f", "j"]
// target = "a"
// 输出: "c"

// 输入:
// letters = ["c", "f", "j"]
// target = "c"
// 输出: "f"

// 输入:
// letters = ["c", "f", "j"]
// target = "d"
// 输出: "f"

// 输入:
// letters = ["c", "f", "j"]
// target = "g"
// 输出: "j"

// 输入:
// letters = ["c", "f", "j"]
// target = "j"
// 输出: "c"

// 输入:
// letters = ["c", "f", "j"]
// target = "k"
// 输出: "c"
// 注:

// letters长度范围在[2, 10000]区间内。
// letters 仅由小写字母组成，最少包含两个不同的字母。
// 目标字母target 是一个小写字母。

////////////////////////////////////////////////


// 方法1：用[26]bool作哈希表（用map也行）记录letter中字母的存在情况。
//       从target的右邻位开始找看存不存在，第一个存在的就是答案
// 方法2： 线性扫描letters，找比目标字母大的第一个字母，返回它；
//		 如果遍历一遍没找到比target大的，则返回letters[0]
// 方法3： 二分查找:要找出target的右邻，而letters是排好序的，那么实际上相当于：
//		 将target插入letters，将会插入在哪？我们要找的是target右邻
// 		 要注意使用labuladong二分两端闭区间模板时，查找target右边界，如果target不存在，最后
// 		 r就会落在第一个比target小的字母。不管存不存在，最后返回的都是r+1对应的那个字母，如果r+1
// 注意，最后如果r+1越界了，需要 模len(letters) 一下

// 二分查找
func nextGreatestLetter(letters []byte, target byte) byte {
	l, r, mid := 0, len(letters)-1, 0
	for l <= r {
		mid = (r-l)/2 + l
		if letters[mid] <= target {
			l = mid + 1
		} else {
			r = mid - 1
		}
	}
	return letters[(r+1)%len(letters)]
}

// 尽管letters是循环的，可能有多个target，
// 但是假定最后一个出现的target才是target的位置，我们要找的就是最后一个target的右邻

```

### 5.3 两个有序数组的中位数

```go
// 给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

// 请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

// 你可以假设 nums1 和 nums2 不会同时为空。

// 示例 1:

// nums1 = [1, 3]
// nums2 = [2]

// 则中位数是 2.0
// 示例 2:

// nums1 = [1, 2]
// nums2 = [3, 4]

// 则中位数是 (2 + 3)/2 = 2.5

//////////////////////////////////////////////

// 寻找两个有序数组的中位数

// 暴力做的话可以用双指针法先合并两个有序数组，再取中位数
// 要求时间复杂度O(log(m+n))
// 基本和二分查找、分治这些有关了

// 这道题应该这样思考：
// 求一个数组的中位数取决于其长度n的奇偶性。
// 若n奇数，则第(n+1)/2个是中位数； 若偶，则中位数为第(n/2)和第(n+1)/2个元素的平均数
// 换言之： ** 有序数组中位数就是有序数组从小到大第k个数 ** （k的解释见上一行)
//
// 对于两个有序数组的中位数也是一样，只要将小于中位数的k-1个数给移除了
// 剩下的第一个数就是中位数。
//
// 直接的按照这个思路，其实就是双指针在不断比较两个数组从头开始的元素，哪个小先踢哪个
// 实现O((m+n)/2)的时间复杂度
//
// 那么要想办法加快这个剔除的过程。
//
// 例如
// A: 1 3 5 7 9 11 12 13 14		长度9
// B: 2 4 6 8 10				长度5
// 合并长度14，那么中位数就是升序第7个数和第8个数的平均值。 这里肉眼可见是 (7+8)/2
// 由于是找第14/2=7个数，可以先从A或B头部删除7/2=3个数，
// 那么便是要删除 A的 1 3 5 或者 B的 2 4 6
// 删哪个呢？
// 删末尾更小的那个，5<6，所以删A的前三个。
//
// 其实看到这，就应该大致明白了，利用这个特性，可以加快删除进度而又不会错过真正的中位数
// 现在A/B变成：
// A: - - - 7 9 11 12 13 14		长度9
// B: 2 4 6 8 10				长度5
//
// 删掉3个了，现在我们要定位到第7-3=4个数据，继续删，
// 删 4/2=2个，由于 4<9 ，所以删除B头部的2个元素
// 得到：
// A: - - - 7 9 11 12 13 14		长度9
// B: - - 6 8 10				长度5
//
// 现在删了5个了，我们要找当前的第4-2=2个元素，因此还要删 2/2=1个数据，因为6<7，所以删6
// 好了，已经删掉6个元素了：
// A: - - - 7 9 11 12 13 14		长度9
// B: - - - 8 10				长度5
//
// 那么第7个和第8个数据怎么判断呢？A，B头部元素谁小谁是第七个
// 因为 7<8所以第7个元素是7
// 再将8和A中7后面的元素9比较，因此第8个元素就是8
//
// 还有一点，如果短的数组剩下的长度小于待删长度，那么整体删除，
// 剩下的就是单个有序数组考虑第k小的问题了
//
// 核心： **折半删除** （当然这里讲的删除不是真删，指针掠过去就行了）
//
// 主要参考自题解https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/man-hua-ru-guo-qi-ta-de-ti-jie-ni-kan-bu-dong-na-j/
//
// 好了本题解决，代码如下：

func findMedianSortedArrays(nums1 []int, nums2 []int) float64 {
	// 特殊
	m, n := len(nums1), len(nums2)
	if m == 0 && n == 0 { // 题目说明不会有这种情况
		return 0
	}
	if m == 0 {
		return medOfSortedArr(nums2)
	}
	if n == 0 {
		return medOfSortedArr(nums1)
	}

	// 二分（折半删除）
	total := m + n       // 总长度
	k := (total + 1) / 2 // 对于total为奇数，第k个就是中位数；偶数的话是左中位数
	// 调用findK返回第k小及第k+1小
	kth, knext := findKthAndNext(nums1, nums2, 0, 0, k)
	// fmt.Println(kth, knext)

	if total%2 == 0 {
		return (float64(kth) + float64(knext)) / 2
	}
	return float64(kth)
}

func medOfSortedArr(nums []int) float64 {
	n := len(nums)
	if n%2 == 0 { // 偶数
		return (float64(nums[n/2-1]) + float64(nums[n/2])) / 2
	}
	return float64(nums[n/2])
}

// 找出第k小元素，以及其后一个， 也就是“主函数”中的left和其后一个元素
// p1,p2是nums1,nums2游标(数组下标)，用来执行“删除”
// k 是待找的第k小
// 使用递归做法
// 要注意k是第k个，转成代码时要减一，作为数组下标
func findKthAndNext(nums1, nums2 []int, p1, p2, k int) (int, int) {

	//fmt.Println(p1, p2, k)

	// 始终保证如果会先空，则必是nums1。 这样可以省去很多if-else
	len1, len2 := len(nums1)-p1, len(nums2)-p2 // 两个数组当前”剩余“的长度
	if len1 > len2 {
		return findKthAndNext(nums2, nums1, p2, p1, k)
	}
	// 如果nums1空了
	if len1 == 0 {
		return nums2[p2+k-1], nums2[p2+k] // kth （以p2开头的第k个）, knext，写个示例就清楚了
	}

	// 如果剩余待删的k变为1，那么返回kth和knext
	if k == 1 {
		kth, knext := 0, 0
		if nums1[p1] <= nums2[p2] { // nums1第p1+1个数和nums2第p2+1个数作比较
			kth = nums1[p1]
			knext = nums2[p2]
			if p1 < len(nums1)-1 && nums1[p1+1] < knext {
				knext = nums1[p1+1]
			}
		} else {
			kth = nums2[p2]
			knext = nums1[p1]
			//fmt.Println(kth, knext, "测试", p1, p2, k)
			if p2 < len(nums2)-1 && nums2[p2+1] < knext {
				knext = nums2[p2+1]
			}
		}
		return kth, knext
	}

	// 折半删除，先定位到待删末尾
	p1If := p1 + int(math.Min(float64(len1), float64(int(k/2)))) - 1
	p2If := p2 + int(math.Min(float64(len2), float64(int(k/2)))) - 1
	// 比较
	if nums1[p1If] <= nums2[p2If] {
		return findKthAndNext(nums1, nums2, p1If+1, p2, k-(p1If-p1+1))
	}
	return findKthAndNext(nums1, nums2, p1, p2If+1, k-(p2If-p2+1))
}

// 总结： 折半删除的思路很妙
// 但是细节是魔鬼！！！！
```

### 5.4 找出第k小的距离对

```go
// 找出第k小的距离对

// 给定一个整数数组，返回所有数对之间的第 k 个最小距离。一对 (A, B) 的距离被定义为 A 和 B 之间的绝对差值。

// 示例 1:

// 输入：
// nums = [1,3,1]
// k = 1
// 输出：0
// 解释：
// 所有数对如下：
// (1,3) -> 2
// (1,1) -> 0
// (3,1) -> 2
// 因此第 1 个最小距离的数对是 (1,1)，它们之间的距离为 0。

// 2 <= len(nums) <= 10000.
// 0 <= nums[i] < 1000000.
// 1 <= k <= len(nums) * (len(nums) - 1) / 2.

// 根据限制条件，时间复杂度一般应不能高于O(n2)，空间复杂度不能为O(k)及以上

// 思考：
// 1. 暴力思路： 用数组存所有距离对（O(n!)空间）,
// 再根据每个数对的距离进行排序（快排、堆排、优先队列等等，O(nlogn)）,自然找到第k小
// 由于题给出len(nums)最大为10000；可想而知O(n!)肯定炸了
// 2. 先将nums排序（O(nlogn)），再线性遍历两两相邻之间的差值绝对值，
// 需要将绝对差值进行排序（并且是带索引的，能通过绝对差值找到原先的数对）
// 绝对差值的排序可以使用堆，也可以利用快排特性实现O(n)。 总体O(nlogn),还能接受
// 这是错的！！！k可能大于n!!!

// 想不到比较好的办法，看题解：

// 使用二分查找+双指针
// 1. 先对nums排序
// 2. 0是理论上可能的最小距离，最大距离则是nums第一个和最后一个的距离，记为top
// 3. 对 [0,top]区间进行二分，得mid
//    接着检查是否存在 >k 个数对，其距离 <mid
//    如果存在，说明mid大了，则继续对 [0,mid-1]二分，反之去[mid+1,top]二分
// 4. 那么，如何高效的计算有多少数对的距离 < mid呢？ 可以使用双指针，实现O(n)
//
// 有关二分查找：
// 很容易理解: 【<mid】 【==mid】(若干数对) 【>mid】
//
// 时间复杂度 O(nlogn + nlogw)，w指最大的数对距离

func smallestDistancePair(nums []int, k int) int {
	// 1. 升序
	sort.Ints(nums)
	n := len(nums)
	// 2. 二分查找. 寻找count最接近k，但是把mid稍一调高又会大于k的状态。停止时自然就是=k的位置
	l, r, mid := 0, nums[n-1], 0
	for l < r { // 终止条件 l==r
		mid = (r-l)/2 + l
		count := countLessEqualMid(nums, mid)
		if count < k {
			l = mid + 1 // [mid+1, r]
		} else { // >=k // 不能排除mid的可能
			r = mid // [l, mid]
		}
	}
	return l
}

// 统计 <= mid 的数对个数
func countLessEqualMid(nums []int, mid int) int {
	count, start := 0, 0
	for i := range nums { // 每一轮都是在找以nums[i]为更大值的所有符合条件的数对
		for nums[i]-nums[start] > mid {
			start++ // 起始位不断右移，直到[start,i]区间内的数对距离<=mid
		}
		count += i - start
	}
	return count
}

```

### 5.5 分割数组的最大值 

```go
// 给定一个非负整数数组和一个整数 m，你需要将这个数组分成 m 个非空的连续子数组。设计一个算法使得这 m 个子数组各自和的最大值最小。

// 注意:
// 数组长度 n 满足以下条件:

// 1 ≤ n ≤ 1000
// 1 ≤ m ≤ min(50, n)
// 示例:

// 输入:
// nums = [7,2,5,10,8]
// m = 2

// 输出:
// 18

// 解释:
// 一共有四种方法将nums分割为2个子数组。
// 其中最好的方式是将其分为[7,2,5] 和 [10,8]，
// 因为此时这两个子数组各自的和的最大值为18，在所有情况中最小。

//////////////////////////////////////////////////////////

// 思考：
// 1. 乍一看很难找到比较好的办法，只好试试暴力求解
// 对于长度为n的数组，分成m个连续非空区间，要想找出所有的分割组合（C(n,m)），时间/空间复杂度太过爆炸
// 暴力行不通了
// 2. 像这种暴力行不通的，组合数巨大的情况，一般想办法从贪心、二分等方向考虑。
// 这里可以思考下二分（当然也有点贪心的味道）
// 将求m个子区间的和的最大值最小，转化成求所有划分出的组合中寻找第1小的组合
// 由于数组是固定的，分成m个区间，不管怎么分，总和还是数组的和，要想所有区间的和的最大值最小
// 理想情况下，这个值就是 sum(nums) / m （每个区间的和相等）
// 但是一般情况下，可能无法保证每个子区间的和都相等，
// 由于是非负整数数组，可以考虑理论上的最小值0，
// 而理论上的最大值max，为了简便起见，选择为 nums总和。
// 要注意这个理论上的上下限都是取不到的，只是为了缩减搜索范围
// 现在，我们对 0~sum(nums)进行二分，得到mid
//
// 对了由于是分成m个子区间，理论上最小的”子区间的和的最大值“应该是max(nums)，因为一个子区间至少要包含一个数据
// 所以现在二分的上下限就是 max(nums)~sum(nums)
//
// 得到mid，mid就是当前”试探“的 ”子区间的和的最大值“
// 然后我们把 mid 作为nums数组的子区间的和的最大值(这里不确定能分多少个子区间)
// 要统计的就是按mid为上限nums所能拆分出的子区间数count
//
// 如果count > m 说明 mid小了；
// count < m 则说明mid大了
// 如果恰好就分了m个呢？就是我们要找的答案吗？
// 不是的，可以想象一下，能以mid为上限恰好可以分m个子区间的情况（或者说满足该条件的mid）
// 有若干个。
// 我们要找的是这些mid中的最小值，也就是左边界。
// 这里直接套用二分查找寻找target左边界的模板就好
//
// 那么外层框架就得到了，但是怎么高效地统计 ”和小于mid的子区间“ 数呢？
// 贪心法，每一个子区间都选到恰好<=mid，再往右加一个就超过mid

func splitArray(nums []int, m int) int {
	// 就不检查特殊情况了，比较繁琐，而且题目有约束

	// 1. 线性遍历得到二分的上下限 O(n)
	n, max, sum := len(nums), 0, 0
	for i := 0; i < n; i++ {
		if nums[i] > max {
			max = nums[i]
		}
		sum += nums[i]
	}
	// 2. 二分找符合条件的mid的”左边界“（最小者）
	l, r, mid, cnt := max, sum, 0, 0
	for l <= r {
		mid = (r-l)/2 + l
		cnt = countSubArrays(nums, mid)
		if cnt > m { // mid小了
			l = mid + 1     // [mid+1, r]
		} else { // mid大或者恰好
			r = mid - 1     // [l, mid-1]   // mid恰好时仍需要向左区间搜索，如果最后左区间搜索不到，会通过l=mid+1回来的
		}
	}
	// 最后ｌ停在我们要求的”mid左边界“
	return l
}

// 【贪心】 统计nums 以mid为“子区间和的最大值” 所能划分的 ”最少的“子区间数
func countSubArrays(nums []int, mid int) int {
	n := len(nums)
	sumSub := 0 // 子区间和
	count := 1  // 子区间数，初始为1，因为最后一个子区间走不到count++
	for i := 0; i < n; i++ {
		if sumSub+nums[i] > mid {
			count++
			sumSub = nums[i]
		} else {
			sumSub += nums[i]
		}
	}
	return count
}

// 体会：
// 当暴力的搜索区间太大，甚至动态规划也感觉复杂度太高的情况，一定要尝试想想
// 二分查找、贪心
// 有的题也要考虑排序。
```

## 参考

- [LeetCode 探索二分查找](https://leetcode-cn.com/explore/learn/card/binary-search/)
- [labuladong的二分查找算法详解](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/er-fen-cha-zhao-suan-fa-xi-jie-xiang-jie-by-labula/)