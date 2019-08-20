---
title: "无显示器、无路由器安装树莓派系统并SSH连接"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["Iro", "树莓派"]
tags: ["Iro", "树莓派", "魔镜"]
keywords: ["Iro", "树莓派", "魔镜", "SSH"]

---

### 1.安装系统

​		进入树莓派官网下载树莓派带桌面版本系统镜像，使用Win32DiskImager软件或者Etcher等软件将镜像文件烧录入事先准备的Micro SD卡。将SD卡插上，即可启动树莓派。

​		但为了能够实现SSH连接，需要对烧录了系统的SD卡的boot分区进行一些修改：

- 新建名称为SSH或ssh的文件。（Raspbian默认不开SSH，需要以此方式提前开启）；
- 修改cmdline.txt在行首添加IP:192.168.137.13。这是设置静态IP的方式。要SSH直连树莓派，必须知道树莓派的IP地址，因此有静态设置IP和动态分配IP两种策略。若要在无显示器条件下提前开启静态IP，需要作此操作。树莓派IP必须设为192.168.137.x。

### 2.SSH连接

- 设置网卡属性。无线网卡设置共享给以太网卡，以太网卡则将IP固定为192.168.137.1（必须和树莓派所设静态IP处于同一网段）

  ![1562500876686](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562500876686.png)

  ![1562500909972](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562500909972.png)

  ![1562500945482](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562500945482.png)

- 查询树莓派IP。用网线连接好笔记本和树莓派之后就可以查看树莓派IP，尽管之前设置了静态IP，看看也不坏事不是？ipconfig查看本机网卡IP，arp -a查看所有与本机相连的设备。如截图所示，192.168.137.10即为树莓派IP。（奇怪的是不是写定的192.168.137.13，而是有10和210两个，经过ping测试和ssh测试，192.168.137.10为树莓派IP）

  ![1562500723912](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562500723912.png)

  ![1562500749779](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562500749779.png)

- SSH连接。使用Putty、Xshell等工具进行SSH连接。ip为所查询到的树莓派IP，port为22。树莓派默认账号为pi，密码为raspberry。至此，成功连接树莓派。

  ![1562500659427](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562500659427.png)



























