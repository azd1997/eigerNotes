---
title: "安装Ubuntu及其他相关使用教程"
date: 2019-08-17T02:04:01+08:00
draft: false
categories: ["linux"]
tags: ["Ubuntu"]
keywords: ["Ubuntu", "Go", "VS Code", "Hugo"]
---

# 通过U盘安装Ubuntu

## 1. 安装

准备好ubuntu镜像文件、rufus(用于制作启动盘，也可以选择别的类似的工具，如UltraISO)、空U盘、用于安装ubuntu的笔记本电脑。

制作好启动盘后，将U盘插上笔记本，重启电脑，在启动时狂按F1键（我用的是thinkpad，其他电脑百度进入BIOS方法即可）听到叮的一声就可以了，接下来会进入BIOS界面，找到BOOT选项，进入之后将U盘启动移至第一选项，保存更改并退出，电脑继续启动，并进入ubuntu安装界面。

具体安装没有特别情况，一路安装即可。

## 2.更换软件源

在进行系统更新时发现镜像拉取失败，是时候更换新的软件源了。国内的话有阿里云、清华、华科等ubuntu镜像源比较常用。

**换源的方法**：
编辑`/etc/apt/sources.list`。

1. 首先先备份下默认的source.list文件：

    ```shell
    cd /etc/apt
    sudo cp sources.list sources.list.old
    ```

    (ps: .old后缀无所谓，改成别的也可以)

2. 进入编辑状态：

    ```shell
    sudo nano sources.list
    ```

    清空原本内容，添加以下内容:

    ```shell
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-security main restricted universe multiverse
    ```

    （ps: deb-src为源码仓库，自行根据需求添加，后边http:xxxx是一样的，例如：deb-src http:......）
    执行CTRL+O保存并CTRL+X退出。

3. 更新软件源、升级系统

    ```shell
    sudo apt update
    sudo apt upgrade
    ```

## 3.安装搜狗中文输入法

1. 进入搜狗输入法官网下载搜狗拼音输入法linux版并安装，完成后重启电脑。
2. 进入Fcitx Configuation软件，点击“+”号，并取消“Only Show Current Language”选项，在搜索栏搜索“Sougo”，界面显示搜狗选项条，选中后点击“OK”

此时使用CTRL+SPACE可以进行英文输入法和搜狗拼音输入法的切换。在使用搜狗输入法的过程中也可以按shift切换搜狗输入法的中英文。

## 4. 安装go语言开发环境

进入<https://studygolang.com>,找到下载页面，下载go1.12.7.linux-amd64.tar.gz，解压至`/usr/local/`目录下，并设置相关的环境变量：

编辑`/etc/profile`或者`$HOME/.profile`（编辑前者其更改全局有效，编辑后者仅当前用户生效，和windows下系统变量、用户变量的关系类似），在文件的末尾追加如下内容：

```shell
# go environment
export GOROOT=/usr/local/go
export GOPATH=/home/eiger/gopath-default
export GOBIN=$GOPATH/bin
export GO111MODULE=on
export GOPROXY=https://goproxy.io
export PATH=$PATH:$GOROOT/bin
export PATH=$PATH:$GOPATH/bin
```

保存退出后使用source /etc/profile使之生效。

go命令教程： <http://wiki.jikexueyuan.com/project/go-command-tutorial/>

## 5.安装远程连接工具

远程连接工具有VNC、Teamviewer、AnyDesk、向日葵等。其中Teamviewer、AnyDesk、向日葵都是免费的可外网访问的远程控制工具。这里选择安装AnyDesk。理由是Teamviewer我已经添加了很多设备，担心被商用警告，而向日葵连接起来很卡，折中选择了AnyDesk。

安装方法：搜索AnyDesk进入其官网 <https://anydesk.com/en/downloads/linux> ，选择Ubuntu适用版本下载。并直接通过安装器安装即可。

为了实现无人值守（也就是可以在远处通过网络直接访问这台电脑而不用经过同意），需要设置无人值守密码。其他用法很简单，略过。

## 6. 设置环境变量

参见 <https://www.linuxidc.com/Linux/2016-09/135476.htm>

一般情况下设置环境变量和查看环境变量，参考 <https://blog.csdn.net/white_idiot/article/details/78253004>

- 设置/etc/profile一劳永逸：）
- source /etc/profile使其生效
- env、export可用来查看所有环境变量

（ps: 解释下export PATH=${JAVA_HOME}/bin:$PATH，:号是PATH变量中值的分隔符，这样写说明将${JAVA_HOME}/bin置于PATH变量的第一个值，而export PATH=$PATH:${JAVA_HOME}/bin则相反）

