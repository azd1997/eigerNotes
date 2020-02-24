---
title: "LeetCode——计算器系列"
date: 2020-02-24T08:40:11+08:00
draft: false
categories: ["algo"]
tags: ["LeetCode"]
keywords: ["stack", "calculator"]
---

## 0. 导语

计算器作为栈的经典应用，本文就[力扣](https://leetcode-cn.com)平台上与计算器有关的以下题目进行总结:

- [150.逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
- [227.基本计算器II](https://leetcode-cn.com/problems/basic-calculator-ii/)（与[面试题16.26计算器](https://leetcode-cn.com/problems/calculator-lcci/)相同）
- [224.基本计算器](https://leetcode-cn.com/problems/basic-calculator/)
- [770.基本计算器IV](https://leetcode-cn.com/problems/basic-calculator-iv/)

## 1. 逆波兰表达式求值

### 1.1 题意

根据[逆波兰表示法](https://baike.baidu.com/item/%E9%80%86%E6%B3%A2%E5%85%B0%E5%BC%8F/128437)，求表达式的值。

有效的运算符包括 $+, -, *, /$ 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

**说明：**

   - 整数除法只保留整数部分。
   - 给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。

示例 1：

    输入: ["2", "1", "+", "3", "*"]
    输出: 9
    解释: ((2 + 1) * 3) = 9

示例 2：

    输入: ["4", "13", "5", "/", "+"]
    输出: 6
    解释: (4 + (13 / 5)) = 6

示例 3：

    输入: ["10", "6", "9", "3", "+", "-11", "*", "/", "*", "17", "+", "5", "+"]
    输出: 22
    解释:
    ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
    = ((10 * (6 / (12 * -11))) + 17) + 5
    = ((10 * (6 / -132)) + 17) + 5
    = ((10 * 0) + 17) + 5
    = (0 + 17) + 5
    = 17 + 5
    = 22

### 1.2 解答

- 思路

  - 线性遍历后缀表达式
     1. 遇数字压入操作数栈`stack`;
     2. 遇操作符则弹出栈顶两个操作数进行计算，计算结果压回栈中
     3. 最后栈中只剩最终计算结果一项

*ps: 题目全是二元操作符，所以才是出栈顶两个数计算；而且要注意**先出栈的为右操作数，后出栈的为左操作数**，事实上获取逆波兰表达式(后缀表达式)时是相反的*

- 实现

```go
// 使用辅助栈存储中间数字
func evalRPN(tokens []string) int {
	stack := make([]int, 0)
	l := 0
	for i:=0; i<len(tokens); i++ {
		l = len(stack)
		switch tokens[i] {
        case "+":
            // 这一步等价于
            // a = stack.pop()
            // b = stack.pop()
            // res = b + a  (注意左右顺序)
            // stack.push(res)
            // 这里这么写只是利用了go slice append方法
			stack = append(stack[:l-2], stack[l-2] + stack[l-1])
		case "-":
			stack = append(stack[:l-2], stack[l-2] - stack[l-1])
		case "*":
			stack = append(stack[:l-2], stack[l-2] * stack[l-1])
		case "/":
			stack = append(stack[:l-2], stack[l-2] / stack[l-1])
		default:	// 数字
			tmp, _ := strconv.Atoi(tokens[i])
			stack = append(stack, tmp)
		}
	}
	// 对于完整有效的逆波兰表达式，运算结束后栈中只剩一个数字，直接返回即可
	return stack[0]
}
```

## 2. 基本计算器II

### 2.1 题意

实现一个基本的计算器来计算一个简单的字符串表达式的值。

字符串表达式仅包含非负整数，+， - ，*，/ 四种运算符和空格  。 整数除法仅保留整数部分。

示例 1:

    输入: "3+2*2"
    输出: 7

示例 2:

    输入: " 3/2 "
    输出: 1

示例 3:

    输入: " 3+5 / 2 "
    输出: 5

说明：
  - 你可以假设所给定的表达式都是有效的。
  - 请不要使用内置的库函数 eval。

### 2.2 解答

- 解法一

由于没有括号，中缀表达式中运算符优先级非常清晰简单: $*,/$高于$+,-$，因此完全可以模拟人计算时的思考来求解。

例如 `3 + 2 * 4`，解法一的做法是，先计算好所有乘除的结果，得到`3 + 8`，最后再将所有数直接相加得到 `11`

  1. 初始化操作数栈`stack`，`op = '+'`(op记载的是当前访问到的操作符的前一个操作符), `num = 0`(用于累加计算连续数字字符组成的若干位数)。
  2. 线性向右遍历
      - 遇空格则跳过
      - 遇数字，则数字更新 `num = num * 10 + s[i]`
      - 遇符号：
        - `+`: 直接将`num`压入`stack`并重置`num`， `op`更新为`s[i]`
        -  `-`: 直接将`-num`压入`stack`并重置`num`， `op`更新为`s[i]`
        - `*/`: 取出操作数栈顶作为左操作数，`num`作为右操作数，计算结果后压回栈中
  3. 最后将栈中所有数相加得到结果

```go
// 1. 使用栈辅助，顺序模拟手动计算
func calculate(s string) int {
	stack := make([]int, 0)	// 操作数的栈，加法栈

	n := len(s)
	num := 0		// 用来存储多位数
	var op byte = '+'
	for i:=0; i<n; i++ {

		// 空格
		// 如果遇到空格，说明不可能有连续数字，此时要处理前面保存的op和num，
		// 也就是向下执行到switch op {}

		// 数字 这里需要注意，当i==n-1时，如果s[i]还是数字，会继续向下执行
		if s[i]>='0' {
			num = num*10 + int(s[i] - '0')
		}

		// 操作符(这里就直接默认剩下是操作符了)
		// i=n-1，最后一个时，必须把剩下的op给处理掉
		if (s[i]<'0' && s[i]!=' ') || i==n-1 {

			switch op {
			case '+':
				// 把加号前的数num压入栈
				// a+b 这里a在栈顶，然后把b(num)也给压到栈顶
				stack = append(stack, num)
				op, num = s[i], 0		// 操作符更新， num重置
			case '-':
				// 把减号前的数num转负存储
				// a-b 这里b为num， 所以是将-num压入
				stack = append(stack, -num)
				op, num = s[i], 0
			case '*':
				// a*b a为原先栈顶， b为num，计算，将结果压栈
				temp := stack[len(stack)-1] * num		// 取栈顶元素与num进行计算
				stack = stack[:len(stack)-1]	// 栈顶出栈
				stack = append(stack, temp)		// 将乘积压回栈内
				op, num = s[i], 0
			case '/':
				// a/b a为原先栈顶， b为num，计算，将结果压栈
				temp := stack[len(stack)-1] / num		// 取栈顶元素除以num
				stack = stack[:len(stack)-1]	// 栈顶出栈
				stack = append(stack, temp)		// 将商压回栈内
				op, num = s[i], 0
			default:
				// Nothing
			}
            //fmt.Println(stack)
		}

    }

	// 经过上面处理，所有中间数字都计算出来，在栈中，最后只需要将栈中所有元素相加即可
	res := 0
	for len(stack)!=0 {
		res += stack[len(stack)-1]
		stack = stack[:len(stack)-1]
	}

	return res
}
```

- 解法二

基于第1节逆波兰表达式的方法，可以通过将中缀表达式转换为后缀表达式，而后再计算逆波兰表达式，来实现计算器。这样的逆波兰实现方法是比较通用的计算器实现方法，以下将介绍它。

实现分两步走：实现`getRPN()`和`calcRPN()`分别用于中缀转后缀和计算后缀表达式(与第1节做法一模一样，这里不再介绍)

`getRPN`的做法：

1. 定义好符号栈`stack`和返回后缀表达式`tokens`
2. 线性遍历中缀表达式`s`
3. 若`s[i]`为：
   1. 空格则跳过
   2. 数字则处理连续数字，获得完整连续的数字
   3. 符号：
      1. `*,/`将符号栈中优先级**等于或高于本身**的运算符(`*,/`)出栈并追加到`tokens`，并将`s[i]`压入栈中。
      2. `+,-`将符号栈中优先级**等于或高于本身**的运算符(`*,/,+,-`)出栈并追加到`tokens`，并将`s[i]`压入栈中。
4. 最后将栈中剩余的操作符依次追加到`tokens`

这里有个小问题：连续数字的处理。解法1实现和第3节的实现中使用了一种方法: 用一个标志标记是否存在连续数字字符并用于记录数字的和`temp = temp * 10 + cur`，这样的话，每次遇到操作符，需要将这个标志进行重置。

而这里则使用了另外一种比较推荐的做法(利用指针移动):

```go
if s[i] >= '0' && s[i] <= '9' {
    // 看后面字符是否仍是数字
    k := i+1
    for ; k<n && isDigit(s[k]); k++ {}
    tokens = append(tokens, s[i:k])		// 将数字加入tokens
    i = k - 1	// 更新i (为什么是k-1，这是因为continue之后会i++)
    continue
}
```

具体实现如下：

```go
// 标准的计算器做法：中缀计算表达式转后缀表达式(逆波兰表达式)，再计算

func calculate2(s string) int {
	// 1. 中缀转后缀
	tokens := getRPN(s)
	// 2. 计算后缀表达式
	return evalRPN(tokens)
}

// 转成逆波兰表达式
func getRPN(s string) []string {
	n := len(s)
	tokens := make([]string, 0, n)		// 返回的逆波兰表达式
	stack := make([]string, 0)		// 符号栈

	temp := -1		// 累加数字	-1表示还没有数字
	// （和解法1有点不一样，这里处理数字不能使用0，只能使用-1等负数作为标记）
	for i:=0; i<n; i++ {
		//fmt.Println(tokens, stack)
		if s[i] == ' ' {continue}	// 空格跳过
		// 遇到数字
		if s[i] >= '0' && s[i] <= '9' {
			// 看后面字符是否仍是数字
			k := i+1
			for ; k<n && isDigit(s[k]); k++ {}
			tokens = append(tokens, s[i:k])		// 将数字加入tokens
			i = k - 1	// 更新i (为什么是k-1，这是因为continue之后会i++)
			continue
		}

		// 遇到其他字符（操作符），先将操作数加入返回数组tokens
		// 注意这里要考虑运算符优先级（*/ > +-）
		if s[i] == '/' || s[i] == '*' {
			// 将栈中同等优先级或更高优先级的运算符弹出并添加到tokens
			for len(stack) != 0 && (stack[len(stack)-1] == "/" || stack[len(stack)-1] == "*") {
				tokens = append(tokens, stack[len(stack)-1])
				stack = stack[:len(stack)-1]
			}
			stack = append(stack, string(s[i]))		// 当前运算符入栈
			continue
		}
		// 遇到+-符号
		if s[i] == '+' || s[i] == '-' {
			// 将栈中同等优先级或更高优先级的运算符弹出并添加到tokens
			for len(stack) != 0 && (
					stack[len(stack)-1] == "/" ||
					stack[len(stack)-1] == "*" ||
					stack[len(stack)-1] == "+" ||
					stack[len(stack)-1] == "-") {
				tokens = append(tokens, stack[len(stack)-1])
				stack = stack[:len(stack)-1]
			}
			stack = append(stack, string(s[i]))		// 当前运算符入栈
			continue
		}
	}
	// 最后temp是否还有数字
	if temp != -1 {
		tokens = append(tokens, strconv.Itoa(temp))
	}
	// 操作符栈中的符号加入到结果
	for len(stack) != 0 {
		tokens = append(tokens, stack[len(stack)-1])
		stack = stack[:len(stack)-1]
	}

	return tokens
}

func isOPeration(str string) bool {
	return str == "+" || str == "-" || str == "*" || str == "/"
}

// 计算逆波兰表达式
func evalRPN(tokens []string) int {
	stack := make([]int, 0)		// 操作数栈
	for _, token := range tokens {
		// 如果是操作符(二元)，那么把操作数栈顶两个数弹出计算，将结果压回
		if isOPeration(token) {
			ans := calc(token[0], stack[len(stack)-2], stack[len(stack)-1])
			stack[len(stack)-2] = ans
			stack = stack[:len(stack)-1]
		} else {	// 否则为操作数
			stack = append(stack, string2Num(token))
		}
	}
	return stack[len(stack)-1]	// 只要是正常的表达式，操作数栈最后只剩结果
}

func string2Num(str string) int {
	// 自己实现string2num
	//n := len(str)
	//sign := 1
	//start := 0
	//if str[0] == '-' {
	//	sign = -1
	//	start = 1
	//}
	//
	//res := 0
	//for i:=start; i<n; i++ {
	//	res = res * 10 + int(str[i] - '0')
	//}
	//res = res * sign
	//return res

	// 调用API
	ret, _ := strconv.Atoi(str)
	return ret
}

// 判断是否为 '0'~'9'
func isDigit(ch byte) bool {
	return ch >= '0' && ch <= '9'
}

func calc(op byte, a, b int) int {
	switch op {
	case '+':
		return a+b
	case '-':
		return a-b
	case '*':
		return a*b
	case '/':
		return a/b
	default:
		return -1
	}
}
```


## 3. 基本计算器

### 3.1 题意

实现一个基本的计算器来计算一个简单的字符串表达式的值。

字符串表达式可以包含左括号 ( ，右括号 )，加号 + ，减号 -，非负整数和空格  。

示例 1:

    输入: "1 + 1"
    输出: 2

示例 2:

    输入: " 2-1 + 2 "
    输出: 3

示例 3:

    输入: "(1+(4+5+2)-3)+(6+8)"
    输出: 23

说明：

   - 你可以假设所给定的表达式都是有效的。
   - 请不要使用内置的库函数 eval。

## 3.2 解答

- 解法一

本题和上一题的区别在于本题没有`*,/`但却增加了`()`括号，这意味着，这里需要处理括号的存在，其实可以把第2节解法2中有关`*/`的部分删掉，而后再额外增加`()`的处理。

`()`如何处理？按人的思维，括号内需要优先计算，而放到中缀转后缀的过程中，则是将括号内的运算符优先添加到后缀表达式中，这样，在计算后缀表达式结果时，自然就先计算了括号内表达式。

基于栈，`()`括号匹配是很容易的事：遇`(`压栈，遇`)`则不断出栈并添加到`tokens`直至遇到配对的`(`。

而对于普通的运算符`+-`，每次遇到就将栈中`(`以后的符号都出栈加入到`tokens`，然后再把自身压入栈中(这样通过栈把运算符的顺序颠倒后再加入到`tokens`)

```go
package lt224

import (
	"fmt"
	"strconv"
)


// 标准的计算器做法：中缀计算表达式转后缀表达式(逆波兰表达式)，再计算

func calculate(s string) int {
	// 1. 中缀转后缀
	tokens := getRPN(s)
	fmt.Println(tokens)
	// 2. 计算后缀表达式
	return evalRPN(tokens)
}

// 转成逆波兰表达式
func getRPN(s string) []string {
	n := len(s)
	tokens := make([]string, 0, n)		// 返回的逆波兰表达式
	stack := make([]string, 0)		// 符号栈

	temp := -1		// 累加数字	-1表示还没有数字
	// （和解法1有点不一样，这里处理数字不能使用0，只能使用-1等负数作为标记）
	for i:=0; i<n; i++ {
		//fmt.Println(tokens, stack)
		if s[i] == ' ' {continue}	// 空格跳过
		// 遇到数字
		if s[i] >= '0' && s[i] <= '9' {
			// 数字累加
			if temp == -1 {
				temp = int(s[i] - '0')
			} else {
				temp = temp*10 + int(s[i] - '0')
			}
		} else {
			// 遇到其他字符（操作符），先将操作数加入返回数组tokens
			if temp != -1 {
				tokens = append(tokens, strconv.Itoa(temp))	// 将数字压入
				temp = -1
			}
			// 再判断当前字符是不是操作符，是则将栈中的所有操作符加入到结果
			if isOPeration(string(s[i])) {
				for len(stack) != 0 {
					// 遇到左括号 '(' 结束
					if stack[len(stack)-1] == "(" {break}
					tokens = append(tokens, stack[len(stack)-1])
					stack = stack[:len(stack)-1]
				}
				// 再把当前符号入栈
				stack = append(stack, string(s[i]))
			} else {
				// 该字符不是符号，那么就是左右括号
				if s[i] == '(' {
					// 左括号入栈
					stack = append(stack, string(s[i]))
				}
				if s[i] == ')' {
					// 右括号将出栈元素加入到结果tokens，直到遇到左括号
					for stack[len(stack)-1] != "(" {
						tokens = append(tokens, stack[len(stack)-1])
						stack = stack[:len(stack)-1]
					}
					// 左括号出栈
					stack = stack[:len(stack)-1]
				}
				// 连括号也不是？不管了
			}
		}
	}
	// 最后temp是否还有数字
	if temp != -1 {
		tokens = append(tokens, strconv.Itoa(temp))
	}
	// 操作符栈中的符号加入到结果
	for len(stack) != 0 {
		tokens = append(tokens, stack[len(stack)-1])
		stack = stack[:len(stack)-1]
	}

	return tokens
}

func isOPeration(str string) bool {
	return str == "+" || str == "-" || str == "*" || str == "/"
}

// 计算逆波兰表达式
func evalRPN(tokens []string) int {
	stack := make([]int, 0)		// 操作数栈
	for _, token := range tokens {
		// 如果是操作符(二元)，那么把操作数栈顶两个数弹出计算，将结果压回
		if isOPeration(token) {
			ans := calc(token[0], stack[len(stack)-2], stack[len(stack)-1])
			stack[len(stack)-2] = ans
			stack = stack[:len(stack)-1]
		} else {	// 否则为操作数
			stack = append(stack, string2Num(token))
		}
	}
	return stack[len(stack)-1]	// 只要是正常的表达式，操作数栈最后只剩结果
}

func string2Num(str string) int {
	ret, _ := strconv.Atoi(str)
	return ret
}

func calc(op byte, a, b int) int {
	switch op {
	case '+':
		return a+b
	case '-':
		return a-b
	case '*':
		return a*b
	case '/':
		return a/b
	default:
		return -1
	}
}
```

## 4. 基本计算器IV

### 4.1 题意

给定一个表达式 expression 如 expression = `"e + 8 - a + 5"` 和一个求值映射，如 `{"e": 1}`（给定的形式为 evalvars = `["e"]` 和 evalints = `[1]`），返回表示简化表达式的标记列表，例如 `["-1*a","14"]`

   - 表达式交替使用块和符号，每个块和符号之间有一个空格。
   - 块要么是括号中的表达式，要么是变量，要么是非负整数。
   - 块是括号中的表达式，变量或非负整数。
   - 变量是一个由小写字母组成的字符串（不包括数字）。请注意，变量可以是多个字母，并注意变量从不具有像 `"2x"` 或 `"-x"` 这样的前导系数或一元运算符 。

表达式按通常顺序进行求值：先是括号，然后求乘法，再计算加法和减法。例如，expression = `"1 + 2 * 3"` 的答案是 `["7"]`。

输出格式如下：

   - 对于系数非零的每个自变量项，我们按字典排序的顺序将自变量写在一个项中。例如，我们永远不会写像 `“b*a*c”` 这样的项，只写 `“a*b*c”`。
   - 项的次数等于被乘的自变量的数目，并计算重复项。(例如，`"a*a*b*c"` 的次数为 `4`。)。我们先写出答案的最大次数项，用字典顺序打破关系，此时忽略词的前导系数。
   - 项的前导系数直接放在左边，用星号将它与变量分隔开(如果存在的话)。前导系数 `1` 仍然要打印出来。
   - 格式良好的一个示例答案是 `["-2*a*a*a", "3*a*a*b", "3*b*b", "4*a", "5*c", "-6"]` 。
   - 系数为 `0` 的项（包括常数项）不包括在内。例如，“0” 的表达式输出为 `[]`。



示例：

    输入：expression = "e + 8 - a + 5", evalvars = ["e"], evalints = [1]
    输出：["-1*a","14"]

    输入：expression = "e - 8 + temperature - pressure",
    evalvars = ["e", "temperature"], evalints = [1, 12]
    输出：["-1*pressure","5"]

    输入：expression = "(e + 8) * (e - 8)", evalvars = [], evalints = []
    输出：["1*e*e","-64"]

    输入：expression = "7 - 7", evalvars = [], evalints = []
    输出：[]

    输入：expression = "a * b * c + b * a * c * 4", evalvars = [], evalints = []
    输出：["5*a*b*c"]

    输入：expression = "((a - b) * (b - c) + (c - a)) * ((a - b) + (b - c) * (c - a))",
    evalvars = [], evalints = []
    输出：["-1*a*a*b*b","2*a*a*b*c","-1*a*a*c*c","1*a*b*b*b","-1*a*b*b*c","-1*a*b*c*c","1*a*c*c*c","-1*b*b*b*c","2*b*b*c*c","-1*b*c*c*c","2*a*a*b","-2*a*a*c","-2*a*b*b","2*a*c*c","1*b*b*b","-1*b*b*c","1*b*c*c","-1*c*c*c","-1*a*a","1*a*b","1*a*c","-1*b*c"]



提示：

  - expression 的长度在 [1, 250] 范围内。
  - evalvars, evalints 在范围 [0, 100] 内，且长度相同。

### 4.2 解答