---
title: "edgeX安装使用全系列"
date: 2019-08-21T01:54:34+08:00
draft: false
categories: ["iot"]
tags: ["edgeX"]
keywords: ["edgeX", "go"]
---

# edgeX安装使用全系列

## 1. 开发者篇

### 1.1 介绍

EdgeX Foundry是一组为边缘网关平台提供的微服务集合，还包含了一系列的SDK。主要的SDK编写语言为go或者c，还有少数微服务因为历史遗留原因由java编写（edgeX一开始是由java编写的）。所以想要运行或者开发某个微服务时，需要有对应开发语言的开发环境。

本文只讲go开发，也就是说本文只涉及基于go编写的edgeX微服务。

### 1.2 前置需求

- 硬件：不提，一般都可以
- 软件：安装好`git`和`mongodb`（默认的主数据库）

### 1.3 获取EdgeX Foundry

#### 1.3.1 GO开发所需

- `Go`开发环境。参阅：<https://eiger.me/post/linux/installubuntu/#4-%E5%AE%89%E8%A3%85go%E8%AF%AD%E8%A8%80%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83>
- 安装`0MQ`库。`0MQ`被用在core-data, export-distro 和 the rules engine 之间。安装参考：<https://gist.github.com/katopz/8b766a5cb0ca96c816658e9407e83d00>

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ ./setup-zeromq.sh
    --2019-08-21 08:26:09--  https://github.com/zeromq/libzmq/releases/download/v4.2.2/zeromq-4.2.2.tar.gz
    Resolving github.com (github.com)... 52.74.223.119
    Connecting to github.com (github.com)|52.74.223.119|:443... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/263379/d5eb3cca-f60d-11e6-9759-2a65ee20d058?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20190821%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190821T122612Z&X-Amz-Expires=300&X-Amz-Signature=6f7458e4a04dea27894aeaf489f909bc2d669b5c2fcd11d1ecfbf636031102b1&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dzeromq-4.2.2.tar.gz&response-content-type=application%2Foctet-stream [following]
    --2019-08-21 08:26:11--  https://github-production-release-asset-2e65be.s3.amazonaws.com/263379/d5eb3cca-f60d-11e6-9759-2a65ee20d058?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20190821%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190821T122612Z&X-Amz-Expires=300&X-Amz-Signature=6f7458e4a04dea27894aeaf489f909bc2d669b5c2fcd11d1ecfbf636031102b1&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dzeromq-4.2.2.tar.gz&response-content-type=application%2Foctet-stream
    Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 52.216.21.59
    Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.21.59|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1236437 (1.2M) [application/octet-stream]
    Saving to: ‘zeromq-4.2.2.tar.gz’

    zeromq-4.2.2.tar.gz 100%[===================>]   1.18M  64.0KB/s    in 16s

    2019-08-21 08:26:29 (75.2 KB/s) - ‘zeromq-4.2.2.tar.gz’ saved [1236437/1236437]

    (...长串输出信息...)
    ----------------------------------------------------------------------
    Libraries have been installed in:
    /usr/local/lib

    If you ever happen to want to link against installed libraries
    in a given directory, LIBDIR, you must either use libtool, and
    specify the full pathname of the library, or use the `-LLIBDIR'
    flag during linking and do at least one of the following:
    - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
        during execution
    - add LIBDIR to the `LD_RUN_PATH' environment variable
        during linking
    - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
    - have your system administrator add LIBDIR to `/etc/ld.so.conf'

    See any operating system documentation about shared libraries for
    more information, such as the ld(1) and ld.so(8) manual pages.
    ----------------------------------------------------------------------
    /bin/mkdir -p '/usr/local/bin'
    /bin/bash ./libtool   --mode=install /usr/bin/install -c tools/curve_keygen '/usr/local/bin'
    libtool: install: /usr/bin/install -c tools/.libs/curve_keygen /usr/local/bin/curve_keygen
    /bin/mkdir -p '/usr/local/include'
    /usr/bin/install -c -m 644 include/zmq.h include/zmq_utils.h '/usr/local/include'
    /bin/mkdir -p '/usr/local/lib/pkgconfig'
    /usr/bin/install -c -m 644 src/libzmq.pc '/usr/local/lib/pkgconfig'
    make[2]: Leaving directory '/home/eiger/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts/zeromq-4.2.2'
    make[1]: Leaving directory '/home/eiger/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts/zeromq-4.2.2'
        libzmq.so.5 (libc6,x86-64) => /usr/local/lib/libzmq.so.5
        libzmq.so (libc6,x86-64) => /usr/local/lib/libzmq.so
    ```

- 一个趁手的IDE：Goland或者VSCode，或者别的。

#### 1.3.2 获取项目源码

edgeX中go语言编写的微服务源码地址：<https://github.com/edgexfoundry/edgex-go>

下载代码的方式很多，我选择`go get`的方式：

```shell
# 加-v有输出信息
go get -v github.com/edgexfoundry/edgex-go
# 完成后项目源码目录在
# $GOPATH/pkg/mod/github.com/edgexfoundry/edgex-go@v1.0.1
# 官方文档及官方Github文档中均是安装在了
# $GOPATH/src/github.com/edgexfoundry/edgex-go
```

