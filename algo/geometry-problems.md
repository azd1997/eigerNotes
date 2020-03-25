---
title: "几何相关问题"
date: 2020-03-25T11:10:25+08:00
draft: false
categories: ["algo"]
tags: ["几何"]
keywords: ["几何问题"]
---

## 0. 导语

## 1. 三维形体的表面积

LeetCode 892

```go
在 N * N 的网格上，我们放置一些 1 * 1 * 1  的立方体。

每个值 v = grid[i][j] 表示 v 个正方体叠放在对应单元格 (i, j) 上。

请你返回最终形体的表面积。

 

示例 1：

输入：[[2]]
输出：10
示例 2：

输入：[[1,2],[3,4]]
输出：34
示例 3：

输入：[[1,0],[0,2]]
输出：16
示例 4：

输入：[[1,1,1],[1,0,1],[1,1,1]]
输出：32
示例 5：

输入：[[2,2,2],[2,1,2],[2,2,2]]
输出：46
 

提示：

1 <= N <= 50
0 <= grid[i][j] <= 50

///////////////////////////////////////////

// 思考路线是：遍历grid，叠加每一个grid[i][j]贡献的表面积
// 对于top和bottom，只要grid[i][j]>0，就直接了2的表面积
// 对于四侧面，只有当【相邻】位置的高度小于grid[i][j],才会贡献表面积，
// 贡献量为 v = grid[i][j] - neighbor （减去相邻位置的高度）。
// 也就是max(v, 0)

func surfaceArea3(grid [][]int) int {
	// 特殊情况
	N := len(grid) // N*N网格

	area := 0

	for i := 0; i < N; i++ {
		for j := 0; j < N; j++ {
			if grid[i][j] > 0 {
				area += 2 // bottom & top
				for k := 0; k < 4; k++ {
					ny, nx := i+dy[k], j+dx[k]                  // 某个方向上的邻位
					if ny >= 0 && ny < N && nx >= 0 && nx < N { // 邻位坐标有效
						area += max(grid[i][j]-grid[ny][nx], 0)
					} else { // 邻位越界
						area += grid[i][j]
					}
				}
			}
		}
	}
	return area

}

var dy = [4]int{1, 0, -1, 0}
var dx = [4]int{0, -1, 0, 1}

func max(a, b int) int {
	if a >= b {
		return a
	}
	return b
}

// 另外一种思路，则是，先所有表面积相加，再把重叠的面积减去
func surfaceArea4(grid [][]int) int {
	// 特殊情况
	N := len(grid) // N*N网格

	area := 0

	for i := 0; i < N; i++ {
		for j := 0; j < N; j++ {
			if grid[i][j] > 0 {
				area += 4*grid[i][j] + 2 // 4个侧面 + bottom & top
				if i > 0 {
					area -= 2 * min(grid[i-1][j], grid[i][j]) // 每次只减去与i-1的重叠面积，注意是要乘以2的
				}
				if j > 0 {
					area -= 2 * min(grid[i][j-1], grid[i][j]) // 减去与j-1邻位的重叠面积
				}
			}
		}
	}
	return area
}

func min(a, b int) int {
	if a <= b {
		return a
	}
	return b
}

```