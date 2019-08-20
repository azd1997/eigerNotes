---
title: "Iro——树莓派安装魔镜、语音助手、智能家居全系列"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["linux"]
tags: ["树莓派", "MagicMirror", "Wukong", "HomeAssistant"]
keywords: ["树莓派", "MagicMirror", "Wukong", "HomeAssistant"]
---

# Iro——树莓派安装魔镜、语音助手、智能家居全系列

## 1. 树莓派安装系统及SSH连接

### 1.1 安装系统

​进入树莓派官网下载树莓派带桌面版本系统镜像，使用Win32DiskImager软件或者Etcher等软件将镜像文件烧录入事先准备的Micro SD卡。将SD卡插上，即可启动树莓派。

​但为了能够实现SSH连接，需要对烧录了系统的SD卡的boot分区进行一些修改：

- 新建名称为SSH或ssh的文件。（Raspbian默认不开SSH，需要以此方式提前开启）；
- 修改cmdline.txt在行首添加IP:192.168.137.13。这是设置静态IP的方式。要SSH直连树莓派，必须知道树莓派的IP地址，因此有静态设置IP和动态分配IP两种策略。若要在无显示器条件下提前开启静态IP，需要作此操作。树莓派IP必须设为192.168.137.x。

### 1.2 SSH连接

- 设置网卡属性。无线网卡设置共享给以太网卡，以太网卡则将IP固定为192.168.137.1（必须和树莓派所设静态IP处于同一网段）

![1562500876686](/images/1562500876686.png)
<!-- ![1562500876686](../../../static/images/1562500876686.png) -->

![1562500909972](/images/1562500909972.png)
<!-- ![1562500909972](../../../static/images/1562500909972.png)-->

![1562500945482](/images/1562500945482.png)
<!-- ![1562500945482](../../../static/images/1562500945482.png) -->

- 查询树莓派IP。用网线连接好笔记本和树莓派之后就可以查看树莓派IP，尽管之前设置了静态IP，看看也不坏事不是？ipconfig查看本机网卡IP，arp -a查看所有与本机相连的设备。如截图所示，192.168.137.10即为树莓派IP。（奇怪的是不是写定的192.168.137.13，而是有10和210两个，经过ping测试和ssh测试，192.168.137.10为树莓派IP）

![1562500723912](/images/1562500723912.png)
<!-- ![1562500723912](../../../static/images/1562500723912.png) -->

![1562500749779](/images/1562500749779.png)
<!-- ![1562500749779](../../../static/images/1562500749779.png) -->

- SSH连接。使用Putty、Xshell等工具进行SSH连接。ip为所查询到的树莓派IP，port为22。树莓派默认账号为pi，密码为raspberry。至此，成功连接树莓派。

![1562500659427](/images/1562500659427.png)
<!-- ![1562500659427](../../../static/images/1562500659427.png) -->

## 2. 初次使用树莓派的一些设置

### 2.1 修改用户密码及用户切换

![1562506863061](/images/1562506863061.png)

![1562506863061](/images/1562506863061.png)
<!-- ![1562506863061](../../../static/images/1562506863061.png) -->

### 2.2 系统设置

`sudo raspi-config`进入系统设置界面如下：

![1562507381154](/images/1562507381154.png)

最主要的目的是扩展系统分区至整张SD卡。（Expand Filesystem）

![1562507404826](/images/1562507404826.png)

也可以顺手更新一下。当然更新使用命令行也是很方便的。最后重启下树莓派，使所作设置生效。

![1562507514787](/images/1562507514787.png)

### 2.3 安全关机命令重启命令

- 树莓派可以通过下面几个命令来实现安全关机：

```shell
sudo shutdown -h now

sudo halt

sudo poweroff

sudo init 0
```

上面四行代码都可以，执行一行都可以安全关机, ^_^

- 树莓派重启 定时重启方法：

```shell
sudo reboot

shutdown -r now
```

#### 2.3.1 重启之后发现的问题

重启之后，发现192.168.137.10已经连接不上了，使用arp -a发现又多了一个ip。这个是树莓派的新IP。

