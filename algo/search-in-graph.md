---
title: "图搜索算法——DFS与BFS"
date: 2020-03-13T09:12:20+08:00
draft: false
categories: ["algo"]
tags: ["搜索"]
keywords: ["DFS","BFS"]
---

## 0. 导语

虽然这里讲图搜索算法，但其实DFS/BFS也可以应用与树的搜索，只是树搜索那一篇讲二叉搜索树已经很长了，因此，这两个最常见的搜索算法放到这里来讲。（ps:树可以看做是有向无环图）

## 1. DFS

深度优先搜索/深度优先遍历

- 本质：枚举。
  - 通过枚举状态空间的所有状态，来找到可行解。

- 经典应用：迷宫(Maze)问题

- DFS的状态空间将形成一颗状态空间树

- 时间复杂度：一般为$O(n!)$或$O(n^k)$

- 优点：空间消耗小。只需要保存当前状态密切相关的信息

- 解决的问题：需要遍历全部状态或者大部分状态直到找到可行解的情况。

  - 图的遍历
  - 排列/组合方案
  - 复杂大奥只能枚举+判断的问题

- DFS的关键：如何确定状态空间
  - （搜什么？怎么搜？怎么搜得不重复不漏？）

- 如何加快DFS？ 剪枝
  - 对一些根本不可能有可行解的分支或者说区域不再继续搜索

### 1.2 例题

#### 1.2.1 课程表

LeetCode上课程表系列有三道题。

