---
title: "openwrt路由器折腾记"
date: 2020-09-05T10:57:06+08:00
draft: false
categories: ["linux"]
tags: ["路由器","openwrt"]
keywords: ["openwrt", "路由器", "华工校园网"]
---

以下介绍折腾宿舍校园网的过程

## 1. 宿舍校园网开wifi

1. 华工宿舍校园网屏蔽了路由器。其实是所使用的认证方式（DRCOM）不支持路由器。
2. DRCOM具体是啥，不管，反正理解为一个账号服务器就行了，因此要想上网，必需有个基于DRCOM里边协议的客户端，来支持拨号
3. [scutclient](https://github.com/forward619/scutclient)这个项目使用C语言实现了一个openwrt系统可用的客户端。源码见其`src`目录。但是编译还是比较麻烦的，比较省事的是直接加该仓库提供的路由群，去获取`.ipk`程序文件
4. 为什么一般的路由器不行，非得是刷openwrt系统的路由器？因为一般的路由器系统都是深度定制，无法自行安装第三方软件（也就是我们前边准备好的scutclient程序）；而且大多数新路由器产品配置上缩水，无法运行完整openwrt系统；openwrt是开源的路由器系统，可以将之看作一个定制的linux系统，将合适的路由器设备刷入openwrt后，我们就可以自行安装第三方软件，用来实现校园网拨号


## 2. 操作流程

1. 准备好刷好官方原版openwrt系统的路由器设备（最好安装了luci界面）、scutclient.ipk(安装文件)、putty或者其他支持SSH的软件(用于SSH登录路由器)、winscp（用于将程序安装文件拷贝到路由器）
2. 路由器设备上会提示默认网关是多少（默认网关其实相当于路由器的ip）。我的是192.168.1.1
3. 首先连接路由器开启的wifi（如果路由器不能开启wifi，也可以网线连接电脑和路由器LAN口，记得要把电脑有线网卡ipv4设为192.168.1.2或其他该子网下有效ip）。 浏览器输入192.168.1.1，进入web界面， 以(用户名root密码admin)登录，修改默认的root密码（假设修改为123456），不然的话路由器默认不允许SSH登录。接下去，可以使用该账号密码SSH登录到192.168.1.1，也就是路由器设备
4. 使用`cat /etc/openwrt_release`查看路由器的openwrt系统信息。
```shell
root@OpenWrt:/etc/init.d# cat /etc/openwrt_release
DISTRIB_ID="OpenWrt"
DISTRIB_RELEASE="14.07"
DISTRIB_REVISION="r42625"
DISTRIB_CODENAME="barrier_breaker"
DISTRIB_TARGET="ar71xx/generic"
DISTRIB_DESCRIPTION="OpenWrt Barrier Breaker 14.07"
DISTRIB_TAINTS=""

```
架构是`ar71xx/generic`，系统版本是`14.07`，代号是`barrier_breaker`（俗称BB）

接下去所有需要安装的软件必需匹配这个版本

5. 使用winscp将windows本地下载好的scutclient.ipk文件上传到路由器，再在路由器上`opkg install scutclient.ipk`。 默认安装在`/usr/bin`下

使用`scutclient 校园网账号 校园网密码`，观察控制台输出，可以看出是否拨号成功

6. 设置路由器的网络接口（wan口配置和wifi配置）。可以按网上介绍的通过uci来配置，也可以直接编辑配置文件`/etc/config/network`，在其中添加或修改为如下：

```
config interface 'wan'
        option ifname 'eth1'
        option proto 'static'
        option ipaddr '211.66.15.181'
        option netmask '255.255.255.0'
        option gateway '211.66.15.254'
        option macaddr '00:40:5A:E1:E0:33'
        option dns '202.112.17.33 114.114.114.114'
```

主要配置项：静态IP(proto)、外网IP地址(ipaddr，学校分配)、子网掩码(netmask，255.255.255.0)、网关地址(gateway，开通校园网会知道)、设备物理地址(macaddr，填路由器mac就好)、DNS地址（亲测必须填学校的DNS：202.112.17.33）

wifi配置则是编辑`/etc/config/wireless`，在其中配置：

```
config wifi-iface
        option device 'radio0'
        option network 'lan'
        option mode 'ap'
        option ssid 'W6-244'
        option encryption 'psk2'
        option key 'scut2018'

```
其实就是配置wifi名(ssid)、加密方式(encryption)、密码(key)三项

配置完重启以生效

7. 现在连接wifi能够上网了，但是发现路由器频繁掉线，经过观察，发现是scutclient启动之后过不了多久就被杀掉进程。查看路由器内存使用量，发现还有许多剩余，因此不太可能是因为OOM而被杀掉，因此不知道是为什么被杀掉。

不管怎么样，既然是进程被杀掉导致掉线（scutclient在拨号成功之后需要定期发送心跳包到校园网认证服务器，不然就掉线了），scutclient在启动时是`scutclient 账户 密码 &`后台形式运行，（关于前台进程、后台进程、守护进程三者自行百度）还是有可能因为捕捉SIGHUP信号而被挂起最后被杀掉，最简单的办法就是使用`nohup`来忽略SIGHUP信号
```
nohup scutclient 账户 密码 &
```

openwrt上默认没有nohup，需要从其软件包仓库下载：

```shell
注意版本号要对应，不然可能会安装失败。

# 先安装coreutils
wget http://archive.openwrt.org/barrier_breaker/14.07/ar71xx/generic/packages/packages/coreutils_8.16-1_ar71xx.ipk

opkg install coreutilsxxxxxxxx.ipk

# 再安装coreutils-nohup
wget http://archive.openwrt.org/barrier_breaker/14.07/ar71xx/generic/packages/packages/coreutils-nohup_8.16-1_ar71xx.ipk

opkg install coreutils-nohupxxxxxxxx.ipk
```

安装时若提示类似`default_postinst: not found`之类的错误，这是因为没有提供postinst(post-install安装后脚本，用于安装程序之后干什么)，既然软件自己没有提供，那么我们需要指定默认的postinst，需要在`/lib/functions.sh`中添加脚本内容如下：

```shell
default_postinst() {
        return 0
}
```

这样安装就不会报错了

如果遇到安装后执行`./nohup`提示找不到命令，说明可能装错了版本，不同版本的nohup依赖的libc不同，安装正确版本就可以


8. 最后，还需要设置开机自启动。创建`/etc/init.d/scutclient`文件（关于/etc/init.d，这是较老的linux系统用来配置开机启动的服务的目录，开机时会启动init.d下的所有服务。目前一般都用systemd来管理，不过openwrt没有预装systemd）。编辑内容如下，意思应该很容易懂：

```shell
#!/bin/sh /etc/rc.common

START=99
STOP=15


auth() {

        # 这里的uci get xxxx 是说在/etc/config/下有scutclient这个配置文件，$(uci get xxxx) 是从配置文件中读配置
        # -eq 就是 不等于
        if [ $(uci get scutclient.@option[0].enable) -eq 0 ]
        then
                exit
        fi

        # 这里是重点，这样scutclient就会开机启动并且不会被杀掉进程
        nohup scutclient $(uci get scutclient.@scutclient[0].username) $(uci get scutclient.@scutclient[0].password) $(uci get network.wan.ifname) &

}

start() {
        if [ $(uci get scutclient.@option[0].enable) -eq 0 ]
        then
                exit
        fi
        sleep $(uci get scutclient.@drcom[0].delay)
        auth
}

stop() {
        if [[ $(ps | grep 'scutclient' | wc -l) -eq 0 ]] ;then
                killall scutclient
        fi
}

restart() {
        stop
        auth
}


```

9. 剩下还有就是可以去配置下路由器的时区，同步下局域网内浏览器时间。这个luci界面有指引

## 最后

```
链接：https://pan.baidu.com/s/1gHGlEeXmjSJl_oI3WVALAA 
提取码：u7u7
```