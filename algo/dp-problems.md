---
title: "动态规划问题集锦"
date: 2020-03-09T11:41:25+08:00
draft: false
categories: ["algo"]
tags: ["动态规划"]
keywords: ["股票问题","背包问题"]
---

## 0. 动态规划简介

## 1. 股票问题

LeetCode上股票问题共有6道：
- [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock)
- [122. 买卖股票的最佳时机II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii)
- [123. 买卖股票的最佳时机III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii)
- [188. 买卖股票的最佳时机IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv)
- [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown)
- [714. 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee)

《剑指offer》专题也有一道股票问题[面试题63. 股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof)。

股票问题主要参考来自$labuladong$的[一个方法团灭6道股票问题](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/yi-ge-fang-fa-tuan-mie-6-dao-gu-piao-wen-ti-by-l-3/)

### 1.1 股票问题的泛化框架

给出一般化的股票问题描述：

- 给定每天的股票价格$prices []int$
- 给定允许进行的最大交易次数$k$，一次完整的交易指买入和卖出，且卖出之前必须先买入股票，而买入股票必须在手头没有股票的时候买入。
- 求最大利润

状态机或者说DP table其实就是在穷举所有的**状态**。

这类求最优问题往往需要进行穷举，而和递归解法不同，动态规划解法是在穷举所有状态，并在每一种状态组合下进行**选择**更新状态：

```go
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 择优(选择1，选择2...)
```

那这道股票问题里边**状态**有哪些：

- 天数：每一天的股价不同
- 剩余的允许最大交易次数：到当前天为止，最多允许k笔交易，我们要求的问题是，到最后一天，最多允许交易K次。
- 股票持有状态：1表示持有股票(买入之后的持有，还没卖出)；0表示没持有股票(没买入或者已卖出的持有状态，手上没有股票)

可做的**选择**呢：

- 买入(buy)：受到两点限制，手上没有股票，且剩余可交易次数k>0
- 卖出(sell)：受到一点限制，手上有股票
- 无操作(rest)

那么**状态的穷举框架**就是：

```go
dp[i][k][0 or 1]
0 <= i <= n-1, 1 <= k <= K
n 为天数，大 K 为最多交易数
此问题共 n × K × 2 种状态，全部穷举就能搞定。

for 0 <= i < n:
    for 1 <= k <= K:
        for s in {0, 1}:
            dp[i][k][s] = max(buy, sell, rest)
```

例如`dp[3][2][1]`表示：第四天(prices数组下标是从0开始的)，我最多还可以交易2次，今天我手上是持有股票的。我可以选择卖出或者无操作。

最终我们要的答案是`dp[n-1][K][0]`。 这表示最后一天，在这n天时间里最多允许交易K次,最后手上的股票全卖出去了，能获得的最大利润是多少。(最后一天手上还有股票的话，最大利润肯定没有卖出去高)

**状态转移框架**

情况1：当前没有股票，是由前一天有股票然后今天卖出、或者是前一天没有股票然后今天无操作

$$dp[i][k][0] = max(dp[i-1][k][1] + prices[i], dp[i-1][k][0])$$

情况2：当前有股票，是由前一天没有股票今天买入的、或者是前一天有股票今天无操作的

$$dp[i][k][1] = max(dp[i-1][k-1][0] - prices[i], dp[i-1][k][1])$$

注意这里的$k-1$。由于一次交易包括买入再卖出，因此可以在买入时(或者卖出时)减1.
在上面的状态转移公式中，第$i$天如果买入一次股票的话，是在最大可交易次数的基础上减$1$了，因此，对于第$i-1$天来讲，最大可交易次数为$k-1$。

**基本样例(base case)**

- $dp[-1][k][0] = 0$： 还没开始，自然最大利润是0
- $dp[-1][k][1] = -inf$： 还没开始，无法持有股票，最大利润是负无穷，表示不可能
- $dp[i][0][0] = 0$： $k$表示不允许交易，自然最大利润是0
- $dp[i][0][1] = 0$： 不允许交易，最大利润为负无穷

关于索引$-1$的处理：可以使用判断语句检测索引越界成为$-1$，也可以在$prices$前填充一个元素位置，用来表示第0天。 下面使用第一种策略处理base case。

