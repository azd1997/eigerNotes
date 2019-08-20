---
title: "初次使用树莓派需要做的配置"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["Iro", "树莓派"]
tags: ["Iro", "树莓派", "魔镜"]
keywords: ["Iro", "树莓派", "魔镜", "系统设置"]

---

### 1.修改用户密码及用户切换

![1562506863061](/images/1562506863061.png)

### 2.系统设置

$ sudo raspi-config进入系统设置界面如下：

![1562507381154](/images/1562507381154.png)

最主要的目的是扩展系统分区至整张SD卡。（Expand Filesystem）

![1562507404826](/images/1562507404826.png)

也可以顺手更新一下。当然更新使用命令行也是很方便的。最后重启下树莓派，使所作设置生效。

![1562507514787](/images/1562507514787.png)

### 3.树莓派 respberry安全关机命令重启命令

-  树莓派可以通过下面几个命令来实现安全关机：

```
sudo shutdown -h now

sudo halt

sudo poweroff

sudo init 0
```

上面四行代码都可以，执行一行都可以安全关机, ^_^

- 树莓派重启 定时重启方法：

```
sudo reboot

shutdown -r now
```

### 3.1 重启之后发现的问题

重启之后，发现192.168.137.10已经连接不上了，使用arp -a发现又多了一个ip。这个是树莓派的新IP。

![1562508850271](/images/1562508850271.png)

这里分析下原因：很可能是我设置的静态IP192.168.137.13未成功，系统仍然进行动态分配IP，所以IP会变，但我做了静态IP的设置，所以结果列表里的IP类型显示为静态。这在之后会重新设置静态IP，现在先用动态IP。

### 4.更换镜像源

上面选择了直接通过系统设置更新系统软件包，由于默认采用的是树莓派官方镜像源，所以下载速度比较慢，需要更换国内一些机构维护的镜像源，比如清华、华中科技、浙江大学、阿里等等。去https://www.raspbian.org/RaspbianMirrors查找。需要注意的是，所换的镜像源最好是系统版本对应的（目前树莓派最新的三个版本从前往后是：Jessie、Stretch、Buster）。

具体方法为：编辑 /etc/apt/sources.list文件（sudo nano  /etc/apt/sources.list），进行源的替换。

![1562509655018](/images/1562509655018.png)

更换为：（当选定一个源想要更换时，最好同时打开该源网页以及官方http://raspbian.raspberrypi.org/raspbian/网页进行比对查看。）

https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/

nano编辑器 CTRL O保存并指定文件名，CTRL X退出

![1562510657876](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562510657876.png)

最后更新一下

```
sudo apt-get update
```

![1562510949420](/images/1562510949420.png)

下载速度引起舒适~

### 5.远程桌面连接

树莓派包括所有主流计算机系统都有很多远程桌面控制软件，比较常见的有：XRDP、VNCviewer、Teamviewer、Wayknow等等。因为VNCViewer其实是树莓派自带的，只不过默认不开启，所以我先使用VNC进行桌面连接。后边会安装Teamviewer（需要桌面环境进行配置），Teamviewer最大的好处是可以外网访问。

#### 5.1 VNC连接

首先在设置里开启VNC

![1562512207843](/images/1562512207843.png)

![1562512244640](/images/1562512244640.png)

打开电脑上的VNC viewer软件，连接树莓派

![1562512484266](/images/1562512484266.png)

![1562512529252](/images/1562512529252.png)

现在成功登录树莓派桌面！（我在boot分区显示设置中设置了竖屏，所以是下图这样）

![1562512581846](/images/1562512581846.png)

接下来跟着系统引导设置一些地区之类无关痛痒的选项，略过。Wifi设置在桌面环境下非常容易，也略过。

#### 5.2 Teamviwer连接

比较好的一篇文章：https://www.cnblogs.com/trilobita/p/10506094.html；https://blog.csdn.net/cungudafa/article/details/84495472

由于我习惯了桌面配置，所以直接安装好再桌面设置就好了，不多说。

### 6 静态IP配置文件修改法

```
sudo nano /etc/dhcpcd.conf
```

![1562513217988](/images/1562513217988.png)

添加内容。另外注意配置时，静态IP一定要和路由器网段一致，比如：假设路由器的IP为 192.168.0.x 网段，则上面的 static ip_address 就要对应的修改为 192.168.0.x/24 。还有一点就是手动静态IP要注意不能跟路由器 DHCP 所自动分配的 IP 冲突，否则树莓派就有可能无法正常联网。

因为我是通过笔记本Wlan共享给树莓派所连接的有线网卡，所以这里我应该设置为192.168.137.x网段

```
interface eth0
static ip_address=192.168.137.13/24
static routers=192.168.137.1
static domain_name_servers=8.8.8.8 114.114.114.114
```

保存后重启

```
sudo reboot
```

重新查看下树莓派的IP：

![1562513912458](/images/1562513912458.png)

奇怪的是，192.168.137.169现在变成真正的静态IP了，不管了，现在不追究这些细节。

还可以参考这篇：https://blog.csdn.net/Jack_Lue/article/details/82912327