![1562508850271](/images/1562508850271.png)

这里分析下原因：很可能是我设置的静态IP192.168.137.13未成功，系统仍然进行动态分配IP，所以IP会变，但我做了静态IP的设置，所以结果列表里的IP类型显示为静态。这在之后会重新设置静态IP，现在先用动态IP。

### 2.4 更换镜像源

上面选择了直接通过系统设置更新系统软件包，由于默认采用的是树莓派官方镜像源，所以下载速度比较慢，需要更换国内一些机构维护的镜像源，比如清华、华中科技、浙江大学、阿里等等。去<https://www.raspbian.org/RaspbianMirrors>查找。需要注意的是，所换的镜像源最好是系统版本对应的（目前树莓派最新的三个版本从前往后是：Jessie、Stretch、Buster）。

具体方法为：编辑 `/etc/apt/sources.list`文件（`sudo nano  /etc/apt/sources.list`），进行源的替换。

![1562509655018](/images/1562509655018.png)

更换为：（当选定一个源想要更换时，最好同时打开该源网页以及官方<http://raspbian.raspberrypi.org/raspbian/>网页进行比对查看。）

```shell
https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/
```

nano编辑器 `CTRL O`保存并指定文件名，`CTRL X`退出

![1562510657876](/images/1562510657876.png)

最后更新一下

```shell
sudo apt-get update
```

![1562510949420](/images/1562510949420.png)

下载速度引起舒适~

### 2.5 远程桌面连接

树莓派包括所有主流计算机系统都有很多远程桌面控制软件，比较常见的有：XRDP、VNCviewer、Teamviewer、Wayknow等等。因为VNCViewer其实是树莓派自带的，只不过默认不开启，所以我先使用VNC进行桌面连接。后边会安装Teamviewer（需要桌面环境进行配置），Teamviewer最大的好处是可以外网访问。

#### 2.5.1 VNC连接

首先在设置里开启VNC

![1562512207843](/images/1562512207843.png)

![1562512244640](/images/1562512244640.png)

打开电脑上的VNC viewer软件，连接树莓派

![1562512484266](/images/1562512484266.png)

![1562512529252](/images/1562512529252.png)

现在成功登录树莓派桌面！（我在boot分区显示设置中设置了竖屏，所以是下图这样）

![1562512581846](/images/1562512581846.png)

接下来跟着系统引导设置一些地区之类无关痛痒的选项，略过。Wifi设置在桌面环境下非常容易，也略过。

#### 2.5.2 Teamviwer连接

比较好的一篇文章：<https://www.cnblogs.com/trilobita/p/10506094.html>；<https://blog.csdn.net/cungudafa/article/details/84495472>

由于我习惯了桌面配置，所以直接安装好再桌面设置就好了，不多说。

### 2.6 静态IP配置文件修改法

```shell
sudo nano /etc/dhcpcd.conf
```

![1562513217988](/images/1562513217988.png)

添加内容。另外注意配置时，静态IP一定要和路由器网段一致，比如：假设路由器的IP为 192.168.0.x 网段，则上面的 static ip_address 就要对应的修改为 192.168.0.x/24 。还有一点就是手动静态IP要注意不能跟路由器 DHCP 所自动分配的 IP 冲突，否则树莓派就有可能无法正常联网。

因为我是通过笔记本Wlan共享给树莓派所连接的有线网卡，所以这里我应该设置为192.168.137.x网段

```shell
interface eth0
static ip_address=192.168.137.13/24
static routers=192.168.137.1
static domain_name_servers=8.8.8.8 114.114.114.114
```

保存后重启

```shell
sudo reboot
```

重新查看下树莓派的IP：

![1562513912458](/images/1562513912458.png)

奇怪的是，192.168.137.169现在变成真正的静态IP了，不管了，现在不追究这些细节。

还可以参考这篇：<https://blog.csdn.net/Jack_Lue/article/details/82912327>

## 3. 安装MagicMirror魔镜

### 3.1 魔镜看法

