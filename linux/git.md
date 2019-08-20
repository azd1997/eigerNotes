---
title: "git学习全系列"
date: 2019-08-17T04:44:28+08:00
draft: false
categories: ["linux"]
tags: ["git"]
keywords: ["git"]
---

# git学习全系列

## 1. 修改commit消息

<https://www.cnblogs.com/revel171226/p/9208589.html>

## 2. Pull&Request

<https://blog.csdn.net/qq_33429968/article/details/62219783>

## 3. 合并多条commit消息

<https://blog.csdn.net/u013276277/article/details/82470177>

## 4. 删除仓库下所有文件但不删除仓库

<https://www.cnblogs.com/Ghost4C-QH/p/8777744.html>

## 5. 与远程仓库建立连接

### 5.1 建立关联

```shell
# 建立本地Git仓库
$ git init
# 与远程主机origin（默认主机名）的仓库建立关联
$ git remote add origin git@github.com:azd1997/EChain.git
# 拉取远程仓库状态，合并到本地仓库
$ git pull
# 本地仓库作出文件修改后，将版本提交到远程仓库
$ git add .
$ git commit -m '提交信息'
$ git push
```

### 5.2 解除关联

```shell
git remote rm origin
```

### 5.3 拒绝合并不相关历史处理

在我的实际操作中，远程仓库创建时设置了LICENSE而没有设置.gitignore，在本地仓库我设置了.gitignore。这导致我在进行上面步骤中`git pull`这一步时提示**拒绝合并不相关历史**

![20190723214615](/images/TIM截图20190723214615.png)

这是因为本地仓库和远程仓库没有相同的commit记录造成的（不确定，待完善）。

使用下面命令可继续合并：

```shell
git pull --allow-unrelated-histories
```

![20190723214818](/images/TIM截图20190723214818.png)