## 7. 查找程序所在位置

参见 <https://blog.csdn.net/baidu_38172402/article/details/80795553>

- 使用dpkg install或者apt install安装的，使用`sudo dpkg -L APPNAME`可以列出程序的所有安装文件路径
- 自动安装在PATH下的程序（也就是可以用APPNAME --version或类似方式判断安装成功与否的），可直接使用`whereis APPNAME`

博文中还提及了其他的查询程序位置的方法，按需选择。

## 8. 安装VS Code

虽然Ubuntu自带的gedit文本编辑器我觉得挺清爽也挺好用的，但考虑到没啥插件支持，写较长文档时来回翻找很痛苦，以及后期代码开发，于是决定安装一款比较好用的代码编辑器兼文档编辑器：VS Code。

下载地址： <https://code.visualstudio.com/Download>
选择linux版本的.tar.gz压缩包，还是自己手动安装，方便自己知道安装去哪了。

下载解压后复制到/usr/local目录下，设置环境变量`export PATH=$PATH:/usr/local/VSCode-linux-x64/bin`后`source /etc/profile`。

现在在终端输入code即可打开VSCode了。

### 8.1. 关于vscode插件

如有需要，参考：

- <https://www.jianshu.com/p/9f13e971fe6b>
- <https://zhuanlan.zhihu.com/p/29553584>

先装几个看上去很不错的插件：

- Bookmarks
- GitLens
- Markdown PDF

    转pdf插件，需要chromium，会自动帮你下载，在VSCode底部可看到提示。但我自动安装失败，选择手动安装chrome。之后需要设置markdownpdf的一些设置。具体参看<https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf>
    （VSCode上没法同时进行设置和查阅markdownpdf）。

    markdown-pdf的设置（编辑/home/eiger/.config/Code/User/settings.json）：

    ```json
    // markdowm-pdf设置
    "markdown-pdf.outputDirectoryRelativePathFile": true,
    "markdown-pdf.outputDirectory": ".",
    "markdown-pdf.executablePath": "/usr/bin/google-chrome",
    "markdown-pdf.displayHeaderFooter": true,
    "markdown-pdf.footerTemplate": "<div style=\"font-size: 9px; margin: 0 auto;\"> <span class='pageNumber'></span> / <span class='totalPages'></span></div>",
    // 这里添加了“ by Eiger ”头部
    "markdown-pdf.headerTemplate": "<div style=\"font-size: 9px; margin-left: 1cm;\"> <span class='title'></span> <span> by Eiger</span> </div> <div style=\"font-size: 9px; margin-left: auto; margin-right: 1cm; \"> <span class='date'></span></div>",
    ```

- vscode-pdf
- Markdown All in One
- Markdown Preview Github Styling

### 8.2. vscode一些设置

- 取消代码右边栏缩略图。本来屏幕就小，太占位置了这个缩略图，直接在view->show minimap一项取消选中即可。
- 文件大纲视图。在左边栏Explorer菜单的底部点击OUTLINE并自行拖动调整区域大小。
- 自动清除行末空格。设置中开启Files.TrimTrailingWhitespace。重启VSCode后生效。

settings.json内容：

```json
// 文本编辑器设置
"editor.minimap.enabled": false,    // 关闭minimap
"editor.fontSize": 16,
// 文件设置
"files.autoSave": "afterDelay",
"files.trimTrailingWhitespace": true,   // 保存文件时自动剪去行末空格
// markdowm-pdf设置
"markdown-pdf.outputDirectoryRelativePathFile": true,
"markdown-pdf.outputDirectory": ".",
"markdown-pdf.executablePath": "/usr/bin/google-chrome",
"markdown-pdf.displayHeaderFooter": true,
"markdown-pdf.footerTemplate": "<div style=\"font-size: 9px; margin: 0 auto;\"> <span class='pageNumber'></span> / <span class='totalPages'></span></div>",
"markdown-pdf.headerTemplate": "<div style=\"font-size: 9px; margin-left: 1cm;\"> <span class='title'></span> <span> by Eiger</span> </div> <div style=\"font-size: 9px; margin-left: auto; margin-right: 1cm; \"> <span class='date'></span></div>",
"markdown-pdf.highlightStyle": "github.css",    // 设置高亮风格为github.css
// 文件浏览器窗口删除文件不再弹窗提醒
"explorer.confirmDelete": false
// go设置
"go.useLanguageServer": true,
"go.autocompleteUnimportedPackages": true,
"go.useCodeSnippetsOnFunctionSuggest": true
```

