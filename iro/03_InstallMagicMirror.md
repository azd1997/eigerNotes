---
title: "安装MagicMirror2"
date: 2019-07-23T10:37:14+08:00
draft: false
categories: ["Iro", "树莓派"]
tags: ["Iro", "树莓派", "魔镜"]
keywords: ["Iro", "树莓派", "魔镜"]

---

### 1. 魔镜看法

​		魔镜的本质只是一个全屏的WEB网页，而魔镜项目做成了Node.js的一个模块。因此安装魔镜实际上是安装Node.js并在其上安装魔镜网页并同时启动服务端和客户端。

### 2. 安装

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

### 3. 配置

执行安装步骤最后一步``npm install && npm start`之后，会显示魔镜默认页面

![1562585803263](/images/1562585803263.png)

显然，需要创建一个所谓的config文件。

阅读了一下魔镜项目README，需要配置的还挺多的。

#### 3.1 树莓派系统上的一些设置

https://github.com/MichMich/MagicMirror/wiki/Configuring-the-Raspberry-Pi

我在这里只做了开启OpenGL和安装鼠标自动隐藏插件两步。其他设置如自启动等等软件方面弄完了部署时再做。

![1562586332912](C:\Users\37419\AppData\Roaming\Typora\typora-user-images\1562586332912.png)

开启OpenGL后config.txt所设置的竖屏就会失效，此时采用

```
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
添加：
@xrandr --output HDMI-1 --rotate right
```

![1562586962628](/images/1562586962628.png)

重启后，画面仍没有转为右向（竖屏），先不管，后边再补充。

接下来设置鼠标自动隐藏：

```
安装unclutter:
sudo apt-get install unclutter

配置unclutter自启动
sudo nano /etc/xdg/lxsession/LXDE-pi/autostart
添加：
@xrandr --output HDMI-1 --rotate right
```

![1562587357247](/images/1562587357247.png)

//TODO:屏幕休眠、屏保等问题待解决。

#### 3.2 设置魔镜自启动

https://github.com/MichMich/MagicMirror/wiki/Auto-Starting-MagicMirror

#### 3.3 设置魔镜配置文件

https://github.com/MichMich/MagicMirror

1. Copy `/home/pi/MagicMirror/config/config.js.sample` to `/home/pi/MagicMirror/config/config.js`. 
   **Note:** If you used the installer script. This step is already done for you.

2. Modify your required settings. 
   Note: You can check your configuration running `npm run config:check` in `/home/pi/MagicMirror`.

   暂时不作自定义，使用默认配置文件。

   重新启动魔镜（魔镜退出方法：Ctrl+Q或者Alt调起菜单再在File下列选项中选择q）

   ```
   cd  /home/pi/MagicMirror
   npm start
   ```

   浏览器访问localhost：8080、127.0.0.1:8080(从树莓派上访问)、192.168.137.169:8080（从电脑上访问）

   显示结果如下：

   ![1562588302582](/images/1562588302582.png)

看上去有些地方水土不服需要修改。

#### 3.4 魔镜模块配置

![1562588892711](/images/1562588892711.png)

#### 3.5 魔镜第三方模块

https://github.com/MichMich/MagicMirror/wiki/3rd-party-modules

#### 3.6 魔镜第三方模块开发

https://github.com/MichMich/MagicMirror/tree/master/modules