虽然这没什么本质影响，但是考虑到后边还要下载edgex的sdk进行开发，如果放到pkg里会有些怪怪的，通常认为pkg放一些不变的东西好些。所以重新将代码拉取到src下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ git clone  https://github.com/edgexfoundry/edgex-go.git
Cloning into 'edgex-go'...
remote: Enumerating objects: 24527, done.
remote: Total 24527 (delta 0), reused 0 (delta 0), pack-reused 24527
Receiving objects: 100% (24527/24527), 150.19 MiB | 255.00 KiB/s, done.
Resolving deltas: 100% (12385/12385), done.
```

#### 1.3.3 安装MongoDB

这里只介绍linux上安装方法：

1. 安装mongodb并验证：

    ```shell
    sudo apt install mongodb-server
    systemctl status mongodb
    ```

2. 初始化数据库

    当mongodb运行起来后，需要通过`init_mongo.js`来为`EdgeX`各项服初始化数据：

    ```shell
    wget https://github.com/edgexfoundry/docker-edgex-mongo/raw/master/init_mongo.js
    sudo -u mongodb mongo < init_mongo.js
    ```

    我的过程是：

    ```shell
    # 下载到本地后，移动到 $GOPATH/src/github.com/edgexfoundry/edgex-go/scripts/
    wget https://github.com/edgexfoundry/docker-edgex-mongo/raw/master/init_mongo.js

    # 执行sudo -u mongodb mongo < init_mongo.js时报错权限不足
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ ls
    init_mongo.js  setup-zeromq.sh  zeromq-4.2.2  zeromq-4.2.2.tar.gz
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ sudo -u mongodb mongo < init_mongo.js
    [sudo] password for eiger:
    MongoDB shell version v3.6.3
    connecting to: mongodb://127.0.0.1:27017
    MongoDB server version: 3.6.3
    2019-08-21T09:28:28.461-0400 I STORAGE  [main] In File::open(), ::open for '/home/eiger/.mongorc.js' failed with Permission denied
    The ".mongorc.js" file located in your home folder could not be executed

    # 查看'/home/eiger/.mongorc.js'权限，提升权限，重试
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ ll ~/.mongorc.js
    -rw------- 1 eiger eiger 0 Aug 14 09:53 /home/eiger/.mongorc.js
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ sudo chmod 777 ~/.mongorc.js
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ sudo -u mongodb mongo < init_mongo.js
    (...长串正常输出信息...)
    { "ok" : 1 }
    bye
    2019-08-21T09:37:49.857-0400 E -        [main] Error saving history file: FileOpenFailed: Unable to open() file /home/eiger/.dbshell: Permission denied

    # 如法炮制，提升'/home/eiger/.dbshell'权限，再重试
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ sudo chmod 777 ~/.dbshell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go/scripts$ sudo -u mongodb mongo < init_mongo.js
    MongoDB shell version v3.6.3
    connecting to: mongodb://127.0.0.1:27017
    MongoDB server version: 3.6.3
    admin
    authorization
    Successfully added user: {
        "user" : "admin",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "authorization"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'authorization.keyStore' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    WriteResult({ "nInserted" : 1 })
    {
        "ok" : 0,
        "errmsg" : "a collection 'authorization.serviceMapping' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    WriteResult({ "nInserted" : 1 })
    WriteResult({ "nInserted" : 1 })
    WriteResult({ "nInserted" : 1 })
    WriteResult({ "nInserted" : 1 })
    WriteResult({ "nInserted" : 1 })
    WriteResult({ "nInserted" : 1 })
    WriteResult({ "nInserted" : 1 })
    admin
    WriteResult({ "nRemoved" : 9 })
    WriteResult({
        "nRemoved" : 0,
        "writeError" : {
            "code" : 40670,
            "errmsg" : "removing FeatureCompatibilityVersion document is not allowed"
        }
    })
    WriteResult({
        "nInserted" : 0,
        "writeError" : {
            "code" : 11000,
            "errmsg" : "E11000 duplicate key error collection: admin.system.version index: _id_ dup key: { : \"authSchema\" }"
        }
    })
    admin
    Successfully added user: {
        "user" : "admin",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "admin"
            }
        ]
    }
    metadata
    Successfully added user: {
        "user" : "meta",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "metadata"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.addressable' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.command' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.device' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.deviceManager' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.deviceProfile' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.deviceReport' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.deviceService' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.provisionWatcher' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.schedule' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'metadata.scheduleEvent' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    coredata
    Successfully added user: {
        "user" : "core",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "coredata"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'coredata.event' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'coredata.reading' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'coredata.valueDescriptor' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    rules_engine_db
    Successfully added user: {
        "user" : "rules_engine_user",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "rules_engine_db"
            }
        ]
    }
    notifications
    Successfully added user: {
        "user" : "notifications",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "notifications"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'notifications.notification' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'notifications.transmission' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'notifications.subscription' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    scheduler
    Successfully added user: {
        "user" : "scheduler",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "scheduler"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'scheduler.interval' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'scheduler.intervalAction' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 2,
        "note" : "all indexes already exist",
        "ok" : 1
    }
    logging
    Successfully added user: {
        "user" : "logging",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "logging"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'logging.logEntry' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    exportclient
    Successfully added user: {
        "user" : "exportclient",
        "roles" : [
            {
                "role" : "readWrite",
                "db" : "exportclient"
            }
        ]
    }
    {
        "ok" : 0,
        "errmsg" : "a collection 'exportclient.exportConfiguration' already exists",
        "code" : 48,
        "codeName" : "NamespaceExists"
    }
    bye
    ```

#### 1.3.4 编译源码并测试

1. 准备go环境。EdgeX使用的go版本为1.11.8，而本机安装了1.12.7。
2. 编译微服务。

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go$ make build
    (...长串输出信息，一次成功...)
    # 编译产物（即各个微服务）在 edgex-go/cmd下
    ```

3. 测试编译结果：

    ```shell
    # make run拉起所有服务
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/edgex-go$ make run
    cd bin && ./edgex-launch.sh
    Creating directory: ./logs
    Creating directory: ./logs
    level=INFO ts=2019-08-21T16:25:47.987847637Z app=edgex-core-command source=main.go:58 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:47.987882347Z app=edgex-core-metadata source=main.go:58 msg="Service dependencies resolved..."
    Creating directory: ./logs
    level=INFO ts=2019-08-21T16:25:48.004290482Z app=edgex-core-metadata source=main.go:59 msg="Starting edgex-core-metadata 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.004298733Z app=edgex-core-command source=main.go:59 msg="Starting edgex-core-command 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.012562216Z app=edgex-core-command source=main.go:62 msg="This is the Core Command Microservice"
    level=INFO ts=2019-08-21T16:25:48.012561757Z app=edgex-core-metadata source=main.go:62 msg="This is the EdgeX Core Metadata Microservice"
    level=INFO ts=2019-08-21T16:25:48.018675255Z app=edgex-core-command source=main.go:69 msg="Service started in: 40.893216ms"
    level=INFO ts=2019-08-21T16:25:48.018682606Z app=edgex-core-metadata source=main.go:69 msg="Service started in: 41.020568ms"
    level=INFO ts=2019-08-21T16:25:48.029320458Z app=edgex-core-metadata source=main.go:70 msg="Listening on port: 48081"
    level=INFO ts=2019-08-21T16:25:48.029329766Z app=edgex-core-command source=main.go:70 msg="Listening on port: 48082"
    Creating directory: ./logs
    level=INFO ts=2019-08-21T16:25:48.111919353Z app=edgex-sys-mgmt-agent source=main.go:59 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:48.117110016Z app=edgex-sys-mgmt-agent source=main.go:60 msg="Starting edgex-sys-mgmt-agent 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.122206755Z app=edgex-sys-mgmt-agent source=main.go:63 msg="This is the System Management Agent Service"
    level=INFO ts=2019-08-21T16:25:48.127502809Z app=edgex-sys-mgmt-agent source=main.go:70 msg="Service started in: 24.360819ms"
    level=INFO ts=2019-08-21T16:25:48.132092488Z app=edgex-sys-mgmt-agent source=main.go:71 msg="Listening on port: 48090"
    Creating directory: ./logs
    level=INFO ts=2019-08-21T16:25:48.146974866Z app=edgex-support-logging source=main.go:52 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:48.147263715Z app=edgex-support-logging source=main.go:53 msg="Starting edgex-support-logging 1.1.0"
    level=INFO ts=2019-08-21T16:25:48.147643812Z app=edgex-support-logging source=main.go:59 msg="Service started in: 36.610875ms"
    Creating directory: ./logs
    level=INFO ts=2019-08-21T16:25:48.148829023Z app=edgex-support-logging source=main.go:60 msg="Listening on port: 48061"
    Creating directory: ./logs
    Creating directory: ./logs
    level=INFO ts=2019-08-21T16:25:48.162574144Z app=edgex-export-client source=main.go:54 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:48.171478674Z app=edgex-support-notifications source=main.go:62 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:48.173658445Z app=edgex-core-data source=main.go:59 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:48.173957942Z app=edgex-export-client source=main.go:55 msg="Starting edgex-export-client 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.176272348Z app=edgex-core-data source=main.go:60 msg="Starting edgex-core-data 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.176271604Z app=edgex-support-notifications source=main.go:63 msg="Starting edgex-support-notifications 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.184283506Z app=edgex-core-data source=main.go:63 msg="This is the Core Data Microservice"
    level=INFO ts=2019-08-21T16:25:48.184310657Z app=edgex-export-client source=main.go:58 msg="This is the Export Client Microservice"
    level=INFO ts=2019-08-21T16:25:48.184310107Z app=edgex-support-notifications source=main.go:66 msg="This is the Support Notifications Microservice"
    level=INFO ts=2019-08-21T16:25:48.190741378Z app=edgex-support-scheduler source=main.go:46 msg="Service dependencies resolved...edgex-support-scheduler 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.192652703Z app=edgex-core-data source=main.go:70 msg="Service started in: 60.266844ms"
    level=INFO ts=2019-08-21T16:25:48.192717084Z app=edgex-support-notifications source=main.go:73 msg="Service started in: 61.273528ms"
    level=INFO ts=2019-08-21T16:25:48.192714903Z app=edgex-export-client source=main.go:65 msg="Service started in: 68.127124ms"
    level=INFO ts=2019-08-21T16:25:48.205363087Z app=edgex-support-notifications source=main.go:74 msg="Listening on port: 48060"
    level=INFO ts=2019-08-21T16:25:48.205373181Z app=edgex-support-scheduler source=main.go:47 msg="Starting edgex-support-scheduler 1.1.0 "
    level=INFO ts=2019-08-21T16:25:48.205371151Z app=edgex-export-client source=main.go:66 msg="Listening on port: 48071"
    level=INFO ts=2019-08-21T16:25:48.205370104Z app=edgex-core-data source=main.go:71 msg="Listening on port: 48080"
    level=INFO ts=2019-08-21T16:25:48.209024221Z app=edgex-support-scheduler source=loader.go:29 msg="loading intervals, interval actions ..."
    level=INFO ts=2019-08-21T16:25:48.224999837Z app=edgex-support-scheduler source=schedule.go:115 msg="scheduler could not find interval with interval with name : midnight"
    level=INFO ts=2019-08-21T16:25:48.242858723Z app=edgex-support-scheduler source=loader.go:138 name=midnight id=id msg="added interval to the support-scheduler database"
    level=INFO ts=2019-08-21T16:25:48.252812155Z app=edgex-support-scheduler source=schedule.go:146 msg="added the interval with id : 7be075a5-f527-49af-8552-8bd89000cf0d into the scheduler queue"
    level=WARN ts=2019-08-21T16:25:48.261084906Z app=edgex-support-scheduler source=schedule.go:235 msg="scheduler could not find interval id with intervalAction name : scrub-pushed-events"
    level=INFO ts=2019-08-21T16:25:48.270312018Z app=edgex-support-scheduler source=loader.go:153 name=scrub-pushed-events id=2411d50e-a0b8-405f-aec5-c1908a3f1bc9 msg="added interval action to the support-scheduler"
    level=INFO ts=2019-08-21T16:25:48.277813221Z app=edgex-support-scheduler source=schedule.go:299 msg="added the intervalAction with id: 2411d50e-a0b8-405f-aec5-c1908a3f1bc9 to interal: midnight into the queue"
    level=WARN ts=2019-08-21T16:25:48.285830113Z app=edgex-support-scheduler source=schedule.go:235 msg="scheduler could not find interval id with intervalAction name : scrub-aged-events"
    level=INFO ts=2019-08-21T16:25:48.295678845Z app=edgex-support-scheduler source=loader.go:153 name=scrub-aged-events id=d27300cc-c5d1-4208-b4ac-b7b7f6b3ce3f msg="added interval action to the support-scheduler"
    level=INFO ts=2019-08-21T16:25:48.30263224Z app=edgex-support-scheduler source=schedule.go:299 msg="added the intervalAction with id: d27300cc-c5d1-4208-b4ac-b7b7f6b3ce3f to interal: midnight into the queue"
    level=INFO ts=2019-08-21T16:25:48.311003784Z app=edgex-support-scheduler source=loader.go:52 msg="finished loading intervals, interval actions"
    level=INFO ts=2019-08-21T16:25:48.319165949Z app=edgex-support-scheduler source=main.go:56 msg="This is the Support Scheduler Microservice"
    level=INFO ts=2019-08-21T16:25:48.327485167Z app=edgex-support-scheduler source=main.go:66 msg="Service started in: 181.927958ms"
    level=INFO ts=2019-08-21T16:25:48.335770127Z app=edgex-support-scheduler source=main.go:67 msg="Listening on port: 48085"
    level=INFO ts=2019-08-21T16:25:49.001052664Z app=edgex-export-distro source=messaging.go:41 msg="Connecting to incoming message bus at: tcp://localhost:5563"
    level=INFO ts=2019-08-21T16:25:49.004807938Z app=edgex-export-distro source=messaging.go:50 msg="Connected to inbound event messages for topic: events"
    level=INFO ts=2019-08-21T16:25:49.011885438Z app=edgex-export-distro source=main.go:53 msg="Service dependencies resolved..."
    level=INFO ts=2019-08-21T16:25:49.02014149Z app=edgex-export-distro source=main.go:54 msg="Starting edgex-export-distro 1.1.0 "
    level=INFO ts=2019-08-21T16:25:49.028259337Z app=edgex-export-distro source=main.go:57 msg="This is the Export Distro Microservice"
    level=INFO ts=2019-08-21T16:25:49.03657099Z app=edgex-export-distro source=main.go:66 msg="Service started in: 1.048024751s"
    level=INFO ts=2019-08-21T16:25:49.036901065Z app=edgex-export-distro source=registrations.go:306 msg="Starting Export Distro :48070"
    level=INFO ts=2019-08-21T16:25:49.044854901Z app=edgex-export-distro source=main.go:67 msg="Listening on port: 48070"
    level=INFO ts=2019-08-21T16:25:49.060781513Z app=edgex-export-distro source=registrations.go:340 msg="Starting registration loop"
    level=INFO ts=2019-08-22T01:01:19.135242491Z app=edgex-core-data source=event.go:287 msg="Scrubbing events.  Deleting all events that have been pushed"
    level=INFO ts=2019-08-22T01:01:19.230020654Z app=edgex-core-data source=router.go:178 msg="Deleting events by age: 604800000"

    #　在另外一个终端执行curl http://localhost:48080/api/v1/ping，得到pong响应
    iger@eiger-ThinkPad-X1-Carbon-3rd:~$ curl http://localhost:48080/api/v1/ping
    pongeiger@eiger-ThinkPad-X1-Carbon-3rd:~$

    # 说明edgex-go微服务组运行正常。
    ```

### 1.4 为EdgeX写一个设备服务

EdgeX Device Service SDK可帮助开发人员快速为EdgeX创建新的设备连接器，因为它提供了每个设备服务所需的通用支架。脚手架提供了供应设备的模式。它提供了通用模板代码来接收命令（a.k.a. actu）请求并做出反应。最后，脚手架提供了通用代码，以帮助将来自传感器的数据导入EdgeX Core Data（通常称为数据摄取）。通过SDK，开发人员可以专注于通过设备协议与设备通信所特有的代码。

在这些指南中，您将创建一个简单的设备服务，该服务生成随机数，而不是从实际传感器获取数据。通过这种方式，您可以探索完成设备服务所需的一些脚手架和工作，而无需实际使用设备进行通信。

#### 1.4.1 前置条件

- go
- git
- make

#### 1.4.2 获取EdgeX Go SDK

```shell
# 获取Go SDK
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ git clone https://github.com/edgexfoundry/device-sdk-go.git
Cloning into 'device-sdk-go'...
remote: Enumerating objects: 2964, done.
remote: Total 2964 (delta 0), reused 0 (delta 0), pack-reused 2964
Receiving objects: 100% (2964/2964), 736.44 KiB | 119.00 KiB/s, done.
Resolving deltas: 100% (1578/1578), done.
# 准备工作区
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ mkdir device-simple
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ ls
device-sdk-go  device-simple  edgex-go
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ cp -rf ./device-sdk-go/example/* ./device-simple/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ ls
device-sdk-go  device-simple  edgex-go
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ ls device-simple/
cmd  driver
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ cp ./device-sdk-go/Makefile ./device-simple
    # 注意这里官方文档VERSION名写错了
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ cp ./device-sdk-go/Version ./device-simple/
cp: cannot stat './device-sdk-go/Version': No such file or directory
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ cp ./device-sdk-go/VERSION ./device-simple/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ cp ./device-sdk-go/version.go ./device-simple/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ ls device-simple/
cmd  driver  Makefile  VERSION  version.go
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$
```

#### 1.4.3 新的设备服务

1. 修改device-simple/cmd/main.go中import项：

    ```go
    // 原本如下
    "github.com/edgexfoundry/device-sdk-go"
    "github.com/edgexfoundry/device-sdk-go/example/driver"
    // 修改为
    "github.com/edgexfoundry/device-simple"
    "github.com/edgexfoundry/device-simple/driver"
    ```

2. 修改Makefile

    ```Makefile
    # Replace MICROSERVICES
    MICROSERVICES=example/cmd/device-simple/device-simple
    # with
    MICROSERVICES=cmd/device-simple/device-simple

    # Modify GOFLAGS:
    GOFLAGS=-ldflags "-X github.com/edgexfoundry/device-sdk-go.Version=$(VERSION)"
    # line to refer to the new service with:
    GOFLAGS=-ldflags "-X github.com/edgexfoundry/device-simple.Version=$(VERSION)"

    # Modify build:
    example/cmd/device-simple/device-simple:
    $(GO) build $(GOFLAGS) -o $@ ./example/cmd/device-simple
    # to:
    cmd/device-simple/device-simple:
    $(GO) build $(GOFLAGS) -o $@ ./cmd/device-simple
    ```

    (ps: 可以看到还有`docker`项也需要修改，但不是这个章节的关注点，所以暂时不修改)

3. 保存Makefile
4. 创建go.mod

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple$ go mod init
    go: creating new go.mod: module github.com/edgexfoundry/device-simple
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple$ ls
    cmd  driver  go.mod  Makefile  VERSION  version.go
    ```

    进入IDE，打开一些go文件，会自动将依赖写入`go.mod`，并生成`go.sum`。

#### 1.4.4 编译服务

1. 确保现在的工程目录

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple$ tree .
    .
    ├── cmd
    │   └── device-simple
    │       ├── Dockerfile
    │       ├── main.go
    │       └── res
    │           ├── configuration.toml
    │           ├── docker
    │           │   └── configuration.toml
    │           ├── off.jpg
    │           ├── on.png
    │           └── Simple-Driver.yaml
    ├── driver
    │   └── simpledriver.go
    ├── go.mod
    ├── go.sum
    ├── Makefile
    ├── VERSION
    └── version.go

    5 directories, 13 files
    ```

2. 编译服务

    ```shell
    # make build
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple$ make build
    CGO_ENABLED=0 GO111MODULE=on go build -ldflags "-X github.com/edgexfoundry/device-simple.Version=1.1.0" -o cmd/device-simple/device-simple ./cmd/device-simple
    CGO_ENABLED=0 GO111MODULE=on go install -tags=safe
    # 编译结果device-simple/device-simple
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple$ ls cmd/device-simple/
    device-simple  Dockerfile  main.go  res
    ```

#### 1.4.5 自定义设备服务

在这一小节将完成一个生成随机数生成设备，并可以让你了解到该设备服务如何调用访问这个"设备"

```go
// simple-driver.go

// import()项
// 增加
"math/rand"

// HandleReadCommands()
// 修改
// if reqs[0].DeviceResourceName == "SwitchButton" {
// cv, _ := dsModels.NewBoolValue(reqs[0].DeviceResourceName, now, s.switchButton)
// res[0] = cv
// 为
if reqs[0].DeviceResourceName == "randomnumber" {
    cv, _ := dsModels.NewInt32Value(reqs[0].DeviceResourceName, now, int32(rand.Intn(100)))
    res[0] = cv
```

#### 1.4.6 自定义设备协议

设备配置文件是一个YAML文件，用于描述EdgeX的一类设备。有关设备类型，这些设备提供的数据以及如何命令设备的一般特性都在设备配置文件中提供。设备服务使用设备配置文件来了解从设备收集的数据（在某些情况下，提供设备服务使用的信息，以了解如何与设备通信并获得所需的传感器读数）。需要设备配置文件来描述从简单随机数生成设备服务收集的数据。

步骤如下：

浏览cmd/device-simple/res文件夹中的文件。记下已经存在的示例设备配置文件YAML文件（Simple-Driver.yml）。您可以浏览此文件的内容以查看YAML如何表示设备。特别要注意传感器的字段或属性如何用“deviceResources”表示。要发布到设备的命令由“deviceCommands”表示。

将random-generator-device.yaml下载到cmd /device-simple /res文件夹。

在文本编辑器中打开random-generator-device.yaml文件。在此设备配置文件中，您定义要向EdgeX描述的设备具有EdgeX需要了解的单个属性（或deviceResource） -在这种情况下，属性是“randomnumber”。请注意如何键入deviceResource。

在现实世界物联网情况下，此设备资源列表可能非常广泛，并且可以填充所有不同类型的数据。

另请注意，设备配置文件如何描述其他人可以使用的REST命令，以便从设备服务中调用（或“获取”）随机数。

实际做法：在`cmd//device-simple/res`准备好`random-generator-device.yaml`文件。其内容为：

```yaml
name: "RandNum-Device"
manufacturer: "Dell Technologies"
model: "1"
labels:
 - "random"
 - "test"
description: "random number generator to simulate a device"

deviceResources:
    -
        name: "randomnumber"
        description: "generated random number"
        attributes:
            { type: "random" }
        properties:
            value:
                { type: "INT32", readWrite: "R", defaultValue: "0.00", minimum: "0.00", maximum: "100.00"  }
            units:
                { type: "String", readWrite: "R", defaultValue: "" }

deviceCommands:
    -
        name: "Random"
        get:
            -
                { operation: "get", object: "randomnumber", property: "value", parameter: "Random" }

coreCommands:
  -
    name: "Random"
    get:
        path: "/api/v1/device/{deviceId}/Random"
        responses:
          -
            code: "200"
            description: ""
            expectedValues: ["Random"]
          -
            code: "503"
            description: "service unavailable"
            expectedValues: []
```

#### 1.4.7 配置设备服务

现在更新新设备服务的配置 -更改其操作的端口（以免与其他设备服务冲突），更改从设备服务收集数据的自动事件频率（在此示例中每10秒） ），并在服务启动时设置随机数生成设备的初始供应。

将configuration.toml下载到cmd /device-simple /res文件夹（这将覆盖现有文件 -没关系）。

configuration.toml内容：

```toml
[Service]
Host = "localhost"
Port = 49992
ConnectRetries = 3
Labels = []
OpenMsg = "device simple started"
MaxResultCount = 50000
Timeout = 5000
EnableAsyncReadings = true
AsyncBufferSize = 16

[Registry]
Host = "localhost"
Port = 8500
CheckInterval = "10s"
FailLimit = 3
FailWaitTime = 10

[Clients]
  [Clients.Data]
  Name = "edgex-core-data"
  Protocol = "http"
  Host = "localhost"
  Port = 48080
  Timeout = 5000

  [Clients.Metadata]
  Name = "edgex-core-metadata"
  Protocol = "http"
  Host = "localhost"
  Port = 48081
  Timeout = 5000

  [Clients.Logging]
  Name = "edgex-support-logging"
  Protocol = "http"
  Host = "localhost"
  Port = 48061

[Device]
  DataTransform = true
  InitCmd = ""
  InitCmdArgs = ""
  MaxCmdOps = 128
  MaxCmdValueLen = 256
  RemoveCmd = ""
  RemoveCmdArgs = ""
  ProfilesDir = "./res"

[Logging]
EnableRemote = false
File = "./device-simple.log"
Level = "DEBUG"

# Pre-define Devices
[[DeviceList]]
  Name = "RandNum-Device01"
  Profile = "RandNum-Device"
  Description = "Random Number Generator Device"
  Labels = [ "random", "test" ]
  [DeviceList.Protocols]
    [DeviceList.Protocols.Other]
      Address = "random"
      Port = 300
  [[DeviceList.AutoEvents]]
    Resource = "Random"
    OnChange = false
    Frequency = "10s"
```

#### 1.4.8 重新编译

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple$ make build
CGO_ENABLED=0 GO111MODULE=on go build -ldflags "-X github.com/edgexfoundry/device-simple.Version=1.1.0" -o cmd/device-simple/device-simple ./cmd/device-simple
CGO_ENABLED=0 GO111MODULE=on go install -tags=safe
```

#### 1.4.9 运行设备服务

1. 参照[2.1.3 运行EdgeX](#213-%e8%bf%90%e8%a1%8cedgex)启动EdgeX全部微服务。
2. 进入前面的编译输出目录`device-simple/cmd/device-simple/`下执行`./device-simple`

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple/cmd/device-simple$ ls
    device-simple  Dockerfile  main.go  res
    # 第一次执行时报错端口300不能由整型转字符串
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple/cmd/device-simple$ ./device-simple
    Init: useRegistry:  profile:  confDir:
    Loading configuration from: /home/eiger/gopath-default/src/github.com/edgexfoundry/device-simple/cmd/device-simple/res/configuration.toml

    error loading config file: unable to parse configuration file (res/configuration.toml): (64, 7): Can't convert 300(int64) to string
    # 这个应该是读取配置文件的部分代码写定了
    # 按照配置经验来看，应该是配置结构体反序列化声明时标注了端口为字符串类型
    # 所以配置文件中该项必须为字符串形式（用双引号括起）
    # 其实查看res/docker/configuration.toml可以看出模板配置文件在自定义设备配置处端口也是双号引括起。

    # 将配置文件中(64,7)处端口号加上双引号后重新启动
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-simple/cmd/device-simple$ ./device-simple
    Init: useRegistry:  profile:  confDir:
    Loading configuration from: /home/eiger/gopath-default/src/github.com/edgexfoundry/device-simple/cmd/device-simple/res/configuration.toml

    Bypassing registration in registry...
    Calling service.Start.
    EnableRemote is false, using local log file
    level=INFO ts=2019-08-22T07:56:14.83737224Z app=device-simple source=init.go:137 msg="Check Metadata service's status ..."
    level=INFO ts=2019-08-22T07:56:14.837372226Z app=device-simple source=init.go:137 msg="Check Data service's status ..."
    level=INFO ts=2019-08-22T07:56:14.89060449Z app=device-simple source=init.go:47 msg="Service clients initialize successful."
    level=INFO ts=2019-08-22T07:56:14.993195309Z app=device-simple source=service.go:142 msg="Device Service  doesn't exist, creating a new one"
    level=INFO ts=2019-08-22T07:56:15.022525245Z app=device-simple source=service.go:198 msg="Addressable device-simple doesn't exist, creating a new one"
    level=INFO ts=2019-08-22T07:56:15.211907806Z app=device-simple source=service.go:120 msg="*Service Start() called, name=device-simple, version=1.1.0"
    level=INFO ts=2019-08-22T07:56:15.237638827Z app=device-simple source=service.go:126 msg="Listening on port: 49992"
    level=INFO ts=2019-08-22T07:56:15.248397329Z app=device-simple source=service.go:127 msg="Service started in: 412.658582ms"
    # 从下面开始，在不断生成随机数，并发给core data服务
    level=INFO ts=2019-08-22T07:56:25.221156696Z app=device-simple source=utils.go:77 Content-Type=application/json correlation-id=0b1aa307-9006-457b-b93a-7616f94c5b9b msg="SendEvent: Pushed event to core data"
    level=INFO ts=2019-08-22T07:56:35.220232796Z app=device-simple source=utils.go:77 Content-Type=application/json correlation-id=28cec2ce-5a39-4f17-a6c5-49bea7d6aebd msg="SendEvent: Pushed event to core data"
    (......)
    ```

3. 测试

    除了在终端能看到日志输出，以及使用`docker-compose logs -f data`(`data`为服务名，其对应容器为`edgex-core-data`)查看其日志，可以看到不断接收到数据，产生日志。

    从官方文档的4.4节混合环境开发<https://docs.edgexfoundry.org/Ch-GettingStartedHybrid.html>的运行结果显示可以看出我的运行结果是正常的。

    也可以在浏览器访问url: <http://localhost:48080/api/v1/event/device/RandNum-Device-01/100>。

    但是，浏览器页面只显示一对空的方括号。

    在浏览器中开发者工具中`console`控制台可看到`Content Security Policy: The page’s settings blocked the loading of a resource at http://localhost:48080/favicon.ico (“default-src”).`(火狐浏览器)。

    什么意思呢？就是加载`favicon.ico`失败了，然后堵住了后边要做的显示。奇怪的是，在`device-simple`目录下是找不到这个图标文件的。

    从这个错误出发，可以猜测，`device-simple`是没问题的，可能是在SDK中定义http server时静态文件服务器处有部分代码用到了该文件，想将该文件渲染至前端。

    **暂时不知道怎么解决，先放在这**

### 1.5 混合环境开发

混合环境开发指的是现有的已发行的服务以docker形式部署，而正在开发的服务在IDE或命令行下编译，与已发行服务进行交互来测试。这种叫做混合环境开发，或者说一部分服务是docker容器环境，而一部分是在开发环境。

示例：

1. 启动EdgeX全部服务，或者只启动最小系统（consul， Mongo, Core Data, Core Metadata, Core Command, Support Logging, and Support Notifications.）
2. [1.4 为EdgeX写一个设备服务](#14-%e4%b8%baedgex%e5%86%99%e4%b8%80%e4%b8%aa%e8%ae%be%e5%a4%87%e6%9c%8d%e5%8a%a1)就是一个混合环境开发的例子。

### 1.6 从EdgeX Nexus仓库获取docker镜像

<https://docs.edgexfoundry.org/Ch-GettingStartedUsersNexus.html>

### 1.7  EdgeX API 全过程演示

这一节将通过实践展示终端设备到EdgeX再到云端这个过程中的数据流动。在这个过程中将手动创建`REST`风格的调用来模仿EdgeX系统调用。

#### 1.7.1 安装环境

- docker & docker-compose
- postman

    您可以使用curl等工具从命令行进行HTTP调用，但如果您使用专为测试REST API而设计的工具则更容易。为此，我们喜欢使用Postman。您可以下载适用于您的操作系统的本机Postman应用程序。

    **注意！**

    假设为了本演练的目的

  - 所有API微服务都在“localhost”上运行。如果不是这种情况，请将主机名替换为`localhost`。
  - 除非另有明确说明，否则任何POST调用都具有与之关联的关联`CONTENT-TYPE = application/JSON`标头。

#### 1.7.2 运行EdgeX

#### 1.7.3 使用样例描述

<https://docs.edgexfoundry.org/Ch-WalkthroughUseCase.html>

- 假设摄像头拍照，并统计图片中人的数量与狗的数量。
- 摄像头拍照间隔和拍照景深（与人物的距离）可设置
- 在EdgeX中，一个摄像头被描述为一个`Device`，每个Device都被一个`Device Service`(`DS`)管理。每个`DS`负责底层通信采集数据并将数据传给`core-data`服务。反过来，`DS`也可以接受命令请求，解析之后发给真实设备修改其参数（拍照间隔和景深）。

#### 1.7.4 定义你的数据格式

每个`DS`在启动时需要做以下工作：

- 建立`DS`和`Devices`的参考信息。
- 使`DS`本身被EdgeX其他服务发现。
- 通过EdgeX配置好`Devices`

参考信息主要包括两部分：

- Addressabale - 设备或设备服务的地址信息
- Value Descriptor - 设备的测量单元（比如说温度）

Provision(配置)指的是EdgeX与设备初次建立连接的方法过程。

建立了第一次连接之后，就不用每次都建立参考信息了，只是简单检查下参考信息。

那么本小节就将来定义这些参考信息：

1. Addressables

    `Core Metadata API RAML`查阅<https://github.com/edgexfoundry/edgex-go/blob/master/api/raml/core-metadata.raml>

    在这个例子中，设备服务向`Core Metadata`服务发起两个调用，创建两个Addressable：一个是设备服务地址信息，另一个是设备地址信息。

    ```json
    // 设备服务地址
    POST to http://localhost:48081/api/v1/addressable
    BODY: {"name":"camera control","protocol":"HTTP","address":"172.17.0.1","port":49977,"path":"/cameracontrol","publisher":"none","user":"none","password":"none","topic":"none"}
    // 设备地址
    POST to http://localhost:48081/api/v1/addressable
    BODY: {"name":"camera1 address","protocol":"HTTP","address":"172.17.0.1","port":49999,"path":"/camera1","publisher":"none","user":"none","password":"none","topic":"none"}
    ```

    注意：使用postman时BODY数据需要是`raw data`。

    成功的话，会返回`Addressable`的`ID`，形如`5b773ad19f8fc200012a802d`。

2. Value Descriptors

    `Core Data API RAML`查阅<https://github.com/edgexfoundry/edgex-go/blob/master/api/raml/core-data.raml>

    ```json
    // people count
    POST to http://localhost:48080/api/v1/valuedescriptor
    BODY:  {"name":"humancount","description":"people count", "min":"0","max":"100","type":"I","uomLabel":"count","defaultValue":"0","formatting":"%s","labels":["count","humans"]}
    // dog count
    POST to http://localhost:48080/api/v1/valuedescriptor
    BODY:  {"name":"caninecount","description":"dog count", "min":"0","max":"100","type":"I","uomLabel":"count","defaultValue":"0","formatting":"%s","labels":["count","canines"]}
    // depth
    POST to http://localhost:48080/api/v1/valuedescriptor
    BODY:  {"name":"depth","description":"scan distance", "min":"1","max":"10","type":"I","uomLabel":"feet","defaultValue":"1","formatting":"%s","labels":["scan","distance"]}
    // duration
    POST to http://localhost:48080/api/v1/valuedescriptor
    BODY:  {"name":"duration","description":"time between events", "min":"10","max":"180","type":"I","uomLabel":"seconds","defaultValue":"10","formatting":"%s","labels":["duration","time"]}
    // error 应对摄像头与EdgeX失去连接
    POST to http://localhost:48080/api/v1/valuedescriptor
    BODY:  {"name":"cameraerror","description":"error response message from a camera", "min":"","max":"","type":"S","uomLabel":"","defaultValue":"error","formatting":"%s","labels":["error","message"]}

    // GET请求可以获取所有值描述符列表
    GET to http://localhost:48080/api/v1/valuedescriptor
    ```

    同样，每个值描述符的名称必须是唯一的（在所有EdgeX中）。值描述符的类型指示关联值的类型：I（整数），F（浮点数），S（字符或字符串），B（布尔值）或J（JSON对象）。 UI使用格式化，并且应遵循printf格式标准来表示关联值。

#### 1.7.5 定义设备协议

一个设备协议可以认为是一类设备，它们具有相同的类型、数据以及命令。设备服务需要为所管理的设备提供设备协议。

1. 添加一个设备协议
    `Core Metadata API RAML`查阅<https://github.com/edgexfoundry/edgex-go/blob/master/api/raml/core-metadata.raml>

    通过脚手架创建的设备服务（就是SDK中提供的例程）只能管理为人或狗计数的摄像头，因而我们需要以下面的方式添加设备协议（通常以YAML格式描述）。

    ```json
    POST to http://localhost:48081/api/v1/deviceprofile/uploadfile
    Header: 不填
    BODY:
        FORM-DATA:
            key: "file"
            value: EdgeX_CameraMonitorProfile.yml

    ```

    每个配置文件都有一个唯一的名称以及描述，制造商，型号和标签集合，以帮助查询特定的配置文件。这些是配置文件相对简单的属性。

    ```yaml
    # Copyright 2017 Dell Inc. All rights reserved.
    name: "camera monitor profile"
    manufacturer: "Dell"
    model: "Cam12345"
    labels:
        - "camera"
    description: "Human and canine camera monitor profile"
    coreCommands:
    -
        name: People
        get:
            path: "/api/v1/devices/{deviceId}/peoplecount"
            responses:
            -
                code: "200"
                description: "Number of people on camera"
                expectedValues: ["humancount"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: ["cameraerror"]
    -
        name: Dogs
        get:
            path: "/api/v1/devices/{deviceId}/dogcount"
            responses:
            -
                code: "200"
                description: "Number of dogs on camera"
                expectedValues: ["caninecount"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: ["cameraerror"]
    -
        name: ScanDepth
        get:
            path: "/api/v1/devices/{deviceId}/scandepth"
            responses:
            -
                code: "200"
                description: "Get the scan depth"
                expectedValues: ["depth"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: ["cameraerror"]
        put:
            path: "/api/v1/devices/{deviceId}/scandepth"
            parameterNames: ["depth"]
            responses:
            -
                code: "204"
                description: "Set the scan depth."
                expectedValues: []
            -
                code: "503"
                description: "service unavailable"
                expectedValues: ["cameraerror"]
    -
        name: SnapshotDuration
        get:
            path: "/api/v1/devices/{deviceId}/snapshotduration"
            responses:
            -
                code: "200"
                description: "Get the snaphot duration"
                expectedValues: ["duration"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: []
        put:
            path: "/api/v1/devices/{deviceId}/SnapshotDuration"
            parameterNames: ["duration"]
            responses:
            -
                code: "204"
                description: "Set the snapshot duration."
                expectedValues: []
            -
                code: "503"
                description: "service unavailable"
                expectedValues: ["cameraerror"]
    -
        name: Counts
        get:
            path: "/api/v1/devices/{deviceId}/counts"
            responses:
            -
                code: "200"
                description: "Get the people and human counts"
                expectedValues: ["count","count"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: []
    ```

2. 理解Commands
    - 每个命令至少包含一个`get`方法或一个`post`方法，否则无效；
    - 每个命令其名称在该设备协议所有命令中必须独一无二。
    - 设备服务地址和命令路径拼接成命令的完整访问url。（例如 调用foo命令 <http://abc:9000/foo>）
    - `put`方法响应不是必须，`get`方法响应是必需的
    - 每个响应包含：状态码（比如：200-好；404/503-坏）、描述符、期望值数组

        ```json
        "responses":[
            {"code":"200","description":"ok","expectedValues":["humancount", "caninecount"]},
            {"code":"404","description":"bad request","expectedValues":["cameraerror"]}
           ]
       ```

    - 每个命令可以指定参数。（例如摄像头指定拍照间隔）

    注意：Metadata服务并不会检查期望数值数组是不是和描述符匹配，所以设备协议编写时需要自己仔细。

    其实完全可以自行查看前面给出的设备协议文件，很容易看懂。

#### 1.7.6 注册设备服务

一旦参考信息在Metadata和Data服务中建立好，设备服务就可以在EdgeX中注册了，相当于告诉他们“我准备好了”。

1. 注册
    参考`APIs Core Services Configuration and Registry`<https://docs.edgexfoundry.org/Ch-Configuration.html>

    注册过程的一部分是：设备服务在`Core COnfiguration & Registry`中注册并获取最新配置信息。值得注意的是，因为这个例子只是个演示，没有真实设备，所以这里没有探讨微服务交换的部分。

2. 创建设备服务的实例
    参考`APIs Core Services Metadata`<https://github.com/edgexfoundry/edgex-go/blob/master/api/raml/core-metadata.raml>

    设备服务注册过程的另一部分是： 向`Core Metadata`发起一个请求，将其在`Core Metadata`中的定义进行实例化。

    ```json
    POST to http://localhost:48081/api/v1/deviceservice
    BODY: {"name":"camera control device service","description":"Manage human and dog counting cameras","labels":["camera","counter"],"adminState":"unlocked","operatingState":"enabled","addressable":{"name":"camera control"}}
    ```

    所有EdgeX的设备服务名称必须是唯一的。请注意管理和运行状态。管理状态（也称为管理状态）由人或其他系统提供对设备服务的控制。它可以设置为锁定或解锁。当设备服务设置为锁定时，不会响应任何命令请求，也不会从设备发送数据。操作状态（也称为op状态）在EdgeX上提供有关设备服务的内部操作状态的指示。操作状态不是外部设置的（如另一个系统或人员），它是来自EdgeX（可能是设备服务本身）内部关于服务状况的信号。可以启用或禁用设备服务的运行状态。当禁用设备服务的运行状态时，它要么遇到一些困难，要么经历一些不允许其以正常容量运行的过程（例如升级）。

#### 1.7.7 Provision a device

通常来讲，设备服务会发现并立刻连接设备，但也有时候需要人为发出指令才会连接设备。设备服务决定了如何/何时与设备建立连接。

1. 添加你的设备

    `APIs Core Services Metadata`<https://github.com/edgexfoundry/edgex-go/blob/master/api/raml/core-metadata.raml>

    ```json
    POST to http://localhost:48081/api/v1/device
    BODY:  {"name":"countcamera1","description":"human and dog counting camera #1","adminState":"unlocked","operatingState":"enabled","protocols":{"camera protocol":{"camera address":"camera 1"}},"labels": ["camera","counter"],"location":"","service":{"name":"camera control device service"},"profile":{"name":"camera monitor profile"}}
    // camera monitor profile为之前上传CameraPonitorProfile.yml时生成的
    ```

2. 测试设备服务安装

    ```json
    // 检查设备服务
    GET to http://localhost:48081/api/v1/deviceservice
    // 根据标签camera获取所有标签为camera的设备服务
    GET to http://localhost:48081/api/v1/deviceservice/label/camera

    // 检查设备
    GET to http://localhost:48081/api/v1/device
    // 根据设备协议查找所有符合这个协议的设备
    GET to http://localhost:48081/api/v1/device/profilename/camera+monitor+profile
    // + 号代表空格
    ```

#### 1.7.8 调用命令

回忆一下，设备协议包含了一系列命令，而设备在配置过程中和设备协议建立了关联。

```json

// 列举所有命令
// 参考 APIs Core Services Command <https://github.com/edgexfoundry/edgex-go/blob/master/core/command/raml/core-command.raml>
GET to http://localhost:48082/api/v1/device/name/countcamera1

// 检查值描述符
// 参考 APIs Core Services Core Data <https://github.com/edgexfoundry/edgex-go/blob/master/api/raml/core-data.raml>
// 请参阅值描述符在核心数据中。核心数据中应该总共有5个值描述符。请注意，值描述符存储在Core Data中，但在Metadata中引用。这是因为当来自设备的数据被发送到核心数据时，核心数据可能需要根据相关的值描述符参数（如最小值，最大值等）验证传入值，而无需访问核心元数据到做那个验证。将数据导入Core Data是EdgeX的关键功能，必须尽快完成（无需进行额外的REST请求）。
GET to http://localhost:48080/api/v1/valuedescriptor

// 查看core data服务中event数量
// 在我们处理它的同时，注意还没有数据发送到Core Data。由于设备服务和设备完全由您手动驱动，因此尚未收集传感器数据。您可以通过询问核心数据中的事件计数来测试此理论。
GET to http://localhost:48080/api/v1/event/count


// 执行命令
// 虽然在本演练中没有真正的设备或设备服务，但EdgeX并不知道。因此，通过您执行的所有配置和设置，您可以要求EdgeX设置扫描深度或将快照持续时间设置为摄像机，EdgeX将尽职尽责地尝试执行该任务。当然，由于没有设备服务或设备存在，正如预期的那样，EdgeX最终将以错误响应。但是，通过日志文件，您可以看到由Core Command微服务构成的命令，尝试调用管理我们的虚拟摄像机的虚拟设备服务的相应命令。
// 例如，让我们启动一个命令来设置countcamera1的扫描深度（现在是EdgeX中单个人/狗计数相机设备的名称）。启动设置扫描深度的请求的第一个任务是将Command的URL设置为“PUT”或在设备上设置新的扫描深度。如上所示，请通过设备名称请求一个命令列表，并在Core Command上使用以下API
GET to http://localhost:48082/api/v1/device/name/countcamera1

PUT to http://localhost:48082/api/v1/device/<system specific device id>/command/<system specific command id>
BODY:  {"depth":"9"}

// 同样，因为没有设备服务（或设备）实际存在，Core Command将响应HTTP 502 Bad Gateway错误。但是，检查日志记录输出将证明Core Command微服务确实接收到请求并尝试调用不存在的设备服务来发出执行命令。

$ docker logs edgex-core-command

INFO: 2019/02/15 19:32:13 Issuing GET command to: http://172.17.0.1:49977/api/v1/devices/5c6711419f8fc200010f4ada/scandepth
ERROR: 2019/02/15 19:32:13 Get http://172.17.0.1:49977/api/v1/devices/5c6711419f8fc200010f4ada/scandepth: dial tcp 172.17.0.1:49977: getsockopt: connection refused

```

#### 1.7.9 发送event和读取数据

1. 发送event

    数据作为事件提交给Core Data。事件是在特定时间点从设备（通过其ID或名称与设备相关联）传感器读数的集合。事件中的读取是设备感测的特定值，并且与值描述符（通过名称）相关联以提供读取的上下文。因此，人/狗计数相机可能会确定当前正在监控的空间中有5个人和3个狗。在EdgeX语言中，设备服务从设备接收到这些感测值后将创建一个带有两个读数的事件 -一个读数将包含人数的键/值对：5，另一个读数将包含caninecount的键/值对：3。

    设备服务，在创建事件和关联的读取对象时，将通过REST调用将此信息传输到Core Data。

    ```json
    POST to http://localhost:48080/api/v1/event
    BODY: {"device":"countcamera1","readings":[{"name":"humancount","value":"5"},{"name":"caninecount","value":"3"}]}
    ```

    如果需要，设备服务还可以向事件或读取提供origin属性（见下文），以建议感知/收集数据的时间（以Epoch时间戳/毫秒格式）。如果未提供原点，则不会为事件或读取设置原点，但是在数据库中为每个事件和读取提供了创建和修改的时间戳，以便为数据提供一些时间上下文。

    ```json
    BODY: {"device":"countcamera1","origin":1471806386919, "readings":[{"name":"humancount","value":"1","origin":1471806386919},{"name":"caninecount","value":"0","origin":1471806386919}]}
    ```

    注意：智能设备通常会为传感器数据添加时间戳，此时间戳可用作origin时间戳。如果传感器/设备无法提供时间戳（“哑”或棕色场传感器），建议设备服务为传感器数据创建时间戳，该时间戳用作设备的origin时间戳。

2. 读取数据

现在已将事件（或两个）和相关读数发送到Core Data，您可以使用Core Data API来探索现在存储在MongoDB中的数据。

```json
// 回想一下“测试设置”部分，您检查了Core Data中尚未存储任何数据。进行相同的调用，这次，2个事件记录应该是返回的计数。
GET to http://localhost:48080/api/v1/event/count

// Retrieve 10 of the Events associated to the countcamera1 Device.
GET to http://localhost:48080/api/v1/event/device/countcamera1/10

// Retrieve 10 of the human count Readings associated to the countcamera1 Device (i.e. - get Readings by Value Descriptor)
GET to http://localhost:48080/api/v1/reading/name/humancount/10

```

#### 1.7.10 导出设备数据给客户端

设备数据经由设备服务传给`core data`服务，那么接下来数据怎么传给需要使用这些数据的地方呢？比如说传给云端去做数据分析。数据传出的目标称之为客户端，任何想要接收使用设备数据的服务需要注册成为导出层（`export`层的客户端），EdgeX本身有个叫规则引擎（`rules engine`）的服务就是一个导出层客户端。那么本节讲的就是数据如何到处给客户端。

1. 导出层的客户端

    默认的，规则引擎自动注册为导出客户端，接收所有来自`Core Data`的`event/readings`。向`export client`微服务发出以下请求可以查看所有注册为导出客户端的微服务列表：

    ```json
    GET to http://localhost:48071/api/v1/registration
    ```

2. 注册成为导出层客户端

    想要注册成为导出层客户端，就需要向`export client`微服务发出请求。

    要注册新客户端以接收EdgeX数据，首先需要设置能够接收HTTP REST调用的客户端，或者能够从EdgeX接收消息的MQTT主题。出于本演示的目的，假设有一个基于云的MQTT主题已经准备好接收EdgeX事件/读取数据。要注册此MQTT端点以接收JSON格式的所有事件/读取数据，但需要加密，您需要请求Export Client创建新的EdgeX客户端。

    ```json
    POST to http://localhost:48071/api/v1/registration
    BODY: {"name":"MyMQTTTopic","addressable":{"name":"MyMQTTBroker","protocol":"TCP","address":"tcp://m10.cloudmqtt.com","port":15421,"publisher":"EdgeXExportPublisher","user":"hukfgtoh","password":"mypass","topic":"EdgeXDataTopic"},"format":"JSON","encryption":{"encryptionAlgorithm":"AES","encryptionKey":"123","initializingVector":"123"},"enable":true,"destination":"MQTT_TOPIC"}
    ```

    请注意，REST地址的Addressable内置于请求中。

    现在，如果将新事件发布到Core Data，Export Distro微服务将尝试将加密的，JSON格式化的事件/读取数据发送到MQTT客户端。除非您实际设置了MQTT主题以接收消息，否则Export Distro将无法传递内容并导致错误。尽管接收MQTT主题不存在，您仍可以检查Export Distro日志以查看已进行的尝试以及EdgeX Export服务是否正常工作。

    MQTTOutboundServiceActivator：发送到MQTT代理的消息：

    ```json
    Addressable [name=MyMQTTBroker, protocol=TCP, address=tcp://m10.cloudmqtt.com, port=15421, path=null, publisher=EdgeXExportPublisher, user=hukfgtoh, password=mypass, topic=EdgeXDataTopic, toString()=BaseObject [id=null, created=0, modified=0, origin=0]] : 596283c7e4b0011866276e9
    ```

#### 1.7.11 构建自己的解决方案

参考[1.4 为EdgeX写一个设备服务](#14-%e4%b8%baedgex%e5%86%99%e4%b8%80%e4%b8%aa%e8%ae%be%e5%a4%87%e6%9c%8d%e5%8a%a1)

#### 1.7.12 总结

这一节没有进行实践。

总结一下开发自定义设备服务的流程：

1. 明确任务需求；
2. 定义好参考信息包括`Addressables`和`Value Descriptors`；
3. 定义好设备协议；
4. 在代码中写好自动注册设备服务部分的代码，将这些信息发送给`Core Metadata`服务；
5. 在代码中写好如何发现如何连接一个具体设备并且配置其的代码；
6. 向`Core Data`服务发送`event/reading`来发送和读取数据；

最后编译乃至构建容器。

在导出层客户端实际是先注册成为客户端然后从`Core Data`读数据。

### 1.8 实例训练——向EdgeX添加MQTT设备

总体示意图：

![MQTT_Example_Overview](/images/MQTT_Example_Overview.png)
![MQTT_Example_Overview](../../../static/images/MQTT_Example_Overview.png)

用文字来描述就是，EdgeX中的`device-mqtt`服务在`mosquitto`中发布主题，我们本例构建的mqtt客户端（想象成一个真是的基于mqtt的终端设备）将会在`mosquitto`中订阅这个主题并且接收`device-mqtt`发布的主题消息，这就实现了`device-mqtt`对mqtt客户端的通知下发；发过来则实现mqtt客户端数据的上传。

在这个环节中`device-mqtt`是设备服务，mqtt客户端是设备，`Core Data`接收`device-mqtt`自动上传的数据，`Core Command`经由`device-mqtt`再经`mosquitto`向mqtt下发命令并接收命令响应。

![MQTTDeviceService](/images/MQTTDeviceService.png)
![MQTTDeviceService](../../../static/images/MQTTDeviceService.png)

#### 1.8.1 运行MQTT Broker

```shell
# 运行mosquitto docker容器
docker run -d --rm --name broker -p 1883:1883 eclipse-mosquitto
```

运行记录：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker run -d --name broker -p 1883:1883 eclipse-mosquitto
Unable to find image 'eclipse-mosquitto:latest' locally
latest: Pulling from library/eclipse-mosquitto
c87736221ed0: Already exists
02f3d9f19654: Pull complete
33d744bbdbe2: Pull complete
Digest: sha256:f346596c88333e081da1bc62253a8b1e877018a55855c6629839641e2fc121fc
Status: Downloaded newer image for eclipse-mosquitto:latest
20a5c83991cafaba0a1bb87f1362ca29c7eca9bce15d7bc763a33e70d229cf3a
```

#### 1.8.2 构建MQTT Client（MQTT设备仿真）

该客户端要实现三个功能：

- 每15秒发布随机数数据
- 接收读请求，返回响应
- 接受put请求，修改客户端的值（参数）

模拟设备的js代码如下：

```JavaScript
// mock-device.js
function getRandomFloat(min, max) {
    return Math.random() * (max - min) + min;
}

const deviceName = "MQ_DEVICE";
let message = "test-message";

// 1. Publish random number every 15 seconds
schedule('*/15 * * * * *', ()=>{
    let body = {
        "name": deviceName,
               "cmd": "randnum",
        "randnum": getRandomFloat(25,29).toFixed(1)
    };
    publish( 'DataTopic', JSON.stringify(body));
});

// 2. Receive the reading request, then return the response
// 3. Receive the put request, then change the device value
subscribe( "CommandTopic" , (topic, val) => {
    var data = val;
        if (data.method == "set") {
        message = data[data.cmd]
    }else{
        switch(data.cmd) {
            case "ping":
              data.ping = "pong";
              break;
            case "message":
              data.message = message;
              break;
            case "randnum":
                data.randnum = 12.123;
                break;
          }
    }
    publish( "ResponseTopic", JSON.stringify(data));
});
```

将该js文件放置到某文件夹下并重命名为`mqtt-scripts`，通过docker运行并指定broker ip。

```shell
mv mock-device.js /path/to/mqtt-scripts
docker run -d --restart=always --name=mqtt-scripts \
  -v /path/to/mqtt-scripts:/scripts  \
  dersimn/mqtt-scripts --url mqtt://mqtt-broker-ip --dir /scripts

docker run -d --restart=always --name=mqtt-scripts -v mqtt-client-scripts/mqtt-scripts:/scripts  dersimn/mqtt-scripts --url mqtt://172.17.0.1 --dir /scripts
```

我的执行记录：

```shell
# 复制、重命名脚本
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ mkdir mqtt-client-scripts
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ mv mock-device.js mqtt-client-scripts/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ ls mqtt-client-scripts/
mock-device.js

# docker运行 --name容器名；-v (--volume)本机目录与docker目录的映射
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ docker run -d --restart=always --name=mqtt-scripts -v mqtt-client-scripts:/scripts  dersimn/mqtt-scripts --url mqtt://172.17.0.1 --dir /scripts
91c2c91373879665cf15f415428e77df6567e062442f6fe10e84f04258fc8689

```

#### 1.8.3 建立device-service-deomo文件夹

建立device-service-demo文件夹，存放用于部署的一些配置文件。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ tree
.
├── docker-compose.yml
├── mqtt-client-scripts
│   └── mock-device.js
└── mqtt-service-conf
    ├── configuration.toml
    └── mqtt.test.device.profile.yml

2 directories, 4 files
```

1. configuration.toml配置文件定义设备和计划作业。 device-mqtt在启动时据此生成相对实例。MQTT是订阅/发布模式，因此我们必须在配置文件的[DeviceList.Protocols]部分中定义MQTT连接信息。记得用实际的主机地址替换文件中的主机IP。

    下面是我根据我的情况修改的configuration.toml文件内容：

    ```toml
    # configuration.toml
    [Writable]
    LogLevel = 'DEBUG'

    [Service]
    Host = "edgex-device-mqtt"
    Port = 49982
    ConnectRetries = 3
    Labels = []
    OpenMsg = "device mqtt started"
    Timeout = 5000
    EnableAsyncReadings = true
    AsyncBufferSize = 16

    [Registry]
    Host = "edgex-core-consul"
    Port = 8500
    CheckInterval = "10s"
    FailLimit = 3
    FailWaitTime = 10
    Type = "consul"

    [Logging]
    EnableRemote = false
    File = "./device-mqtt.log"

    [Clients]
    [Clients.Data]
    Name = "edgex-core-data"
    Protocol = "http"
    Host = "edgex-core-data"
    Port = 48080
    Timeout = 50000

    [Clients.Metadata]
    Name = "edgex-core-metadata"
    Protocol = "http"
    Host = "edgex-core-metadata"
    Port = 48081
    Timeout = 50000

    [Clients.Logging]
    Name = "edgex-support-logging"
    Protocol = "http"
    Host ="edgex-support-logging"
    Port = 48061

    [Device]
    DataTransform = true
    InitCmd = ""
    InitCmdArgs = ""
    MaxCmdOps = 128
    MaxCmdValueLen = 256
    RemoveCmd = ""
    RemoveCmdArgs = ""
    ProfilesDir = "/custom-config"

    # Pre-define Devices
    [[DeviceList]]
    Name = "MQ_DEVICE"
    Profile = "Test.Device.MQTT.Profile"
    Description = "General MQTT device"
    Labels = [ "MQTT"]
    [DeviceList.Protocols]
        [DeviceList.Protocols.mqtt]
        Schema = "tcp"
        Host = "172.17.0.1"
        Port = "1883"
        ClientId = "CommandPublisher"
        User = ""
        Password = ""
        Topic = "CommandTopic"
    [[DeviceList.AutoEvents]]
        Frequency = "30s"
        OnChange = false
        Resource = "testrandnum"

    # Driver configs
    [Driver]
    IncomingSchema = "tcp"
    IncomingHost = "172.17.0.1"
    IncomingPort = "1883"
    IncomingUser = ""
    IncomingPassword = ""
    IncomingQos = "0"
    IncomingKeepAlive = "3600"
    IncomingClientId = "IncomingDataSubscriber"
    IncomingTopic = "DataTopic"
    ResponseSchema = "tcp"
    ResponseHost = "172.17.0.1"
    ResponsePort = "1883"
    ResponseUser = ""
    ResponsePassword = ""
    ResponseQos = "0"
    ResponseKeepAlive = "3600"
    ResponseClientId = "CommandResponseSubscriber"
    ResponseTopic = "ResponseTopic"
    ```

    在driver配置中，IncomingXxx定义了DataTopic，用于从设备接收异步值；ResponseXxx定义了ResponseTopic，用于从设备接收命令响应。

2. 设备协议定义在了mqtt.test.device.profile.yml中，描述了设备的值和操作。修改后的内容：

    ```yml
    # mqtt.test.device.profile.yml
    name: "Test.Device.MQTT.Profile"
    manufacturer: "iot"
    model: "MQTT-DEVICE"
    description: "Test device profile"
    labels:
       - "mqtt"
       - "test"
    deviceResources:
        -
            name: randnum
            description: "device random number"
            properties:
            value:
                { type: "Float64", size: "4", readWrite: "R", floatEncoding: "eNotation"  }
            units:
                { type: "String", readWrite: "R", defaultValue: "" }
        -
            name: ping
            description: "device awake"
            properties:
            value:
                { type: "String", size: "0", readWrite: "R", defaultValue: "pong" }
            units:
                { type: "String", readWrite: "R", defaultValue: "" }
        -
            name: message
            description: "device message"
            properties:
            value:
                { type: "String", size: "0", readWrite: "W" ,scale: "", offset: "", base: ""  }
            units:
                { type: "String", readWrite: "R", defaultValue: "" }

    deviceCommands:
        -
            name: testrandnum
            get:
            - { index: "1", operation: "get", object: "randnum", parameter: "randnum" }
        -
            name: testping
            get:
            - { index: "1", operation: "get", object: "ping", parameter: "ping" }
        -
            name: testmessage
            get:
            - { index: "1", operation: "get", object: "message", parameter: "message" }
            set:
            - { index: "1", operation: "set", object: "message", parameter: "message" }

    coreCommands:
        -
            name: testrandnum
            get:
            path: "/api/v1/device/{deviceId}/testrandnum"
            responses:
            -
                code: "200"
                description: "get the random value"
                expectedValues: ["randnum"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: []
        -
            name: testping
            get:
            path: "/api/v1/device/{deviceId}/testping"
            responses:
            -
                code: "200"
                description: "ping the device"
                expectedValues: ["ping"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: []
        -
            name: testmessage
            get:
            path: "/api/v1/device/{deviceId}/testmessage"
            responses:
            -
                code: "200"
                description: "get the message"
                expectedValues: ["message"]
            -
                code: "503"
                description: "service unavailable"
                expectedValues: []
            put:
            path: "/api/v1/device/{deviceId}/testmessage"
            parameterNames: ["message"]
            responses:
            -
                code: "204"
                description: "set the message."
                expectedValues: []
            -
                code: "503"
                description: "service unavailable"
                expectedValues: []

    ```

3. docker-compose.yml文件 <https://github.com/edgexfoundry/developer-scripts/blob/master/compose-files/docker-compose-edinburgh-1.0.0.yml>。 将device-mqtt部分取消注释并改为如下后，再重新运行整个edgex，会把这个服务的容器镜像拉取到本地运行。

    ```yml
    device-mqtt:
        image: edgexfoundry/docker-device-mqtt-go:1.0.0
        ports:
            - "49982:49982"
        container_name: edgex-device-mqtt
        hostname: edgex-device-mqtt
        networks:
            - edgex-network
        volumes:
            - db-data:/data/db
            - log-data:/edgex/logs
            - consul-config:/consul/config
            - consul-data:/consul/data
            - ./mqtt-service-conf:/custom-config
                # ./mqtt-service-conf指的是本地存放configuration.toml和设备协议的文件夹路径
        depends_on:
            - data
            - command
        entrypoint:
            - /device-mqtt
            - --registry=consul://edgex-core-consul:8500
            - --confdir=/custom-config
    ```

#### 1.8.4 容器启动EdgeX

在`device-service-demo`目录下，执行

```shell
docker-compose pull
docker-compose up -d
```

所有服务启动成功后，在`consul`控制台应该能看到所有服务状态正常的显示。

#### 1.8.5 device-mqtt异常退出情况分析

首先注意前面各个配置文件无误，且搞明白各个配置项的含义。

在我的安装记录中，出现全部EdgeX服务（包括device-mqtt）启动后device-mqtt异常退出（Exit(1)）。

首先，在consul ui界面，可以看到device-mqtt是无法连接的，而`docker-compose ps`也可以看出device-mqtt异常退出了。

那么，在mqtt客户端（mock-device.js）、edgeX、mosquitto三者启动后，来检查下device-mqtt、mosquitto的日志。方法是`docker-compose logs -f device-mqtt`、`docker logs -f broker`（broker为mosquitto容器名）。这里就不展示详细日志，直接语言描述。

从mosquitto输出可以看出，`CommandPublisher`、`CommandResponseSubscriber`、`DataSubscriber`三者和mmosquitto建立连接而后断开，然后继续连接继续断开，重复了几次之后就报了error不再尝试。这三个客户端都是device-mqtt服务的configuration.toml定义的，也就是说，device-mqtt曾经尝试从mosquitto中读取内容，但是读不到，尝试一段时间后就觉得异常了，然后device-mqtt就退出了。

这个问题看上去模拟mqtt设备连不上mosquitto导致的啊？

那么要来确认一下这个问题了：我们在eclipse mosquitto官网找到其提供了一个测试broker：`test.mosquitto.org`。现在将device-service-demo文件夹下各项配置文件中涉及broker(这里也就是mosquitto)的地方，以及模拟mqtt设备的docker启动时broker_ip，这些地方全部将`172.17.0.1`替换成`test.mosquitto.org`。将device-mqtt和mqtt-scripts这两个容器删掉（`docker rm -f [container_name]`）再重新运行（`docker run [image_name]`）。

再安装mosquitto客户端软件（`sudo apt install mosquitto-clients`），再在终端执行`mosquitto_sub -h test.mosquitto.org -t 'DataTopic' -v`，可以看到终端每隔一段时间输出一个随机数，说明我们的数据通了。

那么，可以得出结论了：我们的模拟mqtt设备（也就是mqtt-scripts）和device-mqtt是没有问题的，问题出在mqtt-scripts之前运行时不知道mosquitto_ip。这说明我们运行mqtt-scripts时broker_ip没指定好。

如果broker_ip全部严格按172.17.0.1填写，是不会出现这个问题的。这里不再赘述。

#### 1.8.6 执行命令

1. 列举可执行的命令

    ```shell
    curl http://your-edgex-server-ip:48082/api/v1/device | json_pp
    ```

    我的记录是：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ curl http://172.17.0.1:48082/api/v1/device | json_pp
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    100 12214    0 12214    0     0  93953      0 --:--:-- --:--:-- --:--:-- 93953
    [
    {
        "id" : "060d2b57-192d-48bf-809e-38136e0707b9",
        "location" : null,
        "lastReported" : 0,
        "commands" : [
            {
                "id" : "97e2313b-62d6-428d-b614-bca734129078",
                "name" : "GenerateRandomValue_Int8",
                "put" : {
                "parameterNames" : [
                    "Min_Int8",
                    "Max_Int8"
                ],
                "path" : "/api/v1/device/{deviceId}/GenerateRandomValue_Int8",
                "url" : "http://edgex-core-command:48082/api/v1/device/060d2b57-192d-48bf-809e-38136e0707b9/command/97e2313b-62d6-428d-b614-bca734129078"
                },
                "created" : 1566636627378,
                "modified" : 1566636627378,
                "get" : {
                "respomosquitto
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/060d2b57-192d-48bf-809e-38136e0707b9/command/97e2313b-62d6-428d-b614-bca734129078",
                "path" : "/api/v1/device/{deviceId}/GenerateRandomValue_Int8"
                }
            },
            {
                "created" : 1566636627396,
                "modified" : 1566636627396,
                "get" : {
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ],
                "path" : "/api/v1/device/{deviceId}/GenerateRandomValue_Int16",
                "url" : "http://edgex-core-command:48082/api/v1/device/060d2b57-192d-48bf-809e-38136e0707b9/command/184c81ab-fb28-4eab-b6c5-86a21cbf8b99"
                },
                "put" : {
                "path" : "/api/v1/device/{deviceId}/GenerateRandomValue_Int16",
                "parameterNames" : [
                    "Min_Int16",
                    "Max_Int16"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/060d2b57-192d-48bf-809e-38136e0707b9/command/184c81ab-fb28-4eab-b6c5-86a21cbf8b99"
                },
                "id" : "184c81ab-fb28-4eab-b6c5-86a21cbf8b99",
                "name" : "GenerateRandomValue_Int16"
            },
            {
                "put" : {
                "path" : "/api/v1/device/{deviceId}/GenerateRandomValue_Int32",
                "parameterNames" : [
                    "Min_Int32",
                    "Max_Int32"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/060d2b57-192d-48bf-809e-38136e0707b9/command/bfad2b11-b4a7-44e4-b4b7-ca8964279cef"
                },
                "created" : 1566636627397,
                "get" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/060d2b57-192d-48bf-809e-38136e0707b9/command/bfad2b11-b4a7-44e4-b4b7-ca8964279cef",
                "path" : "/api/v1/device/{deviceId}/GenerateRandomValue_Int32",
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ]
                },
                "modified" : 1566636627397,
                "id" : "bfad2b11-b4a7-44e4-b4b7-ca8964279cef",
                "name" : "GenerateRandomValue_Int32"
            }
        ],
        "operatingState" : "ENABLED",
        "name" : "Random-Integer-Generator01",
        "labels" : [
            "device-random-example"
        ],
        "adminState" : "UNLOCKED",
        "lastConnected" : 0
    },
    {
        "location" : null,
        "lastReported" : 0,
        "id" : "7140c607-a620-4417-bf08-785fa14e9af6",
        "operatingState" : "ENABLED",
        "commands" : [
            {
                "name" : "RandomValue_Bool",
                "id" : "7ee6d080-7f50-494e-8ea0-0ec8fb3deeb2",
                "put" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Bool",
                "parameterNames" : [
                    "RandomValue_Bool",
                    "EnableRandomization_Bool"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/7140c607-a620-4417-bf08-785fa14e9af6/command/7ee6d080-7f50-494e-8ea0-0ec8fb3deeb2"
                },
                "modified" : 1566636629592,
                "get" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/7140c607-a620-4417-bf08-785fa14e9af6/command/7ee6d080-7f50-494e-8ea0-0ec8fb3deeb2",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Bool",
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ]
                },
                "created" : 1566636629592
            }
        ],
        "labels" : [
            "device-virtual-example"
        ],
        "name" : "Random-Boolean-Device",
        "adminState" : "UNLOCKED",
        "lastConnected" : 0
    },
    {
        "commands" : [
            {
                "id" : "5e2450ad-d587-4609-9ef9-97a392a2ca0b",
                "name" : "RandomValue_Int8",
                "created" : 1566636629610,
                "get" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int8",
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/5e2450ad-d587-4609-9ef9-97a392a2ca0b",
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ]
                },
                "modified" : 1566636629610,
                "put" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int8",
                "parameterNames" : [
                    "RandomValue_Int8",
                    "EnableRandomization_Int8"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/5e2450ad-d587-4609-9ef9-97a392a2ca0b"
                }
            },
            {
                "id" : "ae11c9c1-3aee-44b4-b87e-44957706310a",
                "name" : "RandomValue_Int16",
                "put" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int16",
                "parameterNames" : [
                    "RandomValue_Int16",
                    "EnableRandomization_Int16"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/ae11c9c1-3aee-44b4-b87e-44957706310a"
                },
                "created" : 1566636629610,
                "modified" : 1566636629610,
                "get" : {
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int16",
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/ae11c9c1-3aee-44b4-b87e-44957706310a"
                }
            },
            {
                "id" : "7b1f87a3-ef44-477b-aadd-f7e9e5185f90",
                "name" : "RandomValue_Int32",
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/7b1f87a3-ef44-477b-aadd-f7e9e5185f90",
                "parameterNames" : [
                    "RandomValue_Int32",
                    "EnableRandomization_Int32"
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int32"
                },
                "created" : 1566636629611,
                "get" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/7b1f87a3-ef44-477b-aadd-f7e9e5185f90",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int32",
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ]
                },
                "modified" : 1566636629611
            },
            {
                "modified" : 1566636629611,
                "get" : {
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/1453ba92-1425-4299-9674-301d0bf762cb",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int64"
                },
                "created" : 1566636629611,
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/b74ca78a-bade-4ba5-9731-bedc7825116e/command/1453ba92-1425-4299-9674-301d0bf762cb",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Int64",
                "parameterNames" : [
                    "RandomValue_Int64",
                    "EnableRandomization_Int64"
                ]
                },
                "name" : "RandomValue_Int64",
                "id" : "1453ba92-1425-4299-9674-301d0bf762cb"
            }
        ],
        "operatingState" : "ENABLED",
        "id" : "b74ca78a-bade-4ba5-9731-bedc7825116e",
        "location" : null,
        "lastReported" : 0,
        "adminState" : "UNLOCKED",
        "lastConnected" : 0,
        "name" : "Random-Integer-Device",
        "labels" : [
            "device-virtual-example"
        ]
    },
    {
        "lastConnected" : 0,
        "adminState" : "UNLOCKED",
        "labels" : [
            "device-virtual-example"
        ],
        "name" : "Random-UnsignedInteger-Device",
        "operatingState" : "ENABLED",
        "commands" : [
            {
                "id" : "f8ffc52d-4620-47a0-b313-856b5bb0dc00",
                "name" : "RandomValue_Uint8",
                "created" : 1566636629632,
                "get" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint8",
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/f8ffc52d-4620-47a0-b313-856b5bb0dc00",
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ]
                },
                "modified" : 1566636629632,
                "put" : {
                "parameterNames" : [
                    "RandomValue_Uint8",
                    "EnableRandomization_Uint8"
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint8",
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/f8ffc52d-4620-47a0-b313-856b5bb0dc00"
                }
            },
            {
                "id" : "5774f8e0-321d-470b-8dbe-6c356e8b0a75",
                "name" : "RandomValue_Uint16",
                "created" : 1566636629632,
                "modified" : 1566636629632,
                "get" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint16",
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/5774f8e0-321d-470b-8dbe-6c356e8b0a75",
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ]
                },
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/5774f8e0-321d-470b-8dbe-6c356e8b0a75",
                "parameterNames" : [
                    "RandomValue_Uint16",
                    "EnableRandomization_Uint16"
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint16"
                }
            },
            {
                "name" : "RandomValue_Uint32",
                "id" : "bedfe744-347a-4388-aec0-27d11070d426",
                "put" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint32",
                "parameterNames" : [
                    "RandomValue_Uint32",
                    "EnableRandomization_Uint32"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/bedfe744-347a-4388-aec0-27d11070d426"
                },
                "modified" : 1566636629633,
                "get" : {
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/bedfe744-347a-4388-aec0-27d11070d426",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint32"
                },
                "created" : 1566636629633
            },
            {
                "created" : 1566636629633,
                "get" : {
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/8a6bd666-dfb2-45d1-8e46-46ae642683a6",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint64"
                },
                "modified" : 1566636629633,
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/8bfa98fc-d26e-49ce-8f01-a3de6c985d9b/command/8a6bd666-dfb2-45d1-8e46-46ae642683a6",
                "parameterNames" : [
                    "RandomValue_Uint64",
                    "EnableRandomization_Uint64"
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Uint64"
                },
                "id" : "8a6bd666-dfb2-45d1-8e46-46ae642683a6",
                "name" : "RandomValue_Uint64"
            }
        ],
        "lastReported" : 0,
        "location" : null,
        "id" : "8bfa98fc-d26e-49ce-8f01-a3de6c985d9b"
    },
    {
        "id" : "c6275969-4b4d-4604-9e75-a8d5c679c53f",
        "lastReported" : 0,
        "location" : null,
        "commands" : [
            {
                "created" : 1566636629599,
                "modified" : 1566636629599,
                "get" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/c6275969-4b4d-4604-9e75-a8d5c679c53f/command/92c2f38f-e374-4515-9315-c980e3128219",
                "path" : "/api/v1/device/{deviceId}/RandomValue_Float32",
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ]
                },
                "put" : {
                "path" : "/api/v1/device/{deviceId}/RandomValue_Float32",
                "parameterNames" : [
                    "RandomValue_Float32",
                    "EnableRandomization_Float32"
                ],
                "url" : "http://edgex-core-command:48082/api/v1/device/c6275969-4b4d-4604-9e75-a8d5c679c53f/command/92c2f38f-e374-4515-9315-c980e3128219"
                },
                "id" : "92c2f38f-e374-4515-9315-c980e3128219",
                "name" : "RandomValue_Float32"
            },
            {
                "name" : "RandomValue_Float64",
                "id" : "c8af4fee-90e8-4175-9602-91fef3522b35",
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/c6275969-4b4d-4604-9e75-a8d5c679c53f/command/c8af4fee-90e8-4175-9602-91fef3522b35",
                "parameterNames" : [
                    "RandomValue_Float64",
                    "EnableRandomization_Float64"
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Float64"
                },
                "modified" : 1566636629599,
                "get" : {
                "responses" : [
                    {
                        "code" : "503",
                        "description" : "service unavailable"
                    }
                ],
                "path" : "/api/v1/device/{deviceId}/RandomValue_Float64",
                "url" : "http://edgex-core-command:48082/api/v1/device/c6275969-4b4d-4604-9e75-a8d5c679c53f/command/c8af4fee-90e8-4175-9602-91fef3522b35"
                },
                "created" : 1566636629599
            }
        ],
        "operatingState" : "ENABLED",
        "name" : "Random-Float-Device",
        "labels" : [
            "device-virtual-example"
        ],
        "lastConnected" : 0,
        "adminState" : "UNLOCKED"
    },
    {
        "commands" : [
            {
                "name" : "testrandnum",
                "id" : "481161d3-73e4-4dd9-bd22-95949ee53b50",
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/a1b1dcb8-2a68-42cd-8467-15a914c5f8c8/command/481161d3-73e4-4dd9-bd22-95949ee53b50"
                },
                "get" : {
                "path" : "/api/v1/device/{deviceId}/testrandnum",
                "url" : "http://edgex-core-command:48082/api/v1/device/a1b1dcb8-2a68-42cd-8467-15a914c5f8c8/command/481161d3-73e4-4dd9-bd22-95949ee53b50",
                "responses" : [
                    {
                        "expectedValues" : [
                            "randnum"
                        ],
                        "description" : "get the random value",
                        "code" : "200"
                    },
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ]
                },
                "modified" : 1566637582556,
                "created" : 1566637582556
            },
            {
                "modified" : 1566637582565,
                "get" : {
                "path" : "/api/v1/device/{deviceId}/testping",
                "url" : "http://edgex-core-command:48082/api/v1/device/a1b1dcb8-2a68-42cd-8467-15a914c5f8c8/command/8e228fd4-b5f0-4481-8b51-a7f4d17fdc0a",
                "responses" : [
                    {
                        "expectedValues" : [
                            "ping"
                        ],
                        "code" : "200",
                        "description" : "ping the device"
                    },
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ]
                },
                "created" : 1566637582565,
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/a1b1dcb8-2a68-42cd-8467-15a914c5f8c8/command/8e228fd4-b5f0-4481-8b51-a7f4d17fdc0a"
                },
                "name" : "testping",
                "id" : "8e228fd4-b5f0-4481-8b51-a7f4d17fdc0a"
            },
            {
                "modified" : 1566637582566,
                "get" : {
                "responses" : [
                    {
                        "description" : "service unavailable",
                        "code" : "503"
                    }
                ],
                "path" : "/api/v1/device/{deviceId}/testmessage",
                "url" : "http://edgex-core-command:48082/api/v1/device/a1b1dcb8-2a68-42cd-8467-15a914c5f8c8/command/9c71513c-1098-4f39-82af-15ec8db90549"
                },
                "created" : 1566637582566,
                "put" : {
                "url" : "http://edgex-core-command:48082/api/v1/device/a1b1dcb8-2a68-42cd-8467-15a914c5f8c8/command/9c71513c-1098-4f39-82af-15ec8db90549",
                "parameterNames" : [
                    "message"
                ],
                "path" : "/api/v1/device/{deviceId}/testmessage"
                },
                "name" : "testmessage",
                "id" : "9c71513c-1098-4f39-82af-15ec8db90549"
            }
        ],
        "operatingState" : "ENABLED",
        "id" : "a1b1dcb8-2a68-42cd-8467-15a914c5f8c8",
        "location" : null,
        "lastReported" : 0,
        "adminState" : "UNLOCKED",
        "lastConnected" : 0,
        "name" : "MQ_DEVICE",
        "labels" : [
            "MQTT"
        ]
    }
    ]
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$

    ```

    这里我的输出信息这么长是因为此时我启动了`device-virtual`、`device-random`、`device-mqtt`三个设备服务，所以根据上面的url请求列举所有连接到这三个服务的设备。最末尾的设备为连接到 `device-mqtt`的设备，也就是我们用来模拟设备的mqtt客户端。

2. 执行put命令

    方法：

    ```shell
    $ curl http://your-edgex-server-ip:48082/api/v1/device/ddb2f5cf-eec2-4345-86ee-f0d87e6f77ff/command/0c257a37-2f72-4d23-b2b1-2c08e895060a \
        -H "Content-Type:application/json" -X PUT  \
        -d '{"message":"Hello!"}'

    # 或者
    $ curl http://your-edgex-server-ip:48082/api/v1/device/name/MQ_DEVICE/command/testmessage -H "Content-Type:application/json" -X PUT -d '{"message":"Hello!"}'
    ```

    我的记录：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$ curl http://172.17.0.1:48082/api/v1/device/name/MQ_DEVICE/command/testmessage -H "Content-Type:application/json" -X PUT -d '{"message":"Hello!"}'

    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry/device-service-demo$
    ```

    这样是不正常的，这是因为我在执行这一步时，遇到了[1.8.5 device-mqtt异常退出](#185-device-mqtt%e5%bc%82%e5%b8%b8%e9%80%80%e5%87%ba%e6%83%85%e5%86%b5%e5%88%86%e6%9e%90)的问题。

    解决完这个问题之后，这一步以及后面的步骤调试我不再进行，因为已明白整个系统运作，后面不再赘述，请参考官方文档，我懒得写了。

3. 执行get命令

    方法：

    ```shell
    curl http://your-edgex-server-ip:48082/api/v1/device/name/MQ_DEVICE/command/testmessage | json_pp
    ```

    我的记录：

    ```shell

    ```

4. 计划任务
5. 异步设备读取

## 2. 用户篇

### 2.1 Getting started

#### 2.1.1 前置条件

- docker
- docker-compose

#### 2.1.2 获取EdgeX Compose文件

- 官方文档翻译：

    安装Docker和Docker Compose后，您需要Docker Compose文件，该文件是所有EdgeX Foundry微服务的清单。 EdgeX Foundry在典型的EdgeX Foundry部署中拥有超过12个微服务，每个服务都部署在自己的Docker容器中。

    如果您了解Docker并了解EdgeX Foundry及其微服务的体系结构，您可以手动发出Docker命令来自行下载和运行每个EdgeX Foundry容器。存在情况，特别是在开发情况下，当您想要进行此手动控制时，即使手动发出命令可能有点单调乏味。如果您需要更多控制下载和运行EdgeX Foundry微服务，本文档集中提供了更多说明。

    如果你有Docker Compose文件指定Docker /Docker Compose你想要哪些容器，以及你想如何运行这些容器，那么获取和运行EdgeX Foundry微服务也可以更轻松地完成。 EdgeX Foundry开发团队通过EdgeX Foundry GitHub存储库为每个版本提供Docker Compose文件。要获取并运行EdgeX Foundry，请访问项目GitHub并将适合您要使用的版本的EdgeX Foundry Docker Compose文件下载（或复制内容）到本地目录。

    可以在此处找到EdgeX Foundry Docker组合文件的集合：https：//github.com/edgexfoundry/developer-scripts/tree/master/compose-files

    请注意，大多数Docker Compose文件在文件名中携带特定的版本标识符（如california-0.6.0）。这些Compose文件有助于获取EdgeX的特定版本。 docker-compose.yml文件将从Docker Hub中提取最新标记的EdgeX微服务。 docker-compose-nexus.yml将从开发人员的Nexus注册表中提取最新的微服务图像，该注册表包含最新构建的工件。这些通常是正在进行中的微服务工件，大多数最终用户不应使用它们。建议您使用最新版本的EdgeX Foundry。在撰写本文时，最新版本为：Edinburgh（1.0.0版）

    Docker Compose文件是一个清单文件，其中列出了：

  - 应该下载的Docker容器（或者更确切地说是Docker容器映像），
  - 应该启动容器的顺序
  - 应运行容器的参数

- 我的做法：

    直接将`github.com/edgexfoundry/developer-scripts`下载到本地。我们要使用的docker-compose.yml位置是`developer-scripts/releases/edinburgh/compose-files/docker-compose-edinburgh-1.0.1.yml`

    将其复制到edgexfoundry目录下并重名为`docker-compose.yml`

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ ls
    developer-scripts  device-sdk-go  device-simple  edgex-go
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ cp developer-scripts/releases/edinburgh/compose-files/docker-compose-edinburgh-1.0.1.yml docker-compose.yml
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ ls
    developer-scripts  device-sdk-go  device-simple  docker-compose.yml  edgex-go
    ```

其内容如下：

```yml
# /*******************************************************************************
#  * Copyright 2018 Dell Inc.
#  *
#  * Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except
#  * in compliance with the License. You may obtain a copy of the License at
#  *
#  * http://www.apache.org/licenses/LICENSE-2.0
#  *
#  * Unless required by applicable law or agreed to in writing, software distributed under the License
#  * is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express
#  * or implied. See the License for the specific language governing permissions and limitations under
#  * the License.
#  *
#  * @author: Jim White, Dell
#  * EdgeX Foundry, Edinburgh, version 1.0.1
#  * added: July 26, 2019
#  *******************************************************************************/

version: '3'
volumes:
  db-data:
  log-data:
  consul-config:
  consul-data:
  portainer_data:
  vault-config:
  vault-file:
  vault-logs:

services:
  volume:
    image: edgexfoundry/docker-edgex-volume:1.0.0
    container_name: edgex-files
    networks:
      edgex-network:
        aliases:
            - edgex-files
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data

  consul:
    image: consul:1.3.1
    ports:
      - "8400:8400"
      - "8500:8500"
      - "8600:8600"
    container_name: edgex-core-consul
    hostname: edgex-core-consul
    networks:
      edgex-network:
        aliases:
            - edgex-core-consul
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - volume

  config-seed:
    image: edgexfoundry/docker-core-config-seed-go:1.0.0
    container_name: edgex-config-seed
    hostname: edgex-core-config-seed
    networks:
      edgex-network:
        aliases:
            - edgex-core-config-seed
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - volume
      - consul

  vault:
    image: edgexfoundry/docker-edgex-vault:1.0.0
    container_name: edgex-vault
    hostname: edgex-vault
    networks:
      edgex-network:
        aliases:
            - edgex-vault
    ports:
      - "8200:8200"
    cap_add:
      - "IPC_LOCK"
    command: "server -log-level=info"
    environment:
      - 'VAULT_ADDR=https://edgex-vault:8200'
      - 'VAULT_CONFIG_DIR=/vault/config'
      - 'VAULT_UI=true'
    volumes:
      - vault-config:/vault/config
      - vault-file:/vault/file
      - vault-logs:/vault/logs
    depends_on:
      - volume
      - consul

  vault-worker:
    image: edgexfoundry/docker-edgex-vault-worker-go:1.0.0
    container_name: edgex-vault-worker
    hostname: edgex-vault-worker
    command: ["--init=true", "--debug=false", "--wait=10", "--insureskipverify=false"]
    networks:
      edgex-network:
        aliases:
            - edgex-vault-worker
    volumes:
      - vault-config:/vault/config
    depends_on:
      - volume
      - consul
      - vault

# containers for reverse proxy
  kong-db:
    image: "postgres:9.6"
    container_name: kong-db
    hostname: kong-db
    networks:
      edgex-network:
        aliases:
            - kong-db
    ports:
        - "5432:5432"
    environment:
        - 'POSTGRES_DB=kong'
        - 'POSTGRES_USER=kong'

  kong-migrations:
    image: "kong:1.0.3"
    container_name: kong-migration
    hostname: kong-migration
    networks:
      edgex-network:
        aliases:
            - kong-migration
    environment:
        - 'KONG_DATABASE=postgres'
        - 'KONG_PG_HOST=kong-db'
    command: "kong migrations bootstrap"

  kong:
    image: "kong:1.0.3"
    container_name: kong
    hostname: kong
    networks:
      edgex-network:
        aliases:
            - kong
    ports:
        - "8000:8000"
        - "8001:8001"
        - "8443:8443"
        - "8444:8444"
    environment:
        - 'KONG_DATABASE=postgres'
        - 'KONG_PG_HOST=kong-db'
        - 'KONG_PROXY_ACCESS_LOG=/dev/stdout'
        - 'KONG_ADMIN_ACCESS_LOG=/dev/stdout'
        - 'KONG_PROXY_ERROR_LOG=/dev/stderr'
        - 'KONG_ADMIN_ERROR_LOG=/dev/stderr'
        - 'KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl'
    depends_on:
        - kong-db

  edgex-proxy:
    image: "edgexfoundry/docker-edgex-proxy-go:1.0.0"
    container_name: edgex-proxy
    hostname: edgex-proxy
    networks:
      edgex-network:
        aliases:
            - edgex-proxy
    volumes:
        - vault-config:/vault/config
    depends_on:
        - vault
        - kong-db
        - kong

# end of containers for reverse proxy

  mongo:
    image: edgexfoundry/docker-edgex-mongo:1.0.1
    ports:
      - "27017:27017"
    container_name: edgex-mongo
    hostname: edgex-mongo
    networks:
      edgex-network:
        aliases:
            - edgex-mongo
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - volume

  logging:
    image: edgexfoundry/docker-support-logging-go:1.0.1
    ports:
      - "48061:48061"
    container_name: edgex-support-logging
    hostname: edgex-support-logging
    networks:
      edgex-network:
        aliases:
            - edgex-support-logging
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - config-seed
      - mongo
      - volume

  system:
    image: edgexfoundry/docker-sys-mgmt-agent-go:1.0.1
    ports:
      - "48090:48090"
    container_name: edgex-sys-mgmt-agent
    hostname: edgex-sys-mgmt-agent
    networks:
      edgex-network:
        aliases:
            - edgex-sys-mgmt-agent
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - logging

  notifications:
    image: edgexfoundry/docker-support-notifications-go:1.0.1
    ports:
      - "48060:48060"
    container_name: edgex-support-notifications
    hostname: edgex-support-notifications
    networks:
      edgex-network:
        aliases:
            - edgex-support-notifications
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - logging

  metadata:
    image: edgexfoundry/docker-core-metadata-go:1.0.1
    ports:
      - "48081:48081"
    container_name: edgex-core-metadata
    hostname: edgex-core-metadata
    networks:
      edgex-network:
        aliases:
            - edgex-core-metadata
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - logging

  data:
    image: edgexfoundry/docker-core-data-go:1.0.1
    ports:
      - "48080:48080"
      - "5563:5563"
    container_name: edgex-core-data
    hostname: edgex-core-data
    networks:
      edgex-network:
        aliases:
            - edgex-core-data
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - logging

  command:
    image: edgexfoundry/docker-core-command-go:1.0.1
    ports:
      - "48082:48082"
    container_name: edgex-core-command
    hostname: edgex-core-command
    networks:
      edgex-network:
        aliases:
            - edgex-core-command
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - metadata

  scheduler:
    image: edgexfoundry/docker-support-scheduler-go:1.0.1
    ports:
      - "48085:48085"
    container_name: edgex-support-scheduler
    hostname: edgex-support-scheduler
    networks:
      edgex-network:
        aliases:
            - edgex-support-scheduler
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - metadata

  export-client:
    image: edgexfoundry/docker-export-client-go:1.0.1
    ports:
      - "48071:48071"
    container_name: edgex-export-client
    hostname: edgex-export-client
    networks:
      edgex-network:
        aliases:
            - edgex-export-client
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - data

  export-distro:
    image: edgexfoundry/docker-export-distro-go:1.0.1
    ports:
      - "48070:48070"
      - "5566:5566"
    container_name: edgex-export-distro
    hostname: edgex-export-distro
    networks:
      edgex-network:
        aliases:
            - edgex-export-distro
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - export-client
    environment:
      - EXPORT_DISTRO_CLIENT_HOST=export-client
      - EXPORT_DISTRO_DATA_HOST=edgex-core-data
      - EXPORT_DISTRO_CONSUL_HOST=edgex-config-seed
      - EXPORT_DISTRO_MQTTS_CERT_FILE=none
      - EXPORT_DISTRO_MQTTS_KEY_FILE=none

  rulesengine:
    image: edgexfoundry/docker-support-rulesengine:1.0.0
    ports:
      - "48075:48075"
    container_name: edgex-support-rulesengine
    hostname: edgex-support-rulesengine
    networks:
      edgex-network:
        aliases:
            - edgex-support-rulesengine
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - export-client

#################################################################
# Device Services
#################################################################

  device-virtual:
    image: edgexfoundry/docker-device-virtual-go:1.0.0
    ports:
    - "49990:49990"
    container_name: edgex-device-virtual
    hostname: edgex-device-virtual
    networks:
      edgex-network:
        aliases:
        - edgex-device-virtual
    volumes:
    - db-data:/data/db
    - log-data:/edgex/logs
    - consul-config:/consul/config
    - consul-data:/consul/data
    depends_on:
    - data
    - command

  # device-random:
  #   image: edgexfoundry/docker-device-random-go:1.0.0
  #   ports:
  #     - "49988:49988"
  #   container_name: edgex-device-random
  #   hostname: edgex-device-random
  #   networks:
  #     edgex-network:
  #       aliases:
  #           - edgex-device-random
  #   volumes:
  #     - db-data:/data/db
  #     - log-data:/edgex/logs
  #     - consul-config:/consul/config
  #     - consul-data:/consul/data
  #   depends_on:
  #     - data
  #     - command
  #
  # device-mqtt:
  #   image: edgexfoundry/docker-device-mqtt-go:1.0.0
  #   ports:
  #     - "49982:49982"
  #   container_name: edgex-device-mqtt
  #   hostname: edgex-device-mqtt
  #   networks:
  #     edgex-network:
  #       aliases:
  #           - edgex-device-mqtt
  #   volumes:
  #     - db-data:/data/db
  #     - log-data:/edgex/logs
  #     - consul-config:/consul/config
  #     - consul-data:/consul/data
  #   depends_on:
  #     - data
  #     - command
  #
  # device-modbus:
  #   image: edgexfoundry/docker-device-modbus-go:1.0.0
  #   ports:
  #     - "49991:49991"
  #   container_name: edgex-device-modbus
  #   hostname: edgex-device-modbus
  #   networks:
  #     edgex-network:
  #       aliases:
  #           - edgex-device-modbus
  #   volumes:
  #     - db-data:/data/db
  #     - log-data:/edgex/logs
  #     - consul-config:/consul/config
  #     - consul-data:/consul/data
  #   depends_on:
  #     - data
  #     - command
  #
  # device-snmp:
  #   image: edgexfoundry/docker-device-snmp-go:1.0.0
  #   ports:
  #     - "49993:49993"
  #   container_name: edgex-device-snmp
  #   hostname: edgex-device-snmp
  #   networks:
  #     edgex-network:
  #       aliases:
  #           - edgex-device-snmp
  #   volumes:
  #     - db-data:/data/db
  #     - log-data:/edgex/logs
  #     - consul-config:/consul/config
  #     - consul-data:/consul/data
  #   depends_on:
  #     - data
  #     - command

#################################################################
# UIs
#################################################################
  ui:
    image: edgexfoundry/docker-edgex-ui-go:1.0.0
    ports:
      - "4000:4000"
    container_name: edgex-ui-go
    hostname: edgex-ui-go
    networks:
      edgex-network:
        aliases:
            - edgex-ui-go
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
    depends_on:
      - data
      - command

#################################################################
# Tooling
#################################################################

  portainer:
    image:  portainer/portainer
    ports:
      - "9000:9000"
    command: -H unix:///var/run/docker.sock
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    depends_on:
      - volume

networks:
  edgex-network:
    driver: "bridge"
...
```

#### 2.1.3 运行EdgeX

- 官方文档翻译：

    现在您已拥有EdgeX Foundry Docker Compose文件，您已准备好运行EdgeX Foundry。按照以下步骤获取容器图像并启动EdgeX Foundry！

    首先，除非您下载了docker-compose.yml，否则请将您下载的Docker Compose文件重命名为“docker-compose.yml”。默认情况下，Docker Compose在运行所有Docker Compose命令时会按此名称查找文件。您可以使用原始文件名，但需要向Docker Compose命令添加更多参数，并为错误创建更多情况。

    接下来，打开Docker Compose文件位置的命令终端 -您可以在其中下载上面的EdgeX Foundry docker-compose.yml文件。在某些操作系统上，有一个特殊的Docker终端。在其他平台上，Docker和Docker Compose可以从普通的终端窗口运行。有关在平台上运行Docker和Docker Compose命令的更多帮助，请参阅Docker文档。

    现在在终端中运行以下命令，将所有EdgeX Docker映像拉到（但不要启动）到您的系统：

    docker-compose pull

    拉出Docker镜像后，在终端中使用此命令启动EdgeX：

    docker-compose up -d

    -d选项表示您希望Docker Compose以分离模式运行EdgeX容器 -即在后台运行容器。如果没有-d，容器将全部从终端开始，并进一步使用终端，您必须停止容器。

    在某些情况下，您可能希望使用Docker Compose一次调出一个容器。如果您是开发人员或者您不想调出所有EdgeX（可能是因为您只使用了一些服务），您可以发出Docker Compose命令来分别拉动和启动每个EdgeX容器。因为某些容器依赖于其他容器，所以您应该尝试按特定顺序启动它们。下表提供了按照应该启动的顺序启动每个EdgeX微服务的命令（基于它们之间的依赖关系）。基本上，单个Docker Compose up命令（docker-compose up -d）使用清单按顺序运行此处列出的所有单个up命令。

    |Docker Command|Description|Notes
    |----|----|----|
    |docker-compose pull|Pull down, but don’t start, all the EdgeX Foundry microservices|Docker Compose will indicate when all the containers have been pulled successfully|
    |docker-compose up -d volume|Start the EdgeX Foundry file volume–must be done before the other services are started||
    |docker-compose up -d consul|Start the configuration and registry microservice which all services must register with and get their configuration from||
    |docker-compose up -d config-seed|Populate the configuration/registry microservice||
    |docker-compose up -d mongo|Start the NoSQL MongoDB container|An embedded initialization script configures the database for EdgeX documents|
    |docker-compose up -d logging|Start the logging microservice - used by all micro services that make log entries||
    |docker-compose up -d notifications|Start the notifications and alerts microservice–used by many of the microservices||
    |docker-compose up -d metadata|Start the Core Metadata microservice||
    |docker-compose up -d data|Start the Core Data microservice||
    |docker-compose up -d command|Start the Core Command microservice||
    |docker-compose up -d scheduler|Start the scheduling microservice -used by many of the microservices||
    |docker-compose up -d export-client|Start the Export Client registration microservice||
    |docker-compose up -d export-distro|Start the Export Distribution microservice||
    |docker-compose up -d rulesengine|Start the Rules Engine microservice|This service is still implemented in Java and takes more time to start|
    |docker-compose up -d device-virtual|Start the virtual device service. This service mocks a sensor sending data to EdgX and is used for demonstration||

    使用`docker-compose ps`可以根据当前路径下`docker-compose.yml`文件检查文件所描述的容器的下载和启动状态，其中`config-seed`等含有`seed`字眼的通常需要第一个启动并且在所有服务启动后正常退出。

- **注意**

    `docker-compose pull`过程中因为网络原因可能部分失败，多次重试即可，或者`docker-compose pull [compose-container-name]`来拉取下载失败的容器镜像。

    当拉取完成后，执行`docker-compose up -d`，如果发现部分出错，基本都是存储服务所用的数据库，在本机真实环境装了，造成了端口占用。解决办法就是把真实环境中数据库进程关掉。例如：

    ```shell
    # kong-db和mongo端口被真实环境数据库占用了
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ docker-compose up -d
    Starting kong-migration ...
    Starting kong-db        ...
    edgex-files is up-to-date
    edgex-core-consul is up-to-date
    edgexfoundry_portainer_1 is up-to-date
    Starting kong-db            ... error
    Starting edgex-config-seed ...
    edgex-vault is up-to-date
    Starting edgex-vault-worker ...

    Starting edgex-mongo        ... error
    02704cc8e160b774c2731a120c47389b1fb6c71761b9460fbd2ad3381): Error starting userland proxy: listen tcp 0.0.0.0:5432: bind: addrStarting kong-migration     ... done

    ERROR: for edgex-mongo  Cannot start service mongo: driver failed programming external connectivity on endpoint edgex-mongo (cStarting edgex-config-seed  ... done
    Starting edgex-vault-worker ... done

    ERROR: for kong-db  Cannot start service kong-db: driver failed programming external connectivity on endpoint kong-db (d1b7dc902704cc8e160b774c2731a120c47389b1fb6c71761b9460fbd2ad3381): Error starting userland proxy: listen tcp 0.0.0.0:5432: bind: address already in use

    ERROR: for mongo  Cannot start service mongo: driver failed programming external connectivity on endpoint edgex-mongo (c20ef4cd8dc3c185fdfa99bbef9bf014243834fea7c705bc626172ff95e63180): Error starting userland proxy: listen tcp 0.0.0.0:27017: bind: address already in use
    ERROR: Encountered errors while bringing up the project.

    # 查看端口被哪个进程占用
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ sudo netstat -lnp | grep 5432
    [sudo] password for eiger:
    tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      1398/postgres
    unix  2      [ ACC ]     STREAM     LISTENING     28627    1398/postgres        /var/run/postgresql/.s.PGSQL.5432
    # 强制删除对应进程
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ sudo kill -9 1398
    # 操作同上
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ sudo netstat -lnp | grep 27017
    tcp        0      0 127.0.0.1:27017         0.0.0.0:*               LISTEN      1296/mongod
    unix  2      [ ACC ]     STREAM     LISTENING     29752    1296/mongod          /run/mongodb/mongodb-27017.sock
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ sudo kill -9 1296
    # 重新运行edgex，现在没问题了
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/edgexfoundry$ docker-compose up -d
    edgex-files is up-to-date
    Starting kong-migration ...
    Starting kong-db        ...
    Starting edgex-mongo    ...
    Starting kong-migration     ... done
    Starting kong-db            ... done
    Starting edgex-mongo        ... done
    Starting edgex-config-seed  ... done
    Starting edgex-vault-worker ... done
    Creating kong               ... done
    Creating edgex-proxy           ... done
    Creating edgex-support-logging ... done
    Creating edgex-core-metadata         ... done
    Creating edgex-core-data             ... done
    Creating edgex-sys-mgmt-agent        ... done
    Creating edgex-support-notifications ... done
    Creating edgex-support-scheduler     ... done
    Creating edgex-core-command          ... done
    Creating edgex-export-client         ... done
    Creating edgex-ui-go                 ... done
    Creating edgex-device-virtual        ... done
    Creating edgex-support-rulesengine   ... done
    Creating edgex-export-distro         ... done
    ```

#### 2.1.4 docker-compose命令

- `docker-compose pull` - 根据当前目录下docker-compose.yml拉取容器镜像
- `docker-compose pull [compose-container-name]`
- `docker-compose config --services` - 列举文件所描述的所有服务名
- `docker-compose ps` - 查看文件描述的所有容器其下载和启动状态
- `docker-compose start` - 启动/重启/重建重启所有服务容器
- `docker-compose start [compose-container-name]`
- `docker-compose stop` - 停止所有容器，但不移除，使用`docker ps -a`仍可查看容器
- `docker-compose stop [compose-container-name]`
- `docker-compose down` - 停止所有正在运行的容器并且删除容器文件
- `docker-compose logs -f [compose-container-name]` - 查看服务运行日志

- 额外：
  - `docker ps -a`
  - `docker ps -a --format “table {{.Names}}t{{.Status}}t{{.Ports}}t{{.RunningFor}}` - 自定义显示格式

#### 2.1.5 微服务Ping检查

EdgeX微服务都内置了Ping响应处理，可以对Ping http请求作出响应，这可以作为微服务的可用性/可达性的检测手段。

|EdgeX Foundry Microservice|Docker Compose Container|Container Name|Port|Ping URL
|---|---|---|---|---|
|Core Command|command|edgex-core-command|48082|<http://[host]:48082/api/v1/ping>|
|Core Data|data|edgex-core-data|48080|<http://[host]:48080/api/v1/ping>|
|Core Metadata|metadata|edgex-core-metadata|48081|<http://[host]:48081/api/v1/ping>|
|Export Client|export-client|edgex-export-client|48071|<http://[host]:48071/api/v1/ping>|
|Export Distribution|export-distro|edgex-export-distro|48070|<http://[host]:48070/api/v1/ping>|
|Rules Engine|rulesengine|edgex-support-rulesengine|48075|<http://[host]:48075/api/v1/ping>|
|Support Logging|logging|edgex-support-logging|48061|<http://[host]:48061/api/v1/ping>|
|Support Notifications|notifications|edgex-support-notifications|48060|<http://[host]:48060/api/v1/ping>|
|Support Scheduler|scheduler|edgex-support-scheduler|48085|<http://[host]:48085/api/v1/ping>|
|Virtual Device Service|device-virtual|edgex-device-virtual|49990|<http://[host]:49990/api/v1/ping>|

`[host]`取决于docker引擎。在我的设备上它是`172.17.0.1`或者`172.18.0.1`。也可以通过`ifconfig`命令查看本机地址信息来找出docker的虚拟主机地址。

#### 2.1.6 Consul服务注册

EdgeX采用Consul作为服务注册服务。其余每个微服务启动之后会在Consul中进行注册，并可在Consul操作台面板进行查看。ui地址： <http://[host]:8500/ui>