​魔镜的本质只是一个全屏的WEB网页，而魔镜项目做成了Node.js的一个模块。因此安装魔镜实际上是安装Node.js并在其上安装魔镜网页并同时启动服务端和客户端。

### 3.2 安装

安装直接按照其Github上README进行即可。搬运过来：

1. Download and install the latest *Node.js* version:

- `curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -`
- `sudo apt install -y nodejs`

1. Clone the repository and check out the master branch: `git clone https://github.com/MichMich/MagicMirror`
2. Enter the repository: `cd MagicMirror/`
3. Install and run the app with: `npm install && npm start`
   For **Server Only** use: `npm install && node serveronly` .

**⚠️ Important!**

- **The installation step for npm install will take a very long time**, often with little or no terminal response!
  For the RPi3 this is **~10** minutes and for the Rpi2 **~25** minutes.
  Do not interrupt or you risk getting a 💔 by Raspberry Jam.

Also note that:

- `npm start` does **not** work via SSH. But you can use `DISPLAY=:0 nohup npm start &` instead.
  This starts the mirror on the remote display.
- If you want to debug on Raspberry Pi you can use `npm start dev` which will start MM with *Dev Tools* enabled.
- To access toolbar menu when in mirror mode, hit `ALT` key.
- To toggle the (web) `Developer Tools` from mirror mode, use `CTRL-SHIFT-I` or `ALT` and select `View`.

### 3.3 配置

执行安装步骤最后一步``npm install && npm start`之后，会显示魔镜默认页面

![1562585803263](/images/1562585803263.png)

显然，需要创建一个所谓的config文件。

阅读了一下魔镜项目README，需要配置的还挺多的。

#### 3.3.1 树莓派系统上的一些设置

<https://github.com/MichMich/MagicMirror/wiki/Configuring-the-Raspberry-Pi>

我在这里只做了开启OpenGL和安装鼠标自动隐藏插件两步。其他设置如自启动等等软件方面弄完了部署时再做。

![1562586332912](/images/1562586332912.png)

开启OpenGL后config.txt所设置的竖屏就会失效，此时采用

```shell
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
添加：
@xrandr --output HDMI-1 --rotate right
```

![1562586962628](/images/1562586962628.png)

重启后，画面仍没有转为右向（竖屏），先不管，后边再补充。

接下来设置鼠标自动隐藏：

```shell
安装unclutter:
sudo apt-get install unclutter

配置unclutter自启动
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
添加：
@xrandr --output HDMI-1 --rotate right
```

![1562587357247](/images/1562587357247.png)

//TODO:屏幕休眠、屏保等问题待解决。

#### 3.3.2 设置魔镜自启动

<https://github.com/MichMich/MagicMirror/wiki/Auto-Starting-MagicMirror>

#### 3.3.3 设置魔镜配置文件

<https://github.com/MichMich/MagicMirror>

1. Copy `/home/pi/MagicMirror/config/config.js.sample` to `/home/pi/MagicMirror/config/config.js`.
   **Note:** If you used the installer script. This step is already done for you.

2. Modify your required settings.
   Note: You can check your configuration running `npm run config:check` in `/home/pi/MagicMirror`.

   暂时不作自定义，使用默认配置文件。

   重新启动魔镜（魔镜退出方法：Ctrl+Q或者Alt调起菜单再在File下列选项中选择q）

   ```shell
   cd  /home/pi/MagicMirror
   npm start
   ```

   浏览器访问localhost：8080、127.0.0.1:8080(从树莓派上访问)、192.168.137.169:8080（从电脑上访问）

   显示结果如下：

   ![1562588302582](/images/1562588302582.png)

看上去有些地方水土不服需要修改。

#### 3.3.4 魔镜模块配置

![1562588892711](/images/1562588892711.png)

#### 3.3.5 魔镜第三方模块

<https://github.com/MichMich/MagicMirror/wiki/3rd-party-modules>

#### 3.3.6 魔镜第三方模块开发

<https://github.com/MichMich/MagicMirror/tree/master/modules>

## 4. 安装悟空语音助手

<https://wukong.hahack.com/#/install>

//TODO:更新唤醒词






















