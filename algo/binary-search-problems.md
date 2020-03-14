---
title: "二分查找问题集锦"
date: 2020-03-14T12:34:24+08:00
draft: false
categories: ["algo"]
tags: ["二分查找"]
keywords: ["二分查找"]
---

## 0. 导语

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

```

### 3.1 在排序数组中查找元素的第一个和最后一个位置

## 参考

- [LeetCode 探索二分查找](https://leetcode-cn.com/explore/learn/card/binary-search/)