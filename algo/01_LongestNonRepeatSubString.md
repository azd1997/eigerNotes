---
title: "寻找最长不重复子字符串"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["algo"]
tags: ["go", "algorithm", "Leetcode"]
keywords: ["字符串处理", "Leetcode", "最长子串"]

---

求取字符串中最长的含有不重复字符的子字符串的长度。

### 1 开始之前

先了解下Go语言字符串的处理机制

```go
func compareBytesStringRune(s string)  {
   //比较下一个字符串在这三种情况下的存储情况
   println("sBytes： ", []byte(s))
   println("sString： ", s)

   for i, ch := range []byte(s) {
      println("按Byte遍历，第", i, "个元素为：", ch)
   }
   for i, ch := range s {
      println("直接String遍历，第", i, "个元素为：", ch)
   }
   for i, ch := range []rune(s) {
      println("按Rune遍历，第", i, "个元素为：", ch)
   }

}

func main() {
   compareBytesStringRune("azdisgood")
   compareBytesStringRune("艾振东棒棒的")
}
```

输出结果如下：

```
sBytes：  [3/32]0xc000031eb8
sString：  azd
按Byte遍历，第 0 个元素为： 97
按Byte遍历，第 1 个元素为： 122
按Byte遍历，第 2 个元素为： 100
直接String遍历，第 0 个元素为： 97
直接String遍历，第 1 个元素为： 122
直接String遍历，第 2 个元素为： 100
按Rune遍历，第 0 个元素为： 97
按Rune遍历，第 1 个元素为： 122
按Rune遍历，第 2 个元素为： 100
sBytes：  [6/32]0xc000031eb8
sString：  你好
按Byte遍历，第 0 个元素为： 228
按Byte遍历，第 1 个元素为： 189
按Byte遍历，第 2 个元素为： 160
按Byte遍历，第 3 个元素为： 229
按Byte遍历，第 4 个元素为： 165
按Byte遍历，第 5 个元素为： 189
直接String遍历，第 0 个元素为： 20320
直接String遍历，第 3 个元素为： 22909
按Rune遍历，第 0 个元素为： 20320
按Rune遍历，第 1 个元素为： 22909

Process finished with exit code 0
```

得出结论：直接遍历string，其实是按rune进行遍历。当字符串只包含ASCII字符时，三种遍历方法是一样的。rune是Go的可变长度字符表示，表示中文时一个rune代表了三个byte。

### 2 寻找最长不重复子串算法实现

V1版本只考虑英文字符串，V2版本则增加了对中文字符串的支持。

```go
package main

//Leetcode算法题：寻找字符串中连续不重复的最长子串，并返回其长度。

func maxLengthOfNonRepeatSubStrV1(s string) int {
   //遍历字符串，将每个遍历到的字符作为key，其索引作为value，更新入map。

   lastOccuredMap := make(map[byte]int)
   start := 0  //所遍历的字符其开始位置
   maxLength := 0 //最长不重复子串长度

   for i, ch := range []byte(s) {

      //更新字符起始位
      lastI, exists := lastOccuredMap[ch]
      if exists && lastI >= start {
         start = lastI + 1
      }

      //当遍历到的字符距离start的间隔比之前存的maxLength大时，更新maxLength
      //这是因为当遍历到不重复字符时，没有啥操作，此时就应该更新maxLength
      if i-start+1 > maxLength {
         maxLength = i-start+1
      }

      //写入/更新map
      lastOccuredMap[ch] = i
   }

   return maxLength
}

func maxLengthOfNonRepeatSubStrV2(s string) int {
   //遍历字符串，将每个遍历到的字符作为key，其索引作为value，更新入map。

   lastOccuredMap := make(map[rune]int)
   start := 0  //所遍历的字符其开始位置
   maxLength := 0 //最长不重复子串长度

   for i, ch := range []rune(s) {

      //更新字符起始位
      lastI, exists := lastOccuredMap[ch]
      if exists && lastI >= start {
         start = lastI + 1
      }

      //当遍历到的字符距离start的间隔比之前存的maxLength大时，更新maxLength
      //这是因为当遍历到不重复字符时，没有啥操作，此时就应该更新maxLength
      if i-start+1 > maxLength {
         maxLength = i-start+1
      }

      //写入/更新map
      lastOccuredMap[ch] = i

   }

   return maxLength
}

func main() {
   print(maxLengthOfNonRepeatSubStrV1("azdloveazdaawerrz"), " ")  // maxLength -> 7
   print(maxLengthOfNonRepeatSubStrV1("helloworld"), " ")
   print(maxLengthOfNonRepeatSubStrV1("happynewyear"), " ")
   print(maxLengthOfNonRepeatSubStrV1("azdlovezritistrue"), " ")
   print(maxLengthOfNonRepeatSubStrV1("哈哈今天天气真好啊"), " ")
   println()
   print(maxLengthOfNonRepeatSubStrV2("azdloveazdaawerrz"), " ")  // maxLength -> 7
   print(maxLengthOfNonRepeatSubStrV2("helloworld"), " ")
   print(maxLengthOfNonRepeatSubStrV2("happynewyear"), " ")
   print(maxLengthOfNonRepeatSubStrV2("azdlovezritistrue"), " ")
   print(maxLengthOfNonRepeatSubStrV2("哈哈今天天气真好啊"), " ")
}
```

输出结果为：

```
7 5 5 9 11
7 5 5 9 5
Process finished with exit code 0
```

###