### 8.3. 代码片段定义

File -> Preferences -> User Snippets，可以定义各种文件中的代码片段。

用法参考其官方注释，及博文：

- <https://blog.csdn.net/maokelong95/article/details/54379046>
- <https://segmentfault.com/a/1190000018457312>

我的go代码片段：

```json
"eiger-go": {
    "prefix": "go",
    "body": [
        "//@author: Eiger <374192922@qq.com>",
        "//@date: $CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
        "//@description: This file is for $0",
        "",
        "package $TM_DIRECTORY",
        "",
        "",
        "func main() {",
        "    // write your code here",
        "$1",
        "}",
        ""
    ],
    "description": "eiger's go file template"
}
```

markdown代码片段定义及使用，参考：<https://blog.walterlv.com/post/add-custom-code-snippet-for-vscode.html>

我的markdown代码片段：

```json
"hugo-blog-header": {
    "prefix": "hugo",
    "body": [
        "---",
        "title: \"$1\"",
        // 插入片段后，记得将字母T前边空格删去！
        "date: $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE T$CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND+08:00",
        "draft: false",
        // category请设为所在目录名
        "categories: [\"$2\"]",
        "tags: [\"$3\"]",
        "keywords: [\"$4\", \"$5\"]",
        "---",
        ""
    ],
    "description": "Header of hugo blog"
}
```

## 9. 文件（夹）权限查看及修改

参考： <https://blog.csdn.net/lwc5411117/article/details/79417102>

- 查看文件（夹）权限：

    ```shell
    ls -l FILENAME
    # 或者
    ll FILENAME
    ```

- 修改文件（夹）权限：

    ```shell
    # 形如：
    sudo chmod u+x FILE  # 为当前用户提升FILE执行权限
    sudo chmod a+w FOLDER  # 为所有用户提升FOLEDER文件夹写权限（不影响文件夹内子文件夹和文件）
    sudo chmod -R a+w FOLDER  # 递归修改，影响文件夹内所有内容
    ```

## 10. 查看本机端口使用情况及关闭使用端口的程序

博文参考：

- <https://www.cnblogs.com/wangtao1993/p/6144183.html>
- <https://www.jianshu.com/p/acb085a8f1cb>

1. 使用nmap查看本机ip:127.0.0.1的已使用端口：

    ```shell
    nmap 127.0.0.1
    ```

2. lsof -i:PORT 查看PORT端口的占用情况：

    ```shell
    lsof -i:8000
    ```

    (ps: `lsof -i` 列举所有端口占用情况)

3. netstat -ap | grep PORT 查看PORT端口的占用情况：

    ```shell
    netstat -ap | grep 8000
    ```

    (ps: `netstat -ap` 列举所有端口情况； `netstat -a` 列举所有已建立连接的端口使用情况)

- 关闭使用PORT端口的程序

    ```shell
    kill -9 PID  # 使用PORT端口的进程，其PID号
    ```

ps: 提示权限不足时，前边加上`sudo`

## 11. 安装chrome

在安装VSCode的MarkdownPDF插件时，需要安装chrome，由于自动下载失败，所以只好手动安装chrome浏览器并将其执行路径设置到MarkdownPDF设置中的markdown-pdf.executablePath。

安装参考： <https://blog.csdn.net/zl2001bc/article/details/83275309>

安装步骤如下：

1. 添加chrome下载源到apt 仓库

    ```shell
    sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/
    ```

    如果返回地址解析错误，可以搜索其他提供chrome下载的源，替换掉命令中的地址

2. 导入google 的公钥

    ```shell
    wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
    ```

3. 更新当前系统的可用列表

    ```shell
    sudo apt-get update
    ```

4. 安装chrome 稳定版

    ```shell
    sudo apt-get install google-chrome-stable
    ```

安装过程如下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/
--2019-08-14 03:37:00--  http://www.linuxidc.com/files/repo/google-chrome.list
Resolving www.linuxidc.com (www.linuxidc.com)... 106.119.182.141, 113.107.238.213
Connecting to www.linuxidc.com (www.linuxidc.com)|106.119.182.141|:80... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://www.linuxidc.com/files/repo/google-chrome.list [following]
--2019-08-14 03:37:00--  https://www.linuxidc.com/files/repo/google-chrome.list
Connecting to www.linuxidc.com (www.linuxidc.com)|106.119.182.141|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 131 [application/octet-stream]
Saving to: ‘/etc/apt/sources.list.d/google-chrome.list’