将前面关于状态、选择、状态转移、basecase的描述进行整合，得到股票问题的动态规划解题框架：

```go
// 股票价格prices，最大可交易次数k
func solution(prices []int, k int) int {
    n := len(prices)

    // DP table
    dp := make([][][2]int, n)
    for i:=0; i<n; i++ {
        dp[i] = make([][2]int, k+1)
    } 
    
    for i:=0; i<n; i++ {
        // i表示第几天
        for k1:=1; k1<=k; k1++ {
            // k1表示当前最大可交易次数，从1开始

            // base case
            if i-1 == -1 {
                dp[i][k1][0] = 0
                dp[i][k1][1] = -prices[0]
                continue
            } 
            if k1-1 == 0 {
                dp[i][1][0] = max(dp[i-1][k1][1] + prices[i], dp[i-1][k1][0])
                dp[i][1][1] = max(-prices[i], dp[i-1][1][1])
                continue
            }

            // 状态转移
            dp[i][k1][0] = max(dp[i-1][k1][1] + prices[i], dp[i-1][k1][0])
            dp[i][k1][1] = max(dp[i-1][k1-1][0] - prices[i], dp[i-1][k1][1])
        }
    }

    return dp[n-1][k][0]
}

func max(a,b int) int {
    if a>=b {
        return a
    } else {return b}
}
```

动态规划的解法中，如果当前状态的确定只依赖之前**有限个状态**，那么可以对DP table的占用空间进行优化。

### 1.2 买卖股票的最佳时机

这题中相对前面讨论的一般形式，加入了最多交易一次的限制，也就是$k=1$。代入到上面得到的框架中，解法如下：

```go
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP table
    dp := make([][][2]int, n)
    for i:=0; i<n; i++ {
        dp[i] = make([][2]int, 2)
    } 
    
    for i:=0; i<n; i++ {
        // i表示第几天
        for k1:=1; k1<=1; k1++ {
            // k1表示当前最大可交易次数，从1开始

            // base case
            if i-1 == -1 {
                dp[i][k1][0] = 0
                dp[i][k1][1] = -prices[0]
                continue
            } 
            if k1-1 == 0 {
                dp[i][1][0] = max(dp[i-1][k1][1] + prices[i], dp[i-1][k1][0])
                dp[i][1][1] = max(-prices[i], dp[i-1][1][1])
                continue
            }

            // 状态转移
            dp[i][k1][0] = max(dp[i-1][k1][1] + prices[i], dp[i-1][k1][0])
            dp[i][k1][1] = max(dp[i-1][k1-1][0] - prices[i], dp[i-1][k1][1])
        }
    }

    return dp[n-1][1][0]
}

func max(a,b int) int {
    if a>=b {
        return a
    } else {return b}
}
```

由于$k=1$，$base case$可以简化为

```go
if k1-1 == 0 {
    dp[i][1][0] = max(dp[i-1][k1][1] + prices[i], dp[i-1][k1][0])
    dp[i][1][1] = max(-prices[i], dp[i-1][1][1])
    continue
}
```

可见，不管怎样$k$都是1，不会改变，也就是$k$对状态转移没有影响，因此可以将$k$删去。简化后的解：

```go
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP table
    dp := make([][2]int, n)
    
    for i:=0; i<n; i++ {
        // i表示第几天

        // base case
        if i-1 == -1 {
            dp[i][0] = 0
            dp[i][1] = -prices[0]
            continue
        } 
        
        // 状态转移
        dp[i][0] = max(dp[i-1][1] + prices[i], dp[i-1][0])
        dp[i][1] = max(-prices[i], dp[i-1][1])
 
    }

    return dp[n-1][0]
}
```

由于当前状态($dp[i][0]/dp[i][1]$)只依赖$dp[i-1][0]/dp[i-1][1]$，因此可以将$O(2n)$的空间复杂度降为$O(2)$，只使用两个变量来进行DP更新：

```go
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP variables
    dp_i_0, dp_i_1 := 0, math.MinInt32
    // 初始值即为base case
    
    for i:=0; i<n; i++ {
        // i表示第几天
          
        // 状态转移
        dp_i_0 = max(dp_i_1 + prices[i], dp_i_0)
        dp_i_1 = max(-prices[i], dp_i_1)

    }

    return dp_i_0
}
```

