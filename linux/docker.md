---
title: "docker学习全系列"
date: 2019-08-17T04:42:16+08:00
draft: false
categories: ["linux"]
tags: ["docker"]
keywords: ["docker", "docker-compose"]
---

# docker学习全系列

参考：

阮一峰博客:

- <http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html>
- <http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html>

## docker-compose安装

从<https://github.com/docker/compose/releases>下载对应版本二进制文件，复制粘贴到本机`$PATH`下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ sudo cp docker-compose-Linux-x86_64 /usr/bin/docker-compose
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ cd /usr/bin
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/bin$ sudo chmod a+x docker-compose
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/bin$ cd ~
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker-compose --version
docker-compose version 1.24.1, build 4667896b
```

## docker换源

编辑`/etc/default/docker`并在末尾追加：

```shell
DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"
```

保存后重启docker即可：

```shell
sudo service docker restart
```

## docker停止容器

```shell
# 终止所有容器
docker stop $(docker ps -a -q)
```