google-chrome.list                           100%[==============================================================================================>]     131  --.-KB/s    in 0s

2019-08-14 03:37:00 (7.54 MB/s) - ‘/etc/apt/sources.list.d/google-chrome.list’ saved [131/131]

eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
OK
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo apt update
Hit:1 http://archive.ubuntukylin.com:10006/ubuntukylin xenial InRelease
Hit:2 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic InRelease
Hit:3 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates InRelease
Hit:4 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-backports InRelease
Hit:5 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-security InRelease
Ign:6 https://dl.google.com/linux/chrome/deb stable InRelease
Hit:7 https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu bionic InRelease
Get:8 https://dl.google.com/linux/chrome/deb stable Release [943 B]
Get:9 https://dl.google.com/linux/chrome/deb stable Release.gpg [819 B]
Get:10 https://dl.google.com/linux/chrome/deb stable/main amd64 Packages [1,109 B]
Ign:11 https://repo.fdzh.org/chrome/deb stable InRelease
Get:12 https://repo.fdzh.org/chrome/deb stable Release [1,189 B]
Get:13 https://repo.fdzh.org/chrome/deb stable Release.gpg [819 B]
Ign:13 https://repo.fdzh.org/chrome/deb stable Release.gpg
Reading package lists... Done
W: GPG error: https://repo.fdzh.org/chrome/deb stable Release: The following signatures were invalid: EXPKEYSIG 1397BC53640DB551 Google Inc. (Linux Packages Signing Authority) <linux-packages-keymaster@google.com>
E: The repository 'https://repo.fdzh.org/chrome/deb stable Release' is not signed.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo apt install google-chrome-stable
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  google-chrome-stable
0 upgraded, 1 newly installed, 0 to remove and 17 not upgraded.
Need to get 59.5 MB of archives.
After this operation, 210 MB of additional disk space will be used.
Get:1 https://dl.google.com/linux/chrome/deb stable/main amd64 google-chrome-stable amd64 76.0.3809.100-1 [59.5 MB]
Fetched 59.5 MB in 1min 2s (953 kB/s)
Selecting previously unselected package google-chrome-stable.
(Reading database ... 176803 files and directories currently installed.)
Preparing to unpack .../google-chrome-stable_76.0.3809.100-1_amd64.deb ...
Unpacking google-chrome-stable (76.0.3809.100-1) ...
Processing triggers for mime-support (3.60ubuntu1) ...
Processing triggers for desktop-file-utils (0.23-1ubuntu3.18.04.2) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Processing triggers for gnome-menus (3.13.3-11ubuntu1.1) ...
Setting up google-chrome-stable (76.0.3809.100-1) ...
update-alternatives: using /usr/bin/google-chrome-stable to provide /usr/bin/x-www-browser (x-www-browser) in auto mode
update-alternatives: using /usr/bin/google-chrome-stable to provide /usr/bin/gnome-www-browser (gnome-www-browser) in auto mode
update-alternatives: using /usr/bin/google-chrome-stable to provide /usr/bin/google-chrome (google-chrome) in auto mode
```

chrome安装后提供了两个快捷方式：/usr/bin/google-chrome、/usr/bin/google-chrome-stable。因此在为markdownpdf提供chrome执行路径直接选择二者之一即可。

如果安装完打开chrome发生闪退，解决方法参见上面提到的博文

## 12. 安装Hugo

以前在Windows笔记本上安装了hugo并部署了个人博客： <http://eiger.me>，现在开始用乌班图正式进行一些开发与学习，将笔记文档复制来复制去觉得太麻烦，于是考虑在乌班图上也装hugo并将原博客项目拉取至此（主要方法就是将原项目上传至Github并拉取到乌班图笔记本，这里就不详述）

hugo的官方安装使用教程： <https://www.gohugo.org/>

关于如何基于Hugo和Github Pages部署静态博客网站，参考：

- <https://creaink.github.io/post/Devtools/Hugo/Hugo-intro.html>

这里展示下我用于笔记、博客、主题同步的四个项目：

- 博客总项目： <https://github.com/azd1997/EigerBlog>
- 博客网站项目： <https://github.com/azd1997/azd1997.github.io>
- 博客笔记项目： <https://github.com/azd1997/eigerNotes>
- 博客主题Even项目： <https://github.com/azd1997/hugo-theme-even>

关于Git submodule使用，参考：

- <https://jianshu.com/p/f8a55b972972>

安装hugo：

```shell
sudo apt install hugo
```

安装路径为`/usr/bin/hugo`。

但是目前的hugo apt源最新是v0.40.1，而使用主题even至少需要v0.50，所以说我需要使用`sudo apt --purge remove hugo`完全删除hugo，然后手动去hugo github releases页面（<https://github.com/gohugoio/hugo/releases>）下载最新版本，我下载的是v0.57.1的linux-64bit.deb包。

安装后查看下信息：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/EigerBlog/themes$ whereis hugo
hugo: /usr/local/bin/hugo
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/EigerBlog/themes$ hugo version
Hugo Static Site Generator v0.57.1-58C56E9D linux/amd64 BuildDate: 2019-08-15T18:53:44Z
```