### 1.3 买卖股票的最佳时机II

此题可作任意多次交易，也就是$k=inf$。这种情况下，状态的更新同样与$k$无关。

```go
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP variables
    dp_i_0, dp_i_1 := 0, math.MinInt32
    // 初始值即为base case
    tmp := 0
    for i:=0; i<n; i++ {
        // i表示第几天
          
        // 状态转移
        tmp = dp_i_0 
        dp_i_0 = max(dp_i_1 + prices[i], dp_i_0)
        dp_i_1 = max(tmp - prices[i], dp_i_1)
        
    }

    return dp_i_0
}
```

### 1.4 买卖股票的最佳时机III

这道题限制： $k=2$。

这种情况下$k$对状态转移产生影响。

由于比前面$k$不产生影响的情况更为复杂，因此，先直接套原框架，而后再优化空间。

```go
// k=2
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP table
    dp := make([][3][2]int, n)  // k=2
    
    for i:=0; i<n; i++ {
        // i表示第几天
        for k:=1; k<=2; k++ {
            // k表示当前最大可交易数

            // base case
            if i-1 == -1 {
                dp[0][k][0] = 0
                dp[0][k][1] = -prices[0]
                continue
            }
            if k-1 == 0 {
                dp[i][1][0] = max(dp[i-1][1][1] + prices[i], dp[i-1][1][0])
                dp[i][1][1] = max(-prices[i], dp[i-1][1][1])
                continue
            }

            // 状态转移
            dp[i][k][0] = max(dp[i-1][k][1] + prices[i], dp[i-1][k][0])
            dp[i][k][1] = max(dp[i-1][k-1][0] - prices[i], dp[i-1][k][1])

        }      
    }

    return dp[n-1][2][0]
}
```

其实base case中关于k=0时的处理是可以省略的，原因在于$sp[i][0][s]$默认值就是0，不会影响状态转移公式。

```go
// k=2
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP table
    dp := make([][3][2]int, n)  // k=2
    
    for i:=0; i<n; i++ {
        // i表示第几天
        for k:=1; k<=2; k++ {
            // k表示当前最大可交易数

            // base case
            if i-1 == -1 {
                dp[0][k][0] = 0
                dp[0][k][1] = -prices[0]
                continue
            }

            // 状态转移
            dp[i][k][0] = max(dp[i-1][k][1] + prices[i], dp[i-1][k][0])
            dp[i][k][1] = max(dp[i-1][k-1][0] - prices[i], dp[i-1][k][1])

        }      
    }

    return dp[n-1][2][0]
}
```

其实当前第$i$天的状态只与前面$i-1$天状态有关，第$i$天共有$k \times 2$种组合（2指的是两种持有状态，而在本题中，k的限制为2），因此本题只需要4个变量就可以实现DP状态更新：

```go
// k=2
func maxProfit(prices []int) int {
    // 特殊情况
    n := len(prices)
    if n < 2 {return 0}

    // DP variables && base case
    dp_i_1_0, dp_i_1_1 := 0, math.MinInt32
    dp_i_2_0, dp_i_2_1 := 0, math.MinInt32
    
    for i:=0; i<n; i++ {
        // i表示第几天

        // 状态转移
        dp_i_1_0 = max(dp_i_1_1 + prices[i], dp_i_1_0)      // 注意得是 dp_i_k_0更新早于dp_i_k_1。 （因为dp_i_k_1不依赖于dp_i_k_0）
        dp_i_1_1 = max(-prices[i], dp_i_1_1)
        dp_i_2_0 = max(dp_i_2_1 + prices[i], dp_i_2_0) 
        dp_i_2_1 = max(dp_i_1_0 - prices[i], dp_i_2_1)
   
    }

    return dp_i_2_0
}
```

### 1.5 买卖股票的最佳时机IV

题目约束：最多可完成k笔交易。

这道题，如果直接按照数值$k$的条件去建立DP table，很可能创建超大数组，而超出内存限制。优化如下：

- 如果k比n/2大，那么k的变化就对状态转移没有影响；如果k比n/2小，就需要按照框架，考虑k的改变。