[207. 课程表](https://leetcode-cn.com/problems/course-schedule)

[210. 课程表II](https://leetcode-cn.com/problems/course-schedule-ii)

[630. 课程表III](https://leetcode-cn.com/problems/course-schedule-iii)（并不基于DFS求解）

1. **题207**

题面如下：


    你这个学期必须选修 numCourse 门课程，记为 0 到 numCourse-1 。
    在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们：[0,1]
    给定课程总量以及它们的先决条件，请你判断是否可能完成所有课程的学习？

    示例 1:
    输入: 2, [[1,0]] 
    输出: true
    解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。
    
    示例 2:
    输入: 2, [[1,0],[0,1]]
    输出: false
    解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成​课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。
    
    提示：
    输入的先决条件是由 边缘列表 表示的图形，而不是 邻接矩阵 。详情请参见图的表示法。
    你可以假定输入的先决条件中没有重复的边。
    1 <= numCourses <= 10^5


这道题的关键在于：
- 遍历一个有向图（课程视作图的结点，课程的依赖关系视作有向边）
- 判断图中是否有环

对于DFS而言，无论是递归实现还是栈实现，都需要增加一个`visited`数组用以标记是否访问过，一般来讲就是使用布尔变量（`[]bool`）来标记。

而要检查是否成环，那么就是一轮DFS中没有遇到**该轮中已访问过**的结点。因此`visited`需要能够标记三种状态：没访问过、本轮已访问过、已经访问过而且排除成环可能，分别用`0,1,2`表示。

实现如下

```go
func canFinish(numCourses int, prerequisites [][]int) bool {
    // 1. 构建邻接矩阵用来表示图
    adjacency := make([][]int, numCourses)
    for i:=0; i<n; i++ {
        adjacency[i] = make([]int, numCourses)
    }
    // 2. 根据prerequisites填充邻接矩阵中的有向边
    for _, v := range prerequisites {
        adjacency[v[1]][v[0]] = 1   // 学习课程v[1]之后才能学习v[0]
    }

    // 3. 构建访问数组visited
    visited := make([]int, numCourses)  // 默认值为0，没访问过

    // 以每个课程为起点，尝试DFS，看是否成环
    for i:=0; i<numCourses; i++ {
        // 只要从某一个课程开始DFS成环，要想修全部课程，必然经过该课程，那么也就有环，所以是一旦某一轮检测到环就返回false
        if !dfs(adjacency, visited, i) {return false}
    }

    return true // 没有环，可以完成所有课程
}

// 这里提一下，go语言切片虽然是引用类型，但如果切片在函数体内扩容了，那么函数体内使用的切片就和传进去那个不是同一个切片了。
// 这里由于穿进去的切片都不会扩容，因此可以直接传递，并不断被dfs（）修改

// 从课程x开始DFS，判断是否成环。true代表没环，false代表有环
func dfs(adjacency [][]int, visited []int, x int) bool {
    // 首先检查visited
    if visited[x] == 1 {return false}   // 本轮访问过，成环
    if visited[x] == 2 {return true}    // 前面轮DFS访问过，且不会成环
    
    // DFS
    visited[x] = 1  // 先标记本轮访问过
    for j:=0; j<len(adjacency); j++ {
        if adjacency[x][j] == 1 &&      // 修完x可以修课程j
            !dfs(adjacency, visited, j) {   // 从课程j开始DFS成环
                return false
        }
    }
    // 没有return false，说明从课程x出发往所有可能方向走都不会成环，应该返回true
    visited[x] = 2  // 结束本轮DFS，将标记置2
    return true
}
```

ps: 课程表问题是经典的拓扑排序问题，这里使用DFS（尽管没有排序，只是确定是否成环）实现，也可以使用BFS+入度表实现。

ps: 这里使用了邻接矩阵表示图，但是课程表这样的场景下更可能是稀疏图，最好使用邻接表表示图。

2. **题210**

题210和题207基本一样，只不过题210要求返回一个可行的修完全部课程的数组（或者说修课顺序），没有的话返回空数组。

问题关键在于，从某门课程出发，既要判断其不成环，还要返回该轮DFS总共经过了几个结点（修了几门课）

下面的实现使用邻接表来表示图，而且关键在于”全局“栈的使用，，其他地方的逻辑和题207一致。

总体代码如下


```go
// DFS解法
func findOrder(numCourses int, prerequisites [][]int) []int {
	// 1.检查numCourses
	if numCourses <= 0 {
		return nil
	}

	// 2. 检查prerequisites
	if len(prerequisites) == 0 {
		// 课程没有依赖关系（图中没有有向边），说明随便怎样的顺序都可以
		order := make([]int, numCourses)
		for i := 0; i < numCourses; i++ {
			order[i] = i
		}
		return order
	}

	// 3. 标记数组 visited/marked/flags之类的名字
	// 0表示没访问，1表示当前轮已访问，2表示前面的其他轮已经访问
	visited := make([]int, numCourses)

	// 4. 初始化有向图 graph 的 begin，数组的每一项代表起点，是前驱结点
	graph := make([]map[int]bool, numCourses)
	for i := 0; i < numCourses; i++ {
		graph[i] = make(map[int]bool)
	}

	// 5. 初始化有向图 end， graph[i].map中存的是后继节点们
	for _, p := range prerequisites {
		graph[p[1]][p[0]] = true // 将后继节点加入
	}

	// 6. 使用栈stack记录递归的顺序
	// 这个Stackz在DFS过程中会将没有后继节点的课程放到栈底位置，
	// 没有前驱节点的课程压到栈顶
	stack := make([]int, 0) // 栈其实就是DFS路径
	for i := 0; i < numCourses; i++ {
		if dfs(i, graph, visited, &stack) { // 如果发现环，那么就返回空数组
			return nil
		}
	}

	// 7. DFS过程中一直没有出现成环情况
	// 那么说明当前的栈stack存的就是课程表路径,将顺序颠倒即可
	if len(stack) != numCourses {
		return nil
	} // 其实肯定是==的，只不过，假如stack没这么长，那样的话下面交换就会panic
	for i := 0; i < numCourses/2; i++ {
		stack[i], stack[numCourses-i-1] = stack[numCourses-i-1], stack[i]
	}

	return stack
}

// 从课程x开始DFS，判断是否成环。false不成环，true成环，
// 此外还返回path []int。仅当path是完整课表顺序才返回
func dfs(i int, graph []map[int]bool, visited []int, stack *[]int) bool {
	// 1. 检查visited数组
	if visited[i] == 1 { // 当前轮访问过i，说明成环
		return true
	}
	if visited[i] == 2 { // 其他轮访问过了，从i开始不会成环
		return false
	}

	// 2. 标记当前轮正在访问i
	visited[i] = 1

	// 3. 对后继节点进行DFS
	for j := range graph[i] {
		// 后继节点存在成环情况，直接返回true
		if dfs(j, graph, visited, stack) {
			return true
		}
	}

	// 4. i的所有后继节点都不成环，所以从i开始也不成环
	// 将visited[i]置2
	visited[i] = 2

	// 5. 将i入栈
	*stack = append(*stack, i)

	return false // 不成环
}

```

#### 1.2.2 N皇后

LeetCode上N皇后相关的有四道题。

[51. N皇后](https://leetcode-cn.com/problems/n-queens)

[52. N皇后II](https://leetcode-cn.com/problems/n-queens-ii)

[面试题08.12 八皇后](https://leetcode-cn.com/problems/eight-queens-lcci)

[1222. 可以攻击到国王的皇后](https://leetcode-cn.com/problems/queens-that-can-attack-the-king)（与DFS没啥关系，也很简单）

1. 题51.

    n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击(**两两不在同一行、同一列、同一45度斜线上**)。

![N皇后](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/8-queens.png)

    上图为 8 皇后问题的一种解法。

    给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。

    每一种解法包含一个明确的 n 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。

    示例:
    输入: 4
    输出: [
    [".Q..",  // 解法 1
    "...Q",
    "Q...",
    "..Q."],

    ["..Q.",  // 解法 2
    "Q...",
    "...Q",
    ".Q.."]
    ]
    解释: 4 皇后问题存在两个不同的解法。

```go
// 思考：典型的图问题，这里使用DFS求解，回溯思想

func solveNQueens(n int) [][]string {
	// 特殊情况
	if n <= 0 {
		return nil
	}
	if n == 1 {
		return [][]string{{"Q"}}
	}

	// 初始化res矩阵，先全空
	res := make([][]string, 0)

	// 构造1张空白棋盘
	blank := genBlankChessBoard(n)

	// dfs或者说回溯
	dfs(n, &res, blank, 0) // 从第0行开始

	return res
}

func genBlankChessBoard(n int) [][]byte {
	res := make([][]byte, n)
	for i := 0; i < n; i++ {
		res[i] = make([]byte, n)
	}
	for i := 0; i < n; i++ {
		for j := 0; j < n; j++ {
			res[i][j] = '.'
		}
	}

	return res
}

func boardToRet(board [][]byte) []string {
	n := len(board)
	res := make([]string, n)
	for i := 0; i < n; i++ {
		res[i] = string(board[i])
	}
	return res
}

// DFS + 回溯 + 剪枝
func dfs(n int, res *[][]string, board [][]byte, row int) {
	// 结束条件
	if row == n { // 所有行填上了皇后，就该结束当前这轮DFS
		*res = append(*res, boardToRet(board)) // 保存这一可行解
		return
	}

	// 对当前行所有列的位置尝试放上皇后，并紧接着DFS下一行
	// 其实从这里可看出，这就是一个n叉树
	// 但是可以通过检查是否是可以放置皇后的位置，来实现剪枝，避免不必要的遍历
	for col := 0; col < n; col++ {
		// 检查当前位置是否可放置皇后，不可以则跳过
		if !isValid(n, board, row, col) {
			continue
		}
		// 进行选择
		board[row][col] = 'Q'
		// 进行下一行决策
		dfs(n, res, board, row+1)
		// 还原当前行的决策，恢复成'.'
		board[row][col] = '.'
	}
}

// 检查在此刻的棋盘上，在[row][col]位置是否可以放皇后
func isValid(n int, board [][]byte, row int, col int) bool {
	// 检查列上是否有其他皇后
	for i := 0; i < row; i++ { // 这是因为放皇后是逐行往下放的，当检查[row][col]时其下方一定是空的
		if board[i][col] == 'Q' {
			return false
		}
	}

	// 检查行上是否有其他皇后？不用，因为填的时候就是按一行填一个的规则

	// 检查左上方45度斜线上是否有皇后
	for i, j := row-1, col-1; i >= 0 && j >= 0; i, j = i-1, j-1 {
		if board[i][j] == 'Q' {
			return false
		}
	}
	// 检查右上方45度斜线上是否有皇后
	for i, j := row-1, col+1; i >= 0 && j < n; i, j = i-1, j+1 {
		if board[i][j] == 'Q' {
			return false
		}
	}

	return true
}

////////////////////////////////////////////////

// 纯暴力遍历n叉树时间复杂度O(n^n)，上面经过剪枝后差不多是O(n!)
```

2. 题52

```go
// N皇后II

// 只需要返回所有可行解的个数

// 状态压缩的骚操作还没搞懂
// 这里先直接使用回溯框架解题

func totalNQueens(n int) int {
	if n == 1 {
		return 1
	}
	if n < 4 {
		return 0
	} // n<=0,n=2,n=3都没有解，这里直接当做特殊情况处理

	// 回溯框架
	// 路径（已做过的选择）
	// 当前可做的选择列表
	// 终止条件
	solCount := 0                  // 解法个数
	board := genBlankChessBoard(n) // 生成一个空白棋盘
	dfs(n, &solCount, board, 0)
	return solCount
}

func dfs(n int, solCount *int, board [][]byte, row int) {
	// 终止条件
	if row == n {
		*solCount++ // 可行解数加1
		return
	}

	for col := 0; col < n; col++ {
		// 检查这个位置能否放皇后
		if !isValid(n, board, row, col) {
			continue
		}

		// 做选择
		board[row][col] = 'Q'
		// 进入下一行
		dfs(n, solCount, board, row+1)
		// 回溯，撤销选择
		board[row][col] = '.'
	}
}

func genBlankChessBoard(n int) [][]byte {
	res := make([][]byte, n)
	for i := 0; i < n; i++ {
		res[i] = make([]byte, n)
	}
	for i := 0; i < n; i++ {
		for j := 0; j < n; j++ {
			res[i][j] = '.'
		}
	}

	return res
}

func boardToRet(board [][]byte) []string {
	n := len(board)
	res := make([]string, n)
	for i := 0; i < n; i++ {
		res[i] = string(board[i])
	}
	return res
}

// 检查在此刻的棋盘上，在[row][col]位置是否可以放皇后
func isValid(n int, board [][]byte, row int, col int) bool {
	// 检查列上是否有其他皇后
	for i := 0; i < row; i++ { // 这是因为放皇后是逐行往下放的，当检查[row][col]时其下方一定是空的
		if board[i][col] == 'Q' {
			return false
		}
	}

	// 检查行上是否有其他皇后？不用，因为填的时候就是按一行填一个的规则

	// 检查左上方45度斜线上是否有皇后
	for i, j := row-1, col-1; i >= 0 && j >= 0; i, j = i-1, j-1 {
		if board[i][j] == 'Q' {
			return false
		}
	}
	// 检查右上方45度斜线上是否有皇后
	for i, j := row-1, col+1; i >= 0 && j < n; i, j = i-1, j+1 {
		if board[i][j] == 'Q' {
			return false
		}
	}

	return true
}

```

3. 八皇后

与题51一致

#### 1.2.3 世界冰球锦标赛

[P4799 [CEOI2015 Day2]世界冰球锦标赛](https://www.luogu.com.cn/problem/P4799)

题目描述
译自 CEOI2015 Day2 T1「Ice Hockey World Championship」

今年的世界冰球锦标赛在捷克举行。Bobek 已经抵达布拉格，他不是任何团队的粉丝，也没有时间观念。他只是单纯的想去看几场比赛。如果他有足够的钱，他会去看所有的比赛。不幸的是，他的财产十分有限，他决定把所有财产都用来买门票。

给出 Bobek 的预算和每场比赛的票价，试求：如果总票价不超过预算，他有多少种观赛方案。如果存在以其中一种方案观看某场比赛而另一种方案不观看，则认为这两种方案不同。

输入格式

第一行，两个正整数 $N$ 和 $M$($1 \leq N \leq 40$,$1 \leq M \leq 10^{18}$) ，表示比赛的个数和 Bobek 那家徒四壁的财产。

第二行，$N$个以空格分隔的正整数，均不超过 $10^{16}$，代表每场比赛门票的价格。

输出格式
输出一行，表示方案的个数。由于 $N$ 十分大，注意：答案 $\le 2^{40}$。

输入输出样例
- 输入 

    ```
    5 1000
    100 1500 500 500 1000
    ```
- 输出 

    ```
    8
    ```

说明/提示

样例解释
八种方案分别是：

- 一场都不看，溜了溜了
- 价格 100 的比赛
- 第一场价格 500 的比赛
- 第二场价格 500 的比赛
- 价格 100 的比赛和第一场价格 500 的比赛
- 价格 100 的比赛和第二场价格 500 的比赛
- 两场价格 500 的比赛
- 价格 1000 的比赛

有十组数据，每通过一组数据你可以获得 10 分。各组数据的数据范围如下表所示：

|数据组号|	1-2 | 1−2 |	3-4 | 3−4 | 	5-7 | 5−7 |	8-10 | 8−10 |
|-|-|-|-|-|-|-|-|-|
|$N \leq$	| 10 | 10 |	20 | 20 | 	40 | 40 |	40 | 40 |
|$M \leq$ |	$10^6$ | $10^6$ |$10^{18}$| $10^{18}$ | $10^6$ | $10^6$ | $10^{18}$ | $10^{18}$ |

### 1.2.4 岛屿的最大面积

- [岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

```go
package lt695

// 岛屿的最大面积

// 典型的DFS题（BFS应该也行）

func maxAreaOfIsland(grid [][]int) int {
	// 这里使用grid自身作标记（访问过的1就改成2）
	// 如果题目要求不能修改grid，就另建一个visited矩阵

	// 特殊情况
	m := len(grid)
	if m == 0 {
		return 0
	}
	n := len(grid[0])
	if n == 0 {
		return 0
	}

	maxArea := 0
	// DFS
	for i := 0; i < m; i++ {
		for j := 0; j < n; j++ {
			if grid[i][j] == 1 {
				area := dfs(grid, m, n, i, j)
				if area > maxArea {
					maxArea = area
				}
			}
		}
	}

	return maxArea
}

// 返回岛屿面积
func dfs(grid [][]int, m, n, row, col int) int {
	// 如果访问过就返回
	// ps:其实不用这句判断，因为grid的变化是在传递的，
	// 前面只有当grid[i][j]==1时（说明是新的孤立的岛屿）才会进入这里

	area := 1                //自身
	grid[row][col] = 2       // 标记访问过
	for k := 0; k < 4; k++ { // 4个方向
		newRow, newCol := row+dy[k], col+dx[k]
		if newRow >= 0 && newRow < m &&
			newCol >= 0 && newCol < n &&
			grid[newRow][newCol] == 1 {
			area += dfs(grid, m, n, newRow, newCol)
		}
	}
	return area
}

// 方向数组 上右下左
var dy = []int{-1, 0, 1, 0}
var dx = []int{0, 1, 0, -1}

```

## 2. BFS


## 参考

[喂你脚下有坑 深度优先搜索](https://wnjxyk.keji.moe/algorithm/algorithm-abc/dfs/)