列出常用操作：

```shell
# 生成新站点
hugo new site /path/to/site
# 切换到站点目录，后续操作需在站点根目录下执行
cd /path/to/site
# 生成新md文档
hogo new about.md  # site/content/about.md
hugo new post/mynote.md  # site/content/post/mynote.md
# 本地调试，主题为even，草稿文档也参与调试
hugo server --theme=even --buildDrafts
# 当config.toml进行了相应设置指定了主题之后，可以这样调试：
hugo server -D
# 生成website网站文件，生成位置site/public
hugo --theme=even
# 当设置好GitPages后，将public下所有内容推到远程GitPage项目即可。
# 我的做法是创建一个website目录，每次重新生成网站文件就全部复制到website下，再从website推给Github。为什么这么做，因为我有.git和CNAME不可被删除，而hugo会自动将public原内容清除再生成新的网站。
```

附上我的站点目录：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/EigerBlog$ ls
archetypes   content  layouts   mycustom  resources  themes
config.toml  data     Makefile  public    static     website
```

因为在website目录下有`.git`文件夹和`CNAME`文件是不能被删除的，这导致每次从public文件夹下复制到website时不能直接全选粘贴，这其实有些麻烦。

为此，直接先写一个Makefile文件(参考[Makefile编写](#13-makefile%e7%bc%96%e5%86%99))将所有命令整合一下：

```Makefile
# `make`
default:
        # 生成网站文件
        hugo;
        @echo '网站文件生成在public/下';
        # 临时保存.git与CNAME
        cp -fp website/CNAME mycustom/tmp/;
        cp -rpf website/.git mycustom/tmp/;
        @echo 'CNAME、.git已备份至mycustom/tmp/下';
        pwd;
        # 清空原website文件，将所需文件复制入website
        cd website;pwd;rm -rf *;cd ../;
        @echo 'website下内容已清空';
        cp -rf public/. website;
        cp -rpf mycustom/tmp/.git website/;
        cp -fp mycustom/tmp/CNAME website/;
        @echo 'website文件已全部生成';
        pwd;
        # now=`date`;\
        # echo "make love $${now}";
        # git提交至远程仓库，更新博客
        # CUR_DATE = $(shell date '+%Y-%m-%d');
        @date=`date +%Y-%m-%d`;\
                echo "$${date}";\
                cd website/;pwd;\
                git add .;\
                git commit -m "$${date}  update my blog site:  https://eiger.me";\
                git push;
        # 注意：必须是双引号括起字符串，双$加花括号包住变量
        pwd;
        @echo '博客网站https://eiger.me更新完成';
```

当前站点根目录下结构：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Desktop/EigerBlog$ ls -a
.   archetypes   content  .git         layouts   mycustom  resources  themes
..  config.toml  data     .gitmodules  Makefile  public    static     website
```

现在只需在根目录下执行`make`即可完成博客更新操作。

## 13. Makefile编写

推荐参考阮一峰博客：

- Makefile教程： <http://ruanyifeng.com/blog/2015/02/make.html>
- make构建网站： <http://www.ruanyifeng.com/blog/2015/03/build-website-with-make.html>

## 14. apt卸载程序

参考： <https://blog.csdn.net/get_set/article/details/51276609>

```shell
sudo apt --purge remove APPNAME
```

## 15. 复制与删除

```shell
# 复制单个文件夹到目标文件夹
cp file dir
# 将文件内容复制到另一文件中
cp file1 file2
# 复制文件夹及其下所有内容到目标文件夹
cp -r dir1 dir2
# 复制文件夹下所有文件到目标文件夹
cp -r dir1/. dir2
# 强制删除单个文件
rm -f file
# 强制删除当前目录所有内容
rm -rf *
```

## 16. git查看用户名

```shell
# 查看用户名及邮箱
git config user.name
git config user.email
# 设置用户名及邮箱
git
```

## 17. 安装qq、微信等程序

参考：<https://blog.csdn.net/qq_25987491/article/details/81364461>