```go
func maxProfit(k int, prices []int) int {
    // 特殊情况 
    n := len(prices)
    if n < 2 || k <= 0 {return 0}

    // 分情况进行dp
    if k <= n/2 {
        return dp_k(k, n, prices)
    } else {return dp_inf(n, prices)}
}

func dp_inf(n int, prices []int) int {
    // 空间优化至 2
    dp_i_0, dp_i_1 := 0, -prices[0]
    tmp := 0
    for i:=0; i<n; i++ {
        tmp = dp_i_0
        dp_i_0 = max(dp_i_1 + prices[i], dp_i_0)
        dp_i_1 = max(tmp - prices[i], dp_i_1)
    }
    return dp_i_0
}

func dp_k(k int, n int, prices []int) int {
    // DP table
    dp := make([][][2]int, n)
    for i:=0; i<n; i++ {
        dp[i] = make([][2]int, k+1)
    }
    
    for i:=0; i<n; i++ {
        for k1:=1; k1<=k; k1++ {
            // base case
            if i-1 == -1 {
                dp[0][k1][0] =  0
                dp[0][k1][1] = -prices[0]
                continue
            }
            // 状态转移
            dp[i][k1][0] = max(dp[i-1][k1][1] + prices[i], dp[i-1][k1][0])
            dp[i][k1][1] = max(dp[i-1][k1-1][0] - prices[i], dp[i-1][k1][1])
            // dp[i-1][k1-1][0]  k1==1时其值为0
        }
    }
    return dp[n-1][k][0]
}

// dp_k()可以优化空间至 k * 2
func dp_k(k int, n int, prices []int) int {
    // DP table
    dp := make([][2]int, k+1)
    for i:=0; i<=k; i++ {
        dp[i] = [2]int{0, math.MinInt32}    // math.MinInt32是比所有的-prices[i]都小，才能作为初值
    }
    
    for i:=0; i<n; i++ {
        for k1:=1; k1<=k; k1++ {
            // 状态转移
            dp[k1][0] = max(dp[k1][1] + prices[i], dp[k1][0])
            dp[k1][1] = max(dp[k1-1][0] - prices[i], dp[k1][1])
        }
    }
    return dp[k][0]
}
```

### 1.6 最佳买卖股票时机含冷冻期

限制：

- 总交易数不限
- 卖出股票之后，第二天不能买入。（冷冻期为一天）

这道题在$k=inf$基础上加上了冷冻期的限制，有了冷冻期之后，股票持有状态`rest`有三种：持有、没持有但可买入、没持有但处于冷冻期。
但其实由于冷冻期为1，固定数值，比较好处理，可以直接在原状态转移公式基础上改，而不需要定义三种持有状态。

```go
func maxProfit(prices []int) int {
    // 特殊情况 
    n := len(prices)
    if n < 2 {return 0}

    // DP table
    dp := make([][2]int, n)
    for i:=0; i<n; i++ {

        // base case
        if i-1 == -1 {
            dp[0][0] = 0
            dp[0][1] = -prices[0] 
        }

        // 状态转移
        dp[i][0] = max(dp[i-1][1] + prices[i], dp[i-1][0])
        dp[i][1] = max(dp[i-2][0] - prices[i], dp[i-1][1])  // dp[i-2][0] - prices[i] 冷冻期处理

    }
    return dp[n-1][0]
}


// 空间优化为 3
func maxProfit(prices []int) int {
    // 特殊情况 
    n := len(prices)
    if n < 2 {return 0}


    // DP variables
    dp_i_0, dp_i_1 := 0, -prices[0]         // 取-prices[0]和math.MinInt32都没问题
    dp_ipre_0 := 0       // 相当于dp[i-2][0]
    tmp := 0
    for i:=0; i<n; i++ {
        tmp = dp_i_0
        dp_i_0 = max(dp_i_1 + prices[i], dp_i_0)
        dp_i_1 = max(dp_ipre_0 - prices[i], dp_i_1)
        dp_ipre_0 = tmp
    }
    return dp_i_0
}

```

### 1.7 买卖股票的最佳时机含手续费

限制：
- 可进行任意多次交易（$k=inf$）
- 每次交易需扣除手续费

和对允许最多交易次数k的更新一样，这里让手续费在买入阶段收取。

```go
func maxProfit(prices []int, fee int) int {
    // 特殊情况 
    n := len(prices)
    if n < 2 {return 0}

    // DP table
    dp := make([][2]int, n)
    // base case
    dp[0][0] = 0
    dp[0][1] = -prices[0]

    for i:=1; i<n; i++ {
        // 状态转移
        dp[i][0] = max(dp[i-1][1] + prices[i], dp[i-1][0])
        dp[i][1] = max(dp[i-1][0] - prices[i] - fee, dp[i-1][1])  

    }
    return dp[n-1][0]
}


// 空间优化为 2
func maxProfit(prices []int, fee int) int {
    // 特殊情况 
    n := len(prices)
    if n < 2 {return 0}

    // DP variables
    dp_i_0, dp_i_1 := 0, math.MinInt32
    tmp := 0
    for i:=0; i<n; i++ {
        tmp = dp_i_0
        dp_i_0 = max(dp_i_1 + prices[i], dp_i_0)
        dp_i_1 = max(tmp - prices[i] - fee, dp_i_1)
    }
    return dp_i_0
}
```

### 1.8 股票的最大利润

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

解同**[1.2]节**

## 2. 零钱兑换问题

零钱兑换问题有两道：
- [零钱兑换](https://leetcode-cn.com/problems/coin-change/)
- [零钱兑换II](https://leetcode-cn.com/problems/coin-change-2/)

### 2.1 零钱兑换

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。


### 2.2 零钱兑换II

给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。

你可以假设：

0 <= amount (总金额) <= 5000
1 <= coin (硬币面额) <= 5000
硬币种类不超过 500 种
结果符合 32 位符号整数


## 3. 背包问题

### 3.1 0-1背包问题

- 问题描述

一个可装载$W$重量的背包和$N$个物品，每个物品都有重量和价值两个属性，第$i$的物品的重量为$wt[i]$，价值为$val[i]$，用该背包装下的物品的最大价值为多少？

（0-1背包中0/1的含义是指物品要么能装入背包，要么不能）

- 举例

```
N = 3, W = 4
wt = [2,1,3]
val = [4,2,3]
```

- 动态规划框架

按照股票问题总结的动态规划穷举步骤，来为背包问题提取解题框架

  1. 状态
     - 这个问题可以描述为：背包承重量还剩$w$，有$n$个物品可以选择装，获得的最大价值是$dp[n][w]$ 
  2. 选择
     - 装，或者不装
  3. 得到穷举框架

```go
    for n in [0, N]:
        for w in [0, W]:
            dp[n][w] = 择优(装， 不装)
```
  4. 明确`dp`含义
     - 对于前$i$件物品，当前背包的容量为$w$，可装下的最大价值为$dp[i][w]$
     - 最终要求的答案就是$dp[N][W]$
     - base case: dp[0][w] = dp[i][0] = 0
  5. 进一步细化框架：
```go
    dp[N+1][W+1]
    dp[0][w] = dp[i][0] = 0

    for i in [1, N]:
        for w in [1, W]:
            dp[n][w] = max(装， 不装)
    return dp[N][W]        
```
  6. 状态转移
     - 装： 对于第i-1个物品，dp[i][w] = dp[i-1][w-wt[i-1]] + val[i-1]
     - 不装： dp[i][w] = dp[i-1][w] 
     - dp[i][w] = max(dp[i-1][w-wt[i-1]] + val[i-1], dp[i][w] = dp[i-1][w])

代码如下：
```go
func bag(W, N int, wt, val []int) int {
    dp := make([][]int, N+1)
    for i:=0; i<N; i++ {
        dp[i] = make([]int, W+1)
    }
    // base case 已在初始化中做好

    for i:=1; i<=N; i++ {
        for w:=1; w<=W; w++ {
            if w - wt[i-1] < 0 {
                // 当前容量装不下
                dp[i][w] = dp[i-1][w]
            } else {
                // 选择
                dp[i][w] = max(dp[i-1][w-wt[i-1]] + val[i-1], dp[i-1][w])
            }
        } 
    }
    return dp[N][W]
}
```





