---
title: "安装mainflux全系列"
date: 2019-08-17T02:47:50+08:00
draft: false
categories: ["iot"]
tags: ["mainflux"]
keywords: ["mainflux", "docker", "protoc"]
---

# 安装mainflux

- [安装mainflux](#%e5%ae%89%e8%a3%85mainflux)
  - [1.环境准备](#1%e7%8e%af%e5%a2%83%e5%87%86%e5%a4%87)
  - [2.protoc安装](#2protoc%e5%ae%89%e8%a3%85)
  - [3. GNU MAKE安装](#3-gnu-make%e5%ae%89%e8%a3%85)
  - [4. mainflux编译](#4-mainflux%e7%bc%96%e8%af%91)
    - [4.1 获取mainflux源码](#41-%e8%8e%b7%e5%8f%96mainflux%e6%ba%90%e7%a0%81)
    - [4.2. 编译所有服务](#42-%e7%bc%96%e8%af%91%e6%89%80%e6%9c%89%e6%9c%8d%e5%8a%a1)
    - [4.3. 编译单个微服务](#43-%e7%bc%96%e8%af%91%e5%8d%95%e4%b8%aa%e5%be%ae%e6%9c%8d%e5%8a%a1)
    - [4.4. 编译dockers](#44-%e7%bc%96%e8%af%91dockers)
    - [4.5. 其他docker安装相关](#45-%e5%85%b6%e4%bb%96docker%e5%ae%89%e8%a3%85%e7%9b%b8%e5%85%b3)
    - [4.6. mqtt微服务](#46-mqtt%e5%be%ae%e6%9c%8d%e5%8a%a1)
    - [4.7. 其他](#47-%e5%85%b6%e4%bb%96)
  - [5. 安装node.js和npm](#5-%e5%ae%89%e8%a3%85nodejs%e5%92%8cnpm)
    - [5.1. 解决npm安装ajv-keywords缺少配对问题](#51-%e8%a7%a3%e5%86%b3npm%e5%ae%89%e8%a3%85ajv-keywords%e7%bc%ba%e5%b0%91%e9%85%8d%e5%af%b9%e9%97%ae%e9%a2%98)
    - [5.2. 解决node-gyp提示找不到python](#52-%e8%a7%a3%e5%86%b3node-gyp%e6%8f%90%e7%a4%ba%e6%89%be%e4%b8%8d%e5%88%b0python)
  - [6. 安装Docker](#6-%e5%ae%89%e8%a3%85docker)
    - [6.1. `<none>`镜像的处理](#61-none%e9%95%9c%e5%83%8f%e7%9a%84%e5%a4%84%e7%90%86)
  - [7. mainflux运行测试](#7-mainflux%e8%bf%90%e8%a1%8c%e6%b5%8b%e8%af%95)
  - [8. mainflux安装](#8-mainflux%e5%ae%89%e8%a3%85)
  - [9. mainflux部署](#9-mainflux%e9%83%a8%e7%bd%b2)
    - [9.1 前提](#91-%e5%89%8d%e6%8f%90)
  - [10. postgres、cassandra、nats安装](#10-postgrescassandranats%e5%ae%89%e8%a3%85)
    - [10.1.安装postgres](#101%e5%ae%89%e8%a3%85postgres)
    - [10.2. 安装cassandra](#102-%e5%ae%89%e8%a3%85cassandra)
    - [10.3. 安装nats](#103-%e5%ae%89%e8%a3%85nats)
    - [10.4. 安装Java开发环境（Java 12）](#104-%e5%ae%89%e8%a3%85java%e5%bc%80%e5%8f%91%e7%8e%af%e5%a2%83java-12)
    - [10.5. 安装Java开发环境（Java 8）](#105-%e5%ae%89%e8%a3%85java%e5%bc%80%e5%8f%91%e7%8e%af%e5%a2%83java-8)
  - [11. 其他一些问题](#11-%e5%85%b6%e4%bb%96%e4%b8%80%e4%ba%9b%e9%97%ae%e9%a2%98)
    - [11.1. sudo+xxx:找不到xxx命令](#111-sudoxxx%e6%89%be%e4%b8%8d%e5%88%b0xxx%e5%91%bd%e4%bb%a4)
  - [12. Getting started](#12-getting-started)
    - [12.1 运行整个系统](#121-%e8%bf%90%e8%a1%8c%e6%95%b4%e4%b8%aa%e7%b3%bb%e7%bb%9f)
    - [12.2 安装mainflux-cli](#122-%e5%ae%89%e8%a3%85mainflux-cli)
    - [12.3 系统测试配置（Provisioning）](#123-%e7%b3%bb%e7%bb%9f%e6%b5%8b%e8%af%95%e9%85%8d%e7%bd%aeprovisioning)
    - [12.4 发送消息测试](#124-%e5%8f%91%e9%80%81%e6%b6%88%e6%81%af%e6%b5%8b%e8%af%95)
  - [13. mainflux架构、概念](#13-mainflux%e6%9e%b6%e6%9e%84%e6%a6%82%e5%bf%b5)
    - [13.1 架构](#131-%e6%9e%b6%e6%9e%84)
    - [13.2 Provisioning](#132-provisioning)
      - [13.2.1 用户管理](#1321-%e7%94%a8%e6%88%b7%e7%ae%a1%e7%90%86)
      - [13.2.2 系统配置（provision）](#1322-%e7%b3%bb%e7%bb%9f%e9%85%8d%e7%bd%aeprovision)
      - [13.2.3 访问控制](#1323-%e8%ae%bf%e9%97%ae%e6%8e%a7%e5%88%b6)
    - [13.3 Messaging](#133-messaging)
      - [13.3.1 http](#1331-http)
      - [13.3.2 MQTT](#1332-mqtt)
    - [13.4 Storage](#134-storage)
      - [13.4.1 Writers](#1341-writers)
      - [13.4.2 Readers](#1342-readers)
      - [13.4.2 Reader](#1342-reader)
    - [13.5 LoRa](#135-lora)
    - [13.6 安全通信](#136-%e5%ae%89%e5%85%a8%e9%80%9a%e4%bf%a1)
      - [13.6.1 安全的PostgreSQL连接](#1361-%e5%ae%89%e5%85%a8%e7%9a%84postgresql%e8%bf%9e%e6%8e%a5)
      - [13.6.2 安全的gRPC](#1362-%e5%ae%89%e5%85%a8%e7%9a%84grpc)
    - [13.7 Authentication（认证）](#137-authentication%e8%ae%a4%e8%af%81)
      - [13.7.1 使用Mainflux秘钥认证](#1371-%e4%bd%bf%e7%94%a8mainflux%e7%a7%98%e9%92%a5%e8%ae%a4%e8%af%81)
      - [13.7.2 使用X.509证书进行相互TLS认证](#1372-%e4%bd%bf%e7%94%a8x509%e8%af%81%e4%b9%a6%e8%bf%9b%e8%a1%8c%e7%9b%b8%e4%ba%92tls%e8%ae%a4%e8%af%81)
    - [13.8 CLI](#138-cli)
    - [13.9 Bootstrap（引导）](#139-bootstrap%e5%bc%95%e5%af%bc)
  - [14. mainflux应用](#14-mainflux%e5%ba%94%e7%94%a8)
    - [14.1 连接一个mqtt设备](#141-%e8%bf%9e%e6%8e%a5%e4%b8%80%e4%b8%aamqtt%e8%ae%be%e5%a4%87)

**注意！！！**

- 1-11节记录依据mainflux开发者文档手动编译、构建、部署的过程。官方开发者文档： <https://mainflux.readthedocs.io/en/latest/dev-guide/>
- 12节记录依据官方文档运行mainflux的过程，此后章节记录mainflux的进一步应用及业务实践。<https://mainflux.readthedocs.io/en/latest/getting-started/>

## 1.环境准备

- Ubuntu18.04
- GO 1.12.7（其他不太低的版本也可以）
- 安装protoc（protocol buffers编译器）
- 安装GNU Make
- 安装Docker CE

注意：mainflux使用go protobuf，但go protobuf又依赖于C++ protobuf。

## 2.protoc安装

1. protoc(protobuf compiler)安装

    源码安装就算了，选择二进制安装：

    <https://github.com/protocolbuffers/protobuf/releases>

    选择protobuf-3.9.1-linux-x86_64.tar.gz。将之解压后将可执行文件置于PATH目录下即可。我的操作是：

    ```shell
    sudo cp -r protobuf-3.9.1-linux-x86_64 /usr/local ## 将解压后的整个文件夹复制到/usr/local/下
    sudo nano /etc/profile  ## 追加export PATH=$PATH:/usr/local/protobuf-3.9.1-linux-x86_64/bin
    source /etc/profile  ## 使添加的环境变量立即生效，否则需要重启生效
    ```

    （ps: 如果不是编辑/etc/profile而是编辑$HOME/.profile，则可能需要执行`sudo chmod a+x protoc`，提升所有用户对protoc的执行权限）

    此时，在终端输入`protoc --version`可看到其版本信息，说明protobuf编译器安装成功。

2. 安装go protobuf

    终端输入

    ```shell
    go get -u github.com/golang/protobuf/protoc-gen-go
    ```

    安装完成后会在$GOPATH/bin下产生protoc-gen-go可执行文件。
    由于mainflux使用的是Protocol Buffers for Go with Gadgets，其安装参考<https://github.com/gogo/protobuf>。只需在终端输入：

    ```shell
    go get github.com/gogo/protobuf/protoc-gen-gofast
    ```

    安装完成后会在$GOPATH/bin下产生protoc-gen-gofast可执行文件。

    注意：记得将第一个$GOPATH的bin子目录设为PATH或者GOBIN（go get得到的程序默认安装在此）。

## 3. GNU MAKE安装

Ubuntu默认仓库中有一个Build-essential元包，包含了gcc、make等一系列编译所需的软件。我们所需要的GNU MAKE就是这个包里的make。windows平台上的make可以通过Visual Studio集成的make工具或者是MinGW中的make工具。

在Ubuntu上安装build-essential:

```shell
sudo apt update
sudo apt install build-essential
```

安装完成后输入`make --version`可以进行验证。

## 4. mainflux编译

### 4.1 获取mainflux源码

```shell
go get github.com/mainflux/mainflux
cd $GOPATH/src/github.com/mainflux/mainflux
```

现在mainflux源码被拉取到了$GOPATH/src/github.com/mainflux/mainflux下
（ps: 其实这和直接使用git clone来拉取效果是一样的。只不过放的位置不同）

### 4.2. 编译所有服务

mainflux是一个微服务架构，所以可以在其/cmd目录下看到很多个main函数（每一个文件代表一个微服务）。

切换到mainflux根目录下（如前已达成此操作），执行

```shell
make
```

即可编译所有微服务。

**注意：在编译过程中应关注/mainflux/Makefile文件，其中定义了编译选项和行为！**

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-users cmd/users/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-things cmd/things/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-http cmd/http/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-normalizer cmd/normalizer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-ws cmd/ws/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-coap cmd/coap/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-lora cmd/lora/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-influxdb-writer cmd/influxdb-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-influxdb-reader cmd/influxdb-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-mongodb-writer cmd/mongodb-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-mongodb-reader cmd/mongodb-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cassandra-writer cmd/cassandra-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cassandra-reader cmd/cassandra-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-postgres-writer cmd/postgres-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-postgres-reader cmd/postgres-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cli cmd/cli/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-bootstrap cmd/bootstrap/main.go
cd mqtt/aedes && npm install
/bin/sh: 1: npm: not found
Makefile:102: recipe for target 'mqtt' failed
make: *** [mqtt] Error 127
```

从执行结果可以看出在编译/cmd下的所有微服务。进入到/mainflux/build文件夹下，可以看到编译出来的所有微服务或者说可执行文件。

但是最后报错，提示没有npm，所以下载再安装node.js先（npm是node.js的包管理工具），参考session 5(安装node.js和npm)

安装完npm之后，重新执行make:

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-users cmd/users/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-things cmd/things/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-http cmd/http/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-normalizer cmd/normalizer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-ws cmd/ws/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-coap cmd/coap/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-lora cmd/lora/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-influxdb-writer cmd/influxdb-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-influxdb-reader cmd/influxdb-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-mongodb-writer cmd/mongodb-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-mongodb-reader cmd/mongodb-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cassandra-writer cmd/cassandra-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cassandra-reader cmd/cassandra-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-postgres-writer cmd/postgres-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-postgres-reader cmd/postgres-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cli cmd/cli/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-bootstrap cmd/bootstrap/main.go
cd mqtt/aedes && npm install
npm WARN deprecated circular-json@0.3.3: CircularJSON is in maintenance only, flatted is its successor.
npm WARN deprecated superagent@3.8.3: Please note that v5.0.1+ of superagent removes User-Agent header by default, therefore you may need to add it yourself (e.g. GitHub blocks requests without a User-Agent header).  This notice will go away with v5.0.2+ once it is released.

> dtrace-provider@0.8.7 install /home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes/node_modules/dtrace-provider
> node-gyp rebuild || node suppress-error.js

make[1]: Entering directory '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes/node_modules/dtrace-provider/build'
  TOUCH Release/obj.target/DTraceProviderStub.stamp
make[1]: Leaving directory '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes/node_modules/dtrace-provider/build'

> grpc@1.22.2 install /home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes/node_modules/grpc
> node-pre-gyp install --fallback-to-build --library=static_library

node-pre-gyp WARN Using request for node-pre-gyp https download
[grpc] Success: "/home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes/node_modules/grpc/src/node/extension_binary/node-v57-linux-x64-glibc/grpc_node.node" is installed via remote

> protobufjs@6.8.8 postinstall /home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes/node_modules/protobufjs
> node scripts/postinstall

mqtt-adapter@ /home/eiger/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes
├── 2@1.0.2
├─┬ @grpc/proto-loader@0.5.1
│ └── lodash.camelcase@4.3.0
├─┬ aedes@0.38.1
│ ├── aedes-packet@1.0.0
│ ├─┬ aedes-persistence@6.0.0
│ │ └── qlobber@3.1.0
│ ├─┬ bulk-write-stream@2.0.0
│ │ └── readable-stream@3.4.0
│ ├─┬ end-of-stream@1.4.1
│ │ └─┬ once@1.4.0
│ │   └── wrappy@1.0.2
│ ├── fastfall@1.5.1
│ ├── fastparallel@2.3.0
│ ├── fastseries@1.7.2
│ ├── from2@2.3.0
│ ├─┬ mqemitter@2.2.0
│ │ └── qlobber@1.8.0
│ ├─┬ mqtt-packet@6.2.1
│ │ ├── bl@1.2.2
│ │ └── process-nextick-args@2.0.1
│ ├── pump@3.0.0
│ ├── retimer@2.0.0
│ ├── reusify@1.0.4
│ ├─┬ shortid@2.2.14
│ │ └── nanoid@2.0.3
│ ├── through2@3.0.1
│ ├── uuid@3.3.2
│ └─┬ why-is-node-running@2.1.0
│   └── stackback@0.0.2
├─┬ aedes-logging@2.0.1
│ ├─┬ pino@4.17.6
│ │ ├── fast-json-parse@1.0.3
│ │ ├── fast-safe-stringify@1.2.3
│ │ ├── flatstr@1.0.12
│ │ ├── pino-std-serializers@2.4.2
│ │ ├── quick-format-unescaped@1.1.2
│ │ └─┬ split2@2.2.0
│ │   └── through2@2.0.5
│ └── safe-buffer@5.1.2
├─┬ aedes-persistence-redis@5.1.0
│ ├─┬ aedes-cached-persistence@6.0.0
│ │ ├── aedes-persistence@5.1.1
│ │ ├── multistream@2.1.1
│ │ └── qlobber@1.8.0
│ ├── hashlru@2.3.0
│ ├─┬ ioredis@3.2.2
│ │ ├── bluebird@3.5.5
│ │ ├── cluster-key-slot@1.1.0
│ │ ├── denque@1.4.1
│ │ ├── flexbuffer@0.0.6
│ │ ├── lodash.assign@4.2.0
│ │ ├── lodash.bind@4.2.1
│ │ ├── lodash.clonedeep@4.5.0
│ │ ├── lodash.defaults@4.2.0
│ │ ├── lodash.difference@4.5.0
│ │ ├── lodash.flatten@4.4.0
│ │ ├── lodash.foreach@4.5.0
│ │ ├── lodash.isempty@4.4.0
│ │ ├── lodash.keys@4.2.0
│ │ ├── lodash.noop@3.0.1
│ │ ├── lodash.partial@4.2.1
│ │ ├── lodash.pick@4.4.0
│ │ ├── lodash.sample@4.2.1
│ │ ├── lodash.shuffle@4.2.0
│ │ └── lodash.values@4.3.0
│ ├─┬ msgpack-lite@0.1.26
│ │ ├── event-lite@0.1.2
│ │ ├── ieee754@1.1.13
│ │ ├── int64-buffer@0.1.10
│ │ └── isarray@1.0.0
│ ├── pump@1.0.3
│ ├── qlobber@1.8.0
│ ├── through2@2.0.5
│ └── throughv@1.0.4
├── atob@2.1.2
├─┬ bunyan@1.8.12
│ ├── dtrace-provider@0.8.7
│ ├── moment@2.24.0
│ ├─┬ mv@2.1.1
│ │ ├── ncp@2.0.0
│ │ └─┬ rimraf@2.4.5
│ │   └── glob@6.0.4
│ └── safe-json-stringify@1.2.0
├─┬ chai@3.5.0
│ ├── assertion-error@1.1.0
│ ├─┬ deep-eql@0.1.3
│ │ └── type-detect@0.1.1
│ └── type-detect@1.0.0
├─┬ eslint@4.19.1
│ ├─┬ ajv@5.5.2
│ │ ├── co@4.6.0
│ │ ├── fast-deep-equal@1.1.0
│ │ ├── fast-json-stable-stringify@2.0.0
│ │ └── json-schema-traverse@0.3.1
│ ├─┬ babel-code-frame@6.26.0
│ │ ├─┬ chalk@1.1.3
│ │ │ ├── ansi-styles@2.2.1
│ │ │ └── supports-color@2.0.0
│ │ └── js-tokens@3.0.2
│ ├─┬ chalk@2.4.2
│ │ ├─┬ ansi-styles@3.2.1
│ │ │ └─┬ color-convert@1.9.3
│ │ │   └── color-name@1.1.3
│ │ └── supports-color@5.5.0
│ ├─┬ concat-stream@1.6.2
│ │ ├── buffer-from@1.1.1
│ │ └── typedarray@0.0.6
│ ├─┬ cross-spawn@5.1.0
│ │ ├─┬ shebang-command@1.2.0
│ │ │ └── shebang-regex@1.0.0
│ │ └─┬ which@1.3.1
│ │   └── isexe@2.0.0
│ ├─┬ debug@3.2.6
│ │ └── ms@2.1.2
│ ├── doctrine@2.1.0
│ ├─┬ eslint-scope@3.7.3
│ │ ├── esrecurse@4.2.1
│ │ └── estraverse@4.2.0
│ ├── eslint-visitor-keys@1.0.0
│ ├─┬ espree@3.5.4
│ │ ├── acorn@5.7.3
│ │ └─┬ acorn-jsx@3.0.1
│ │   └── acorn@3.3.0
│ ├── esquery@1.0.1
│ ├── esutils@2.0.3
│ ├─┬ file-entry-cache@2.0.0
│ │ ├─┬ flat-cache@1.3.4
│ │ │ ├── circular-json@0.3.3
│ │ │ ├── graceful-fs@4.2.1
│ │ │ ├─┬ rimraf@2.6.3
│ │ │ │ └── glob@7.1.4
│ │ │ └── write@0.2.1
│ │ └── object-assign@4.1.1
│ ├── functional-red-black-tree@1.0.1
│ ├─┬ glob@7.1.4
│ │ ├── fs.realpath@1.0.0
│ │ ├── inflight@1.0.6
│ │ └── path-is-absolute@1.0.1
│ ├── globals@11.12.0
│ ├── ignore@3.3.10
│ ├── imurmurhash@0.1.4
│ ├─┬ inquirer@3.3.0
│ │ ├── ansi-escapes@3.2.0
│ │ ├─┬ cli-cursor@2.1.0
│ │ │ └─┬ restore-cursor@2.0.0
│ │ │   ├─┬ onetime@2.0.1
│ │ │   │ └── mimic-fn@1.2.0
│ │ │   └── signal-exit@3.0.2
│ │ ├── cli-width@2.2.0
│ │ ├─┬ external-editor@2.2.0
│ │ │ ├── chardet@0.4.2
│ │ │ ├── iconv-lite@0.4.24
│ │ │ └─┬ tmp@0.0.33
│ │ │   └── os-tmpdir@1.0.2
│ │ ├── figures@2.0.0
│ │ ├── mute-stream@0.0.7
│ │ ├─┬ run-async@2.3.0
│ │ │ └── is-promise@2.1.0
│ │ ├── rx-lite@4.0.8
│ │ ├── rx-lite-aggregates@4.0.8
│ │ ├─┬ string-width@2.1.1
│ │ │ └── is-fullwidth-code-point@2.0.0
│ │ ├─┬ strip-ansi@4.0.0
│ │ │ └── ansi-regex@3.0.0
│ │ └── through@2.3.8
│ ├── is-resolvable@1.1.0
│ ├─┬ js-yaml@3.13.1
│ │ ├─┬ argparse@1.0.10
│ │ │ └── sprintf-js@1.0.3
│ │ └── esprima@4.0.1
│ ├── json-stable-stringify-without-jsonify@1.0.1
│ ├─┬ levn@0.3.0
│ │ ├── prelude-ls@1.1.2
│ │ └── type-check@0.3.2
│ ├─┬ minimatch@3.0.4
│ │ └─┬ brace-expansion@1.1.11
│ │   ├── balanced-match@1.0.0
│ │   └── concat-map@0.0.1
│ ├─┬ mkdirp@0.5.1
│ │ └── minimist@0.0.8
│ ├── natural-compare@1.4.0
│ ├─┬ optionator@0.8.2
│ │ ├── deep-is@0.1.3
│ │ ├── fast-levenshtein@2.0.6
│ │ └── wordwrap@1.0.0
│ ├── path-is-inside@1.0.2
│ ├── pluralize@7.0.0
│ ├── progress@2.0.3
│ ├── regexpp@1.1.0
│ ├─┬ require-uncached@1.0.3
│ │ ├─┬ caller-path@0.1.0
│ │ │ └── callsites@0.2.0
│ │ └── resolve-from@1.0.1
│ ├── semver@5.7.0
│ ├─┬ strip-ansi@4.0.0
│ │ └── ansi-regex@3.0.0
│ ├── strip-json-comments@2.0.1
│ ├─┬ table@4.0.2
│ │ ├─┬ UNMET PEER DEPENDENCY ajv@5.5.2
│ │ │ ├── fast-deep-equal@1.1.0
│ │ │ └── json-schema-traverse@0.3.1
│ │ ├── ajv-keywords@2.1.1
│ │ ├─┬ slice-ansi@1.0.0
│ │ │ └── is-fullwidth-code-point@2.0.0
│ │ └─┬ string-width@2.1.1
│ │   ├── is-fullwidth-code-point@2.0.0
│ │   └─┬ strip-ansi@4.0.0
│ │     └── ansi-regex@3.0.0
│ └── text-table@0.2.0
├─┬ eslint-config-airbnb-base@12.1.0
│ └── eslint-restricted-globals@0.1.1
├─┬ eslint-plugin-import@2.18.2
│ ├─┬ array-includes@3.0.3
│ │ ├─┬ define-properties@1.1.3
│ │ │ └── object-keys@1.1.1
│ │ └─┬ es-abstract@1.13.0
│ │   ├─┬ es-to-primitive@1.2.0
│ │   │ ├── is-date-object@1.0.1
│ │   │ └─┬ is-symbol@1.0.2
│ │   │   └── has-symbols@1.0.0
│ │   ├── is-callable@1.1.4
│ │   └── is-regex@1.0.4
│ ├── contains-path@0.1.0
│ ├─┬ debug@2.6.9
│ │ └── ms@2.0.0
│ ├── doctrine@1.5.0
│ ├── eslint-import-resolver-node@0.3.2
│ ├─┬ eslint-module-utils@2.4.1
│ │ └── pkg-dir@2.0.0
│ ├─┬ has@1.0.3
│ │ └── function-bind@1.1.1
│ ├── object.values@1.1.0
│ ├─┬ read-pkg-up@2.0.0
│ │ ├─┬ find-up@2.1.0
│ │ │ └─┬ locate-path@2.0.0
│ │ │   ├─┬ p-locate@2.0.0
│ │ │   │ └─┬ p-limit@1.3.0
│ │ │   │   └── p-try@1.0.0
│ │ │   └── path-exists@3.0.0
│ │ └─┬ read-pkg@2.0.0
│ │   ├─┬ load-json-file@2.0.0
│ │   │ ├─┬ parse-json@2.2.0
│ │   │ │ └─┬ error-ex@1.3.2
│ │   │ │   └── is-arrayish@0.2.1
│ │   │ ├── pify@2.3.0
│ │   │ └── strip-bom@3.0.0
│ │   ├─┬ normalize-package-data@2.5.0
│ │   │ ├─┬ hosted-git-info@2.8.2
│ │   │ │ └─┬ lru-cache@5.1.1
│ │   │ │   └── yallist@3.0.3
│ │   │ └─┬ validate-npm-package-license@3.0.4
│ │   │   ├─┬ spdx-correct@3.1.0
│ │   │   │ └── spdx-license-ids@3.0.5
│ │   │   └─┬ spdx-expression-parse@3.0.0
│ │   │     └── spdx-exceptions@2.2.0
│ │   └── path-type@2.0.0
│ └─┬ resolve@1.12.0
│   └── path-parse@1.0.6
├─┬ grpc@1.22.2
│ ├── lodash.clone@4.5.0
│ ├── nan@2.14.0
│ ├─┬ node-pre-gyp@0.13.0
│ │ ├── detect-libc@1.0.3
│ │ ├─┬ mkdirp@0.5.1
│ │ │ └── minimist@0.0.8
│ │ ├─┬ needle@2.4.0
│ │ │ ├─┬ debug@3.2.6
│ │ │ │ └── ms@2.1.2
│ │ │ ├─┬ iconv-lite@0.4.23
│ │ │ │ └── safer-buffer@2.1.2
│ │ │ └── sax@1.2.4
│ │ ├─┬ nopt@4.0.1
│ │ │ ├── abbrev@1.1.1
│ │ │ └─┬ osenv@0.1.5
│ │ │   ├── os-homedir@1.0.2
│ │ │   └── os-tmpdir@1.0.2
│ │ ├─┬ npm-packlist@1.4.1
│ │ │ ├── ignore-walk@3.0.1
│ │ │ └── npm-bundled@1.0.6
│ │ ├─┬ npmlog@4.1.2
│ │ │ ├─┬ are-we-there-yet@1.1.5
│ │ │ │ ├── delegates@1.0.0
│ │ │ │ └─┬ readable-stream@2.3.6
│ │ │ │   ├── core-util-is@1.0.2
│ │ │ │   ├── isarray@1.0.0
│ │ │ │   ├── process-nextick-args@2.0.1
│ │ │ │   ├── string_decoder@1.1.1
│ │ │ │   └── util-deprecate@1.0.2
│ │ │ ├── console-control-strings@1.1.0
│ │ │ ├─┬ gauge@2.7.4
│ │ │ │ ├── aproba@1.2.0
│ │ │ │ ├── has-unicode@2.0.1
│ │ │ │ ├── object-assign@4.1.1
│ │ │ │ ├── signal-exit@3.0.1
│ │ │ │ ├─┬ string-width@1.0.2
│ │ │ │ │ ├── code-point-at@1.1.0
│ │ │ │ │ └─┬ is-fullwidth-code-point@1.0.0
│ │ │ │ │   └── number-is-nan@1.0.1
│ │ │ │ ├─┬ strip-ansi@3.0.1
│ │ │ │ │ └── ansi-regex@2.1.1
│ │ │ │ └── wide-align@1.1.3
│ │ │ └── set-blocking@2.0.0
│ │ ├─┬ rc@1.2.8
│ │ │ ├── deep-extend@0.6.0
│ │ │ ├── ini@1.3.5
│ │ │ └── strip-json-comments@2.0.1
│ │ ├─┬ rimraf@2.6.3
│ │ │ └── glob@7.1.4
│ │ ├── semver@5.7.0
│ │ └─┬ tar@4.4.10
│ │   ├── chownr@1.1.1
│ │   ├── fs-minipass@1.2.6
│ │   ├── minipass@2.3.5
│ │   ├── minizlib@1.2.1
│ │   ├── safe-buffer@5.1.2
│ │   └── yallist@3.0.3
│ └─┬ protobufjs@5.0.3
│   ├─┬ ascli@1.0.1
│   │ ├── colour@0.7.1
│   │ └── optjs@3.2.2
│   ├─┬ bytebuffer@5.0.1
│   │ └── long@3.2.0
│   ├─┬ glob@7.1.4
│   │ ├── fs.realpath@1.0.0
│   │ ├─┬ inflight@1.0.6
│   │ │ └── wrappy@1.0.2
│   │ ├── inherits@2.0.3
│   │ ├─┬ minimatch@3.0.4
│   │ │ └─┬ brace-expansion@1.1.11
│   │ │   ├── balanced-match@1.0.0
│   │ │   └── concat-map@0.0.1
│   │ ├── once@1.4.0
│   │ └── path-is-absolute@1.0.1
│   └─┬ yargs@3.32.0
│     ├── camelcase@2.1.1
│     ├─┬ cliui@3.2.0
│     │ └── wrap-ansi@2.1.0
│     ├── decamelize@1.2.0
│     ├─┬ os-locale@1.4.0
│     │ └─┬ lcid@1.0.0
│     │   └── invert-kv@1.0.0
│     ├─┬ string-width@1.0.2
│     │ ├── code-point-at@1.1.0
│     │ └─┬ is-fullwidth-code-point@1.0.0
│     │   └── number-is-nan@1.0.1
│     ├── window-size@0.1.4
│     └── y18n@3.2.1
├─┬ jshint-stylish@2.2.1
│ ├── beeper@1.1.1
│ ├─┬ chalk@1.1.3
│ │ ├── ansi-styles@2.2.1
│ │ ├─┬ has-ansi@2.0.0
│ │ │ └── ansi-regex@2.1.1
│ │ ├── strip-ansi@3.0.1
│ │ └── supports-color@2.0.0
│ ├─┬ log-symbols@1.0.2
│ │ └─┬ chalk@1.1.3
│ │   ├── ansi-styles@2.2.1
│ │   └── supports-color@2.0.0
│ ├─┬ plur@2.1.2
│ │ └── irregular-plurals@1.4.0
│ └── string-length@1.0.1
├── lodash@4.17.15
├── minimist@1.2.0
├─┬ mocha@5.2.0
│ ├── browser-stdout@1.3.1
│ ├── commander@2.15.1
│ ├── debug@3.1.0
│ ├── diff@3.5.0
│ ├── escape-string-regexp@1.0.5
│ ├── glob@7.1.2
│ ├── growl@1.10.5
│ ├── he@1.1.1
│ └─┬ supports-color@5.4.0
│   └── has-flag@3.0.0
├─┬ mqemitter-redis@3.0.0
│ ├─┬ hyperid@1.4.1
│ │ └── uuid-parse@1.1.0
│ ├── inherits@2.0.4
│ └─┬ lru-cache@4.1.5
│   ├── pseudomap@1.0.2
│   └── yallist@2.1.2
├─┬ nats@1.3.0
│ ├── nuid@1.1.0
│ └─┬ ts-nkeys@1.0.12
│   └── tweetnacl@1.0.1
├─┬ protobufjs@6.8.8
│ ├── @protobufjs/aspromise@1.1.2
│ ├── @protobufjs/base64@1.1.2
│ ├── @protobufjs/codegen@2.0.4
│ ├── @protobufjs/eventemitter@1.1.0
│ ├── @protobufjs/fetch@1.1.0
│ ├── @protobufjs/float@1.0.2
│ ├── @protobufjs/inquire@1.1.0
│ ├── @protobufjs/path@1.1.2
│ ├── @protobufjs/pool@1.1.0
│ ├── @protobufjs/utf8@1.1.0
│ ├── @types/long@4.0.0
│ ├── @types/node@10.14.15
│ └── long@4.0.0
├─┬ redis@2.8.0
│ ├── double-ended-queue@2.1.0-0
│ ├── redis-commands@1.5.0
│ └── redis-parser@2.6.0
├─┬ request@2.88.0
│ ├── aws-sign2@0.7.0
│ ├── aws4@1.8.0
│ ├── caseless@0.12.0
│ ├─┬ combined-stream@1.0.8
│ │ └── delayed-stream@1.0.0
│ ├── extend@3.0.2
│ ├── forever-agent@0.6.1
│ ├─┬ form-data@2.3.3
│ │ └── asynckit@0.4.0
│ ├─┬ har-validator@5.1.3
│ │ ├─┬ ajv@6.10.2
│ │ │ ├── fast-deep-equal@2.0.1
│ │ │ ├── json-schema-traverse@0.4.1
│ │ │ └─┬ uri-js@4.2.2
│ │ │   └── punycode@2.1.1
│ │ └── har-schema@2.0.0
│ ├─┬ http-signature@1.2.0
│ │ ├── assert-plus@1.0.0
│ │ ├─┬ jsprim@1.4.1
│ │ │ ├── extsprintf@1.3.0
│ │ │ ├── json-schema@0.2.3
│ │ │ └── verror@1.10.0
│ │ └─┬ sshpk@1.16.1
│ │   ├── asn1@0.2.4
│ │   ├─┬ bcrypt-pbkdf@1.0.2
│ │   │ └── tweetnacl@0.14.5
│ │   ├── dashdash@1.14.1
│ │   ├── ecc-jsbn@0.1.2
│ │   ├── getpass@0.1.7
│ │   ├── jsbn@0.1.1
│ │   ├── safer-buffer@2.1.2
│ │   └── tweetnacl@0.14.5
│ ├── is-typedarray@1.0.0
│ ├── isstream@0.1.2
│ ├── json-stringify-safe@5.0.1
│ ├─┬ mime-types@2.1.24
│ │ └── mime-db@1.40.0
│ ├── oauth-sign@0.9.0
│ ├── performance-now@2.1.0
│ ├── qs@6.5.2
│ ├─┬ tough-cookie@2.4.3
│ │ ├── psl@1.3.0
│ │ └── punycode@1.4.1
│ └── tunnel-agent@0.6.0
├─┬ supertest@3.4.2
│ ├── methods@1.1.2
│ └─┬ superagent@3.8.3
│   ├── component-emitter@1.3.0
│   ├── cookiejar@2.1.2
│   ├─┬ debug@3.2.6
│   │ └── ms@2.1.2
│   ├── formidable@1.2.1
│   └── mime@1.6.0
├── toml@2.3.6
└─┬ websocket-stream@5.5.0
  ├─┬ duplexify@3.7.1
  │ └── stream-shift@1.0.0
  ├─┬ readable-stream@2.3.6
  │ ├── core-util-is@1.0.2
  │ ├── string_decoder@1.1.1
  │ └── util-deprecate@1.0.2
  ├─┬ ws@3.3.3
  │ ├── async-limiter@1.0.1
  │ └── ultron@1.1.1
  └── xtend@4.0.2

npm WARN ajv-keywords@2.1.1 requires a peer of ajv@^5.0.0 but none was installed.
```

(通过观察编译过程，可以看出来npm的目的是为了拉取mqtt所需的一些库，查看mainflux/mqtt可进行印证。)

这一回在编译完除mqtt以外微服务时仍然很顺利而且很快，但是在mqtt编译的过程中报了一些警告，包括一些库弃用以及有个库缺少（最后一条警告，解决办法参见[5.1 解决npm安装ajv-keywords缺少配对问题](#51-%e8%a7%a3%e5%86%b3npm%e5%ae%89%e8%a3%85ajv-keywords%e7%bc%ba%e5%b0%91%e9%85%8d%e5%af%b9%e9%97%ae%e9%a2%98))，这些包弃用或者停止维护的警告暂时不管。

解决完npm安装依赖问题后，为了避免出现各种莫名问题，重新执行make。
执行结果：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-users cmd/users/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-things cmd/things/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-http cmd/http/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-normalizer cmd/normalizer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-ws cmd/ws/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-coap cmd/coap/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-lora cmd/lora/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-influxdb-writer cmd/influxdb-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-influxdb-reader cmd/influxdb-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-mongodb-writer cmd/mongodb-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-mongodb-reader cmd/mongodb-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cassandra-writer cmd/cassandra-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cassandra-reader cmd/cassandra-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-postgres-writer cmd/postgres-writer/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-postgres-reader cmd/postgres-reader/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-cli cmd/cli/main.go
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-bootstrap cmd/bootstrap/main.go
cd mqtt/aedes && npm install
up to date in 7.374s

┌─────────────────────────────────────────────────────────┐
│                 npm update check failed                 │
│           Try running with sudo or get access           │
│          to the local update config store via           │
│ sudo chown -R $USER:$(id -gn $USER) /home/eiger/.config │
└─────────────────────────────────────────────────────────┘
```

好了，这回又提示说npm更新检查失败，需要sudo权限或者将当前用户提升权限。

先试下`sudo make`：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo make
CGO_ENABLED=0 GOOS= GOARCH=amd64 GOARM= go build -ldflags "-s -w" -o build/mainflux-users cmd/users/main.go
/bin/sh: 1: go: not found
Makefile:72: recipe for target 'users' failed
make: *** [users] Error 127
```

报错说找不到go命令……这很不科学，在installUbuntu.md文件中安装go环境已确定设置好系统变量……

尝试第二种方法：`sudo chown -R $USER:$(id -gn $USER) /home/eiger/.config`，没有报错，再执行`make`，完美通过！！！

**注意： make命令执行成功后，所有编译结果都在/mainflux/build下，为若干个可执行程序。他们之间是静态链接库的关系，可以根据需要移动、部署，他们不再依赖其他系统的、外部的依赖，方便容器化部署。**

### 4.3. 编译单个微服务

执行：

```shell
make <microservice_name>  ## 例如make http将会编译http适配器服务
```

### 4.4. 编译dockers

执行到这一步时，才想起需要安装docker，因此参照[6 安装docker ce](#6-%e5%ae%89%e8%a3%85docker)安装好docker ce。

- 一次性编译所有dockers：

    ```shell
    make dockers
    ```

- 编译单个docker：

    ```shell
    make docker_<microservice_name>  ## 例如make docker_http
    ```

这里只记录 `make dockers`的过程:

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make dockers
docker build --no-cache --build-arg SVC=users --build-arg GOARCH=amd64 --build-arg GOARM= --tag=mainflux/users-amd64 -f docker/Dockerfile .
ERRO[0000] failed to dial gRPC: cannot connect to the Docker daemon. Is 'docker daemon' running on this host?: dial unix /var/run/docker.sock: connect: permission denied
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.40/build?buildargs=%7B%22GOARCH%22%3A%22amd64%22%2C%22GOARM%22%3A%22%22%2C%22SVC%22%3A%22users%22%7D&cachefrom=%5B%5D&cgroupparent=&cpuperiod=0&cpuquota=0&cpusetcpus=&cpusetmems=&cpushares=0&dockerfile=docker%2FDockerfile&labels=%7B%7D&memory=0&memswap=0&networkmode=default&nocache=1&rm=1&session=30vy9rez3a6gmk08ou65zek32&shmsize=0&t=mainflux%2Fusers-amd64&target=&ulimits=null&version=1: dial unix /var/run/docker.sock: connect: permission denied
Makefile:75: recipe for target 'docker_users' failed
make: *** [docker_users] Error 1
```

显然，这是权限不足的问题，虽然很奇怪，为什么添加了eiger到docker用户组还会这样，但不管了，加上`sudo`再试一次：

```shell
sudo make dockers
```

这回开始编译了，但是编译过程极为漫长！毕竟是二十来个docker镜像需要编译。这部分注意对照Makefile并观察编译过程。

问题又出现了！在编译至docker_ui时报了个错：拉取一个依赖包失败，具体看下面的输出。

```shell
Successfully tagged mainflux/bootstrap-amd64:latest
make -C ui docker
make[1]: Entering directory '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/ui'
docker build --tag=mainflux/ui-amd64 -f docker/Dockerfile .
Sending build context to Docker daemon  113.2kB
Step 1/13 : FROM node:10.15.1-alpine as builder
 ---> fe6ff768f798
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> b0c156339fd9
Step 3/13 : RUN npm install --unsafe-perm=true --allow-root -g elm
 ---> Running in 80d5a8d6f74a
/usr/local/bin/elm -> /usr/local/lib/node_modules/elm/bin/elm

> elm@0.19.0-no-deps install /usr/local/lib/node_modules/elm
> node install.js

+ elm@0.19.0-no-deps
added 1 package from 1 contributor in 566.006s
Removing intermediate container 80d5a8d6f74a
 ---> 941e189101bf
Step 4/13 : COPY . /app
 ---> 776802a00b76
Step 5/13 : RUN elm make --optimize src/Main.elm --output=main.js
 ---> Running in 216a675c55ec
-- HTTP PROBLEM ----------------------------------------------------------------

The following HTTP request failed:

    <https://github.com/avh4/elm-color/zipball/1.0.0/>

Here is the error message I was able to extract:

    HttpExceptionRequest Request { host = "github.com" port = 443 secure = True
    requestHeaders = [("User-Agent","elm/0.19.0"),("Accept-Encoding","gzip")]
    path = "/avh4/elm-color/zipball/1.0.0/" queryString = "" method = "GET"
    proxy = Nothing rawBody = False redirectCount = 10 responseTimeout =
    ResponseTimeoutDefault requestVersion = HTTP/1.1 } ConnectionTimeout

Starting downloads...

  ● elm/http 2.0.0
  ● elm/file 1.0.1
  ● elm/json 1.1.2
  ● elm/html 1.0.0
  ● elm/bytes 1.0.7
  ● elm/url 1.0.0
  ● elm/virtual-dom 1.0.2
  ✗ avh4/elm-color 1.0.0
  ● elm-community/list-extra 8.1.0
  ● elm/core 1.0.2
  ● elm/time 1.0.0
  ● elm/browser 1.0.1
  ● rundis/elm-bootstrap 5.0.0

The command '/bin/sh -c elm make --optimize src/Main.elm --output=main.js' returned a non-zero code: 1
Makefile:18: recipe for target 'docker' failed
make[1]: *** [docker] Error 1
make[1]: Leaving directory '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/ui'
Makefile:81: recipe for target 'docker_ui' failed
make: *** [docker_ui] Error 2
```

由于是半夜放在那让它编译，所以不确定是不是网络出问题还是别的原因。

**注意： 这时候千万别重新执行`sudo make dockers`! 注意编译这些docker镜像时：1.每次执行make都会覆盖原本，重新编译，并不会减少编译所需时间；2.容器内数据没有持久化，如果容器内产生了生产数据，重新编译后将全部丢失！**

那么现在来看下Makefile，可以看出在dockers中，docker_ui和docker_mqtt是两个特殊的存在，从编译过程来看可能是因为这两部分用到了nodejs的一些库。而在编译dockers的顺序中docker_ui和docker_mqtt是最后编译的，也就是说，在上一步`sudo make dockers`过程中已经编译了docker_ui之前的所有镜像，所以接下来只需要单独编译docker_ui和docker_mqtt：

```shell
sudo make docker_ui  ## 这回没有出错，说明晚上可能是因为网络不佳而出错的
sudo make docker_mqtt
```

在编译docker_mqtt的时候虽然最后显示成功了，但是中间报了如下错误：

```shell
Step 3/5 : RUN npm rebuild && npm install
 ---> Running in 1ffb877f0a26

> protobufjs@6.8.8 postinstall /node_modules/protobufjs
> node scripts/postinstall


> grpc@1.22.2 install /node_modules/grpc
> node-pre-gyp install --fallback-to-build --library=static_library

node-pre-gyp WARN Using request for node-pre-gyp https download
[grpc] Success: "/node_modules/grpc/src/node/extension_binary/node-v64-linux-x64-musl/grpc_node.node" is installed via remote

> dtrace-provider@0.8.7 install /node_modules/dtrace-provider
> node-gyp rebuild || node suppress-error.js

gyp ERR! configure error
gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
gyp ERR! stack     at PythonFinder.failNoPython (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/configure.js:484:19)
gyp ERR! stack     at PythonFinder.<anonymous> (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/configure.js:406:16)
gyp ERR! stack     at F (/usr/local/lib/node_modules/npm/node_modules/which/which.js:68:16)
gyp ERR! stack     at E (/usr/local/lib/node_modules/npm/node_modules/which/which.js:80:29)
gyp ERR! stack     at /usr/local/lib/node_modules/npm/node_modules/which/which.js:89:16
gyp ERR! stack     at /usr/local/lib/node_modules/npm/node_modules/isexe/index.js:42:5
gyp ERR! stack     at /usr/local/lib/node_modules/npm/node_modules/isexe/mode.js:8:5
gyp ERR! stack     at FSReqWrap.oncomplete (fs.js:154:21)
gyp ERR! System Linux 4.15.0-55-generic
gyp ERR! command "/usr/local/bin/node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /node_modules/dtrace-provider
gyp ERR! node -v v10.15.1
gyp ERR! node-gyp -v v3.8.0
gyp ERR! not ok
2@1.0.2 /node_modules/2
@grpc/proto-loader@0.5.1 /node_modules/@grpc/proto-loader
lodash.camelcase@4.3.0 /node_modules/lodash.camelcase
protobufjs@6.8.8 /node_modules/protobufjs
@protobufjs/aspromise@1.1.2 /node_modules/@protobufjs/aspromise
@protobufjs/base64@1.1.2 /node_modules/@protobufjs/base64
@protobufjs/codegen@2.0.4 /node_modules/@protobufjs/codegen
@protobufjs/eventemitter@1.1.0 /node_modules/@protobufjs/eventemitter
@protobufjs/fetch@1.1.0 /node_modules/@protobufjs/fetch
@protobufjs/inquire@1.1.0 /node_modules/@protobufjs/inquire
@protobufjs/float@1.0.2 /node_modules/@protobufjs/float
@protobufjs/path@1.1.2 /node_modules/@protobufjs/path
@protobufjs/pool@1.1.0 /node_modules/@protobufjs/pool
@protobufjs/utf8@1.1.0 /node_modules/@protobufjs/utf8
```

参照[session 5.2 解决gyp找不到python](#52-%e8%a7%a3%e5%86%b3node-gyp%e6%8f%90%e7%a4%ba%e6%89%be%e4%b8%8d%e5%88%b0python)重新执行`sudo make docker_mqtt`，这回没有任何报错信息。

至此，所有docker编译成功。

列出docker镜像来看下我们编译的结果：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
mainflux/mqtt-amd64               latest              63c2335da152        47 minutes ago      177MB
mainflux/ui-amd64                 latest              56818963b563        About an hour ago   16.4MB
<none>                            <none>              c236d4a2ba3a        About an hour ago   127MB
mainflux/bootstrap-amd64          latest              2d6bf08877ff        11 hours ago        11.5MB
<none>                            <none>              a34d0a5ecd02        11 hours ago        413MB
mainflux/cli-amd64                latest              9a95ca2d5e58        11 hours ago        7.39MB
<none>                            <none>              74eb37c632e4        11 hours ago        396MB
mainflux/postgres-reader-amd64    latest              0da4b651447e        11 hours ago        10.4MB
<none>                            <none>              165376436de3        11 hours ago        408MB
mainflux/postgres-writer-amd64    latest              ca8dc5c8357c        11 hours ago        10.2MB
<none>                            <none>              f4e8dbb870ba        11 hours ago        406MB
mainflux/cassandra-reader-amd64   latest              a9abfb5cc14f        11 hours ago        10.4MB
<none>                            <none>              db7ad32ad5aa        11 hours ago        408MB
mainflux/cassandra-writer-amd64   latest              b9017e5c7bf7        11 hours ago        10.2MB
<none>                            <none>              9afe59452890        11 hours ago        406MB
mainflux/mongodb-reader-amd64     latest              83312eb43d4e        12 hours ago        13.2MB
<none>                            <none>              8b2fa37acfc8        12 hours ago        417MB
mainflux/mongodb-writer-amd64     latest              c2c20ded7746        12 hours ago        13MB
<none>                            <none>              26c81879092f        12 hours ago        415MB
mainflux/influxdb-reader-amd64    latest              602cca6b16df        12 hours ago        9.64MB
<none>                            <none>              87ab23218a98        12 hours ago        405MB
mainflux/influxdb-writer-amd64    latest              8291fcbf79c7        12 hours ago        9.57MB
<none>                            <none>              e679ddd44034        12 hours ago        403MB
mainflux/lora-amd64               latest              6ad78c136202        12 hours ago        10.5MB
<none>                            <none>              63535273c007        12 hours ago        408MB
mainflux/coap-amd64               latest              436fb919b3ef        12 hours ago        10.5MB
<none>                            <none>              91608629e556        12 hours ago        408MB
mainflux/ws-amd64                 latest              ea32ef946286        12 hours ago        10.6MB
<none>                            <none>              b042f0b98333        12 hours ago        408MB
mainflux/normalizer-amd64         latest              f457472beb17        12 hours ago        12.1MB
<none>                            <none>              95bf7a83f966        12 hours ago        413MB
mainflux/http-amd64               latest              e9c5e8cca6d2        12 hours ago        10.5MB
<none>                            <none>              19d20eb17af4        12 hours ago        407MB
mainflux/things-amd64             latest              6b91b8561a30        12 hours ago        11.7MB
<none>                            <none>              f1a3ac2c5c0f        12 hours ago        414MB
mainflux/users-amd64              latest              64a299ad8ed2        12 hours ago        10.6MB
<none>                            <none>              6779422f65aa        12 hours ago        408MB
<none>                            <none>              3b90edcfdee8        13 hours ago        11.5MB
<none>                            <none>              d53f0f373e9a        13 hours ago        413MB
<none>                            <none>              a9ff9908ae2e        13 hours ago        7.39MB
<none>                            <none>              1408f062a173        13 hours ago        396MB
<none>                            <none>              d895327d99cf        13 hours ago        10.4MB
<none>                            <none>              c17ffbb16c9e        13 hours ago        408MB
<none>                            <none>              0509f73463dd        13 hours ago        406MB
<none>                            <none>              e4853e5a8454        13 hours ago        10.2MB
<none>                            <none>              f7b9418c6859        13 hours ago        10.4MB
<none>                            <none>              fbc4d84e0341        13 hours ago        408MB
<none>                            <none>              b56da15d0023        13 hours ago        10.2MB
<none>                            <none>              003f0c53afa6        13 hours ago        406MB
<none>                            <none>              8b7bb39a178e        13 hours ago        13.2MB
<none>                            <none>              ef263981d942        13 hours ago        417MB
<none>                            <none>              1c0438894bf9        13 hours ago        13MB
<none>                            <none>              c51b3153d7b4        13 hours ago        415MB
<none>                            <none>              1d9fac869f57        13 hours ago        9.64MB
<none>                            <none>              333ace195d78        13 hours ago        405MB
<none>                            <none>              52fb3e85a3ab        13 hours ago        403MB
<none>                            <none>              00d991f250ca        13 hours ago        9.57MB
<none>                            <none>              0a28b98875f6        13 hours ago        408MB
<none>                            <none>              0802b11f9713        13 hours ago        10.5MB
<none>                            <none>              cabad6622c1e        13 hours ago        10.5MB
<none>                            <none>              c456e667acec        13 hours ago        408MB
<none>                            <none>              51f696d2c560        13 hours ago        10.6MB
<none>                            <none>              500e4e234111        13 hours ago        408MB
<none>                            <none>              c23c3e97f79d        13 hours ago        12.1MB
<none>                            <none>              8dc2bee947c6        13 hours ago        413MB
<none>                            <none>              dc9be8714e78        13 hours ago        10.5MB
<none>                            <none>              3958271baf61        13 hours ago        407MB
<none>                            <none>              f8688db1c91b        13 hours ago        11.7MB
<none>                            <none>              7060288201c9        13 hours ago        414MB
<none>                            <none>              1c11e7c44a0b        13 hours ago        10.6MB
<none>                            <none>              31ed21e0c9da        13 hours ago        408MB
nginx                             1.14.2-alpine       8a2fb25a19f5        4 months ago        16MB
golang                            1.10-alpine         7b53e4a31d21        6 months ago        259MB
node                              10.15.1-alpine      fe6ff768f798        6 months ago        70.7MB
hello-world                       latest              fce289e99eb9        7 months ago        1.84kB

```

可以看到有很多`<none>`的镜像，处理方式参见[6.1 `<none>`镜像处理](#61-none%e9%95%9c%e5%83%8f%e7%9a%84%e5%a4%84%e7%90%86)

处理输出：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker image prune
[sudo] password for eiger:
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y
Deleted Images:
......(省略)
```

处理好`<none>`镜像之后，再列一下当前docker镜像：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
mainflux/mqtt-amd64               latest              63c2335da152        About an hour ago   177MB
mainflux/ui-amd64                 latest              56818963b563        About an hour ago   16.4MB
<none>                            <none>              776802a00b76        11 hours ago        119MB
mainflux/bootstrap-amd64          latest              2d6bf08877ff        11 hours ago        11.5MB
mainflux/cli-amd64                latest              9a95ca2d5e58        12 hours ago        7.39MB
mainflux/postgres-reader-amd64    latest              0da4b651447e        12 hours ago        10.4MB
mainflux/postgres-writer-amd64    latest              ca8dc5c8357c        12 hours ago        10.2MB
mainflux/cassandra-reader-amd64   latest              a9abfb5cc14f        12 hours ago        10.4MB
mainflux/cassandra-writer-amd64   latest              b9017e5c7bf7        12 hours ago        10.2MB
mainflux/mongodb-reader-amd64     latest              83312eb43d4e        12 hours ago        13.2MB
mainflux/mongodb-writer-amd64     latest              c2c20ded7746        12 hours ago        13MB
mainflux/influxdb-reader-amd64    latest              602cca6b16df        12 hours ago        9.64MB
mainflux/influxdb-writer-amd64    latest              8291fcbf79c7        12 hours ago        9.57MB
mainflux/lora-amd64               latest              6ad78c136202        12 hours ago        10.5MB
mainflux/coap-amd64               latest              436fb919b3ef        12 hours ago        10.5MB
mainflux/ws-amd64                 latest              ea32ef946286        12 hours ago        10.6MB
mainflux/normalizer-amd64         latest              f457472beb17        12 hours ago        12.1MB
mainflux/http-amd64               latest              e9c5e8cca6d2        12 hours ago        10.5MB
mainflux/things-amd64             latest              6b91b8561a30        13 hours ago        11.7MB
mainflux/users-amd64              latest              64a299ad8ed2        13 hours ago        10.6MB
nginx                             1.14.2-alpine       8a2fb25a19f5        4 months ago        16MB
golang                            1.10-alpine         7b53e4a31d21        6 months ago        259MB
node                              10.15.1-alpine      fe6ff768f798        6 months ago        70.7MB
hello-world                       latest              fce289e99eb9        7 months ago        1.84kB
```

注意：这里所列出的镜像，除了hello-world其他都是mainflux容器编译的产物！（golang/nginx/node应该是编译过程中拉取的。）

最后，再次提示，重新进行任何的docker的编译都将完全覆盖原本docker！！！谨慎进行任何编译操作！！！

### 4.5. 其他docker安装相关

关于编译开发版本docker、覆盖原本docker-compose配置、清除mainflux容器安装，参见mainflux官方说明：
<mainflux.readthedocs.io/en/latest/dev-guide>

### 4.6. mqtt微服务

前边在编译所有微服务一节，输出信息中得到编译mqtt服务操作`cd mqtt/aedes && npm install`。

但奇怪的是在官方说明中又给出如下说明：
mqtt微服务基于nodejs编写，并未被编译（大概意指在编译微服务一节并未将其编译）。且为了启动mqtt服务，需要下载相应的node_modules。

下载node_modules指令：

```shell
cd mqtt
npm install
```

执行结果：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ cd mqtt
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux/mqtt$ ls
aedes  verne
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux/mqtt$ sudo npm install
npm WARN saveError ENOENT: no such file or directory, open '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/package.json'
npm WARN enoent ENOENT: no such file or directory, open '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/package.json'
npm WARN mainflux No description
npm WARN mainflux No repository field.
npm WARN mainflux No README data
npm WARN mainflux No license field.

audited 12 packages in 6.277s
found 0 vulnerabilities
```

看上去应该是官方文档在此处出错了，应该是cd mqtt/aedes。那么这样我们其实前边已经安装好了。

官方文档又提到，它提供了`make mqtt`这句命令来作为前面两句操作的短名。也就是说，我们在前面使用`make`编译所有微服务的最后，完成了mqtt中node_modules的安装。

那么，启动mqtt的方法为：在/mainflux项目根目录下（因为需要找到根目录下的*.proto文件），执行以下命令：

```shell
node mqtt/mqtt.js
```

其实这里也是有问题的，因为mqtt.js在mqtt/aedes下，所以我们改为`node mqtt/aedes/mqtt.js`

### 4.7. 其他

关于.proto文件修改后重新编译以及arm平台（树莓派就是arm架构的）的交叉编译，参看官方说明，[4.5 其他docker安装相关](#45-%e5%85%b6%e4%bb%96docker%e5%ae%89%e8%a3%85%e7%9b%b8%e5%85%b3)已给出。

## 5. 安装node.js和npm

终端输入：

```shell
sudo apt install nodejs
sudo apt install npm
```

我所安装的版本分别为nodejs v8.10.0和npm v3.5.2

### 5.1. 解决npm安装ajv-keywords缺少配对问题

问题描述：

```shell
npm WARN ajv-keywords@2.1.1 requires a peer of ajv@^5.0.0 but none was installed.
```

解决办法：

1. 先执行以下两句更新完软件

    ```shell
    sudo npm install -g npm-install-peers
    sudo npm install -g npm  ## 更新npm
    ```

    执行到此处时，npm版本为v6.10.3，npm-install-peers版本为v1.2.1

2. 再执行:

    ```shell
    sudo npm i ajv
    ```

    (ps: i为install短名)

    执行结果：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo npm i ajv
    npm WARN saveError ENOENT: no such file or directory, open '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/package.json'
    npm notice created a lockfile as package-lock.json. You should commit this file.
    npm WARN enoent ENOENT: no such file or directory, open '/home/eiger/gopath-default/src/github.com/mainflux/mainflux/package.json'
    npm WARN mainflux No description
    npm WARN mainflux No repository field.
    npm WARN mainflux No README data
    npm WARN mainflux No license field.

    + ajv@6.10.2
    added 6 packages from 4 contributors and audited 6 packages in 8.557s
    found 0 vulnerabilities

    ```

    从警告描述上可以看出是说，当前目录下没有package.json等文件。再联系到[4.2 编译所有服务](#42-%e7%bc%96%e8%af%91%e6%89%80%e6%9c%89%e6%9c%8d%e5%8a%a1)遇到此问题时是安装mqtt部分出问题，因此在/mainflux/mqtt/aedes文件下发现了警告中要求的package等诸项文件。因此应当切换到此目录下再安装ajv：

    ```shell
    cd mqtt/aedes
    sudo npm i ajv

    ```

    执行结果如下：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes$ sudo npm i ajv
    npm notice created a lockfile as package-lock.json. You should commit this file.
    npm WARN ajv-keywords@2.1.1 requires a peer of ajv@^5.0.0 but none is installed. You must install peer dependencies yourself.

    + ajv@6.10.2
    updated 1 package and audited 985 packages in 5.685s
    found 0 vulnerabilities

    ```

    从警告中看出，要求ajv@^5.0.0（ps: ^会匹配大版本下的所有依赖包，也就是说只要是ajv@5.x.x都可以），但是安装了ajv@6.10.2。

    也就是说，我们需要手动安装ajv@5.x.x版本。

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux/mqtt/aedes$ sudo npm i ajv@^5.0.0
    + ajv@5.5.2
    added 3 packages from 1 contributor, updated 3 packages and audited 984 packages in 10.349s
    found 0 vulnerabilities
    ```

    现在就没有问题了。

### 5.2. 解决node-gyp提示找不到python

在[4.4 编译dockers](#44-%e7%bc%96%e8%af%91dockers)编译docker_mqtt遇到了node-gyp提示找不到python而rebuild失败的错误，并给出了一堆的报错信息。从中我们看出，node-gyp这个组件在配置时出错，提示找不到python可执行文件，建议设置python环境变量PYTHON指向其执行文件。此时本机上经过检查，已安装有python2.7.15+和python3.6.8。

经过百度“node-gyp提示找不到python”获取到一些信息：npm只支持pthon2.7，不支持python3。网上的解决方案多是在npm install包失败提示该错误时指定参数`npm install --python=python2.7`，但是我的情况是python2.7已经有了，如何指定PYTHON版本的问题。先不设置PYTHON变量，我们去看下报错中的这个配置文件`/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/configure.js:484:19`打开后发现只有380行……

不管了，再来看看node-gyp这个插件的官方说明<https://github.com/nodejs/node-gyp>，先按照上面的说明进行一次完整安装。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo npm install -g node-gyp
[sudo] password for eiger:
/usr/local/bin/node-gyp -> /usr/local/lib/node_modules/node-gyp/bin/node-gyp.js
+ node-gyp@5.0.3
added 98 packages from 67 contributors in 92.347s
```

在node-gyp官方安装说明中指出还需要安装python2.7、make、gcc，这些在我的电脑上都已安装了（自带python2.7，安装build-essential）。

在官方文档中特别指出了：如果电脑上安装有多个版本的python（正是我的电脑目前的情况），需要配置python版本。配置方法：

1. 追加参数方式：

    ```shell
    node-gyp --python /path/to/python2.7
    ```

2. 如果node-gyp是被npm调用的，且电脑上有多个版本python， 可以设置npm的‘python’配置键为合适的值：

    ```shell
    npm config set python /path/to/executable/python2.7
    ```

看上去第二种方案是我需要的。现在解释下为什么不愿意直接在系统级或者用户级设置`PYTHON`变量，因为像npm这种只支持python2的奇葩还是少数，担心影响到以后其他依赖python的程序。好了，现在尝试按照上面第二种方法为npm设置python路径。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ whereis python  ## whereis命令
python: /usr/bin/python /usr/bin/python2.7 /usr/bin/python3.6 /usr/bin/python3.6m /usr/lib/python2.7 /usr/lib/python3.6 /usr/lib/python3.7 /etc/python /etc/python2.7 /etc/python3.6 /usr/local/lib/python2.7 /usr/local/lib/python3.6 /usr/include/python3.6m /usr/share/python /usr/share/man/man1/python.1.gz
```

得到python2.7执行路径`/usr/bin/python2.7`

```shell
npm config set python /usr/bin/python2.7
```

现在npm应该可以正常rebuild了（出错是因为找不到python来满足rebuild依赖）

## 6. 安装Docker

1. 更新国内软件源（在installUbuntu.md已完成相关步骤），这里给出网上修改为中科大镜像源的步骤：

    ```shell
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
    sudo apt update
    ```

2. 安装需要的依赖包

    ```shell
    sudo apt install apt-transport-https ca-certificates software-properties-common curl
    ```

3. 添加 GPG 密钥，并添加 Docker-ce 软件源，这里还是以中国科技大学的 Docker-ce 源为例

    ```shell
    curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
    ```

    (ps: 这里我仍使用的是清华镜像源，即将ustc替换为tuna.tsinghua，这并不影响什么。)

4. 添加成功后更新软件包缓存

    ```shell
    sudo apt update
    ````

5. 安装 Docker-ce

    ```shell
    sudo apt install docker-ce
    ```

6. 设置开机自启动并启动 Docker-ce（安装成功后默认已设置并启动，可忽略）

    ```shell
    sudo systemctl enable docker
    sudo systemctl start docker
    ```

7. 测试运行

    ```shell
    sudo docker run hello-world
    ```

8. 添加当前用户到 docker 用户组，可以不用 sudo 运行 docker（可选）

    ```shell
    sudo groupadd docker
    sudo usermod -aG docker eiger  ## eiger为想要添加的用户
    su root
    su eiger  ## 一定要先切root再切回eiger，这样前面做的设置才会生效！
    ```

9. 测试添加用户组（可选）

    ```shell
    docker run hello-world
    ```

### 6.1. `<none>`镜像的处理

关于编译过程中产生大量`<none>`镜像（或者描述为`<none>:<none>`镜像），可以参考<https://blog.csdn.net/boling_cavalry/article/details/90727359>

`<none>`镜像的产生是由于重新编译产生的产物，称为dangling镜像（摇摆的镜像），有的可能是被容器使用到的，有的则纯粹是老版本（就像蝉的遗蜕）。解决办法是：

```shell
docker image prune
```

该操作会删除所有没有用到的`<none>`镜像，被使用的并不会删除。

详细描述参看官方文档和上面提到的博文。

## 7. mainflux运行测试

```shell
make test
```

执行所有测试，包括Dockertest，这意味着需要运行Docker daemon/service。

首先，启动所有容器化的微服务，也就是将前面编译阶段得到的docker镜像跑起来：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/ui-amd64
86ae690636ed31c5d57739762193d9f098197ad8b6ba4d3b76d2f3a63a7fc64f
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/bootstrap-amd64
92a3cb2381578667ceb04dc9f2a34b0eb7f27289b1ba2140ba6f075b6e3e71c4
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/cli-amd64
1fcb0800dbbea5fb8310b13ea091599db75b425ea6cc7640eb53a850302bb134
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/postgres-reader-amd64
ee0679637a17b63ebf850d2987c6e6e3c50c3cbb9b4f1b2d3a0d4516d98cefae
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/postgres-writer-amd64
ba8649c958e2e1d974e76a4b107d9b2073829f3dc31048b24537949d131c4512
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/cassandra-reader-amd64
8dffba9623b9833eaeb264862cd1e3639a8e1d2f0aa3300ecd960f086c6872a0
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/cassandra-writer-amd64
83e0f4129a1f54620dda0892b8f31e37920a6ed5f2d2720cf271c1bc582253b5
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/mongodb-reader-amd64
3250c2d2277939051a0b47a72853f24ebb03be7c893d5a6a2d5b3ce371dca325
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/mongodb-writer-amd64
94b2fd8d1e6fbe2272d7bf921e6531a8ea8835f3a0e74de1936252b5a99e1a65
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/influxdb-reader-amd64
0e08504c0417a08feb7fd8f285ce762e00487c343b4e9f568abefe0e66aac851
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/influxdb-writer-amd64
0acf6f847cb343edd7fbdf85ef2d8f733fbeb239ee88f10fee291aecd4b868d8
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/lora-amd64
8f36bfe9f133ef1a82aea93278c7785ade392b5c1fbd68f95b5d88c315d9fec0
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/coap-amd64
01eaf38aa83a3fd08009f1ef0940514f75d21ff040fc3932b580400b1df79714
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/ws-amd64
531e1971dad242959092f27de4a71ea460873992b20d129847d7bd8fb7adc11a
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/normalizer-amd64
7b613120a6f0fa2ad5434fc772e241d9eb88a150309b59b08fc67f8c320dea27
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/http-amd64
1d4bd78f37ddd7fe7825a34771afdde2568bf89d9da7605c33eae36f2978f955
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/things-amd64
11cedbac4d9b5e794eef1853ce41ad983cbb0d37d895291ab294893b1d049bab
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d  mainflux/users-amd64
1ac6b4901c033ff8df892f9c80b88d08f9dc22e4d1318a2879979be6bae315e0
```

此时，查看下当前运行的容器列表：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo docker ps
CONTAINER ID        IMAGE                            COMMAND             CREATED             STATUS              PORTS               NAMES
0e08504c0417        mainflux/influxdb-reader-amd64   "/exe"              8 minutes ago       Up 8 minutes                            exciting_feynman
3250c2d22779        mainflux/mongodb-reader-amd64    "/exe"              9 minutes ago       Up 9 minutes                            dreamy_blackburn
86ae690636ed        mainflux/ui-amd64                "/entrypoint.sh"    13 minutes ago      Up 13 minutes       80/tcp              silly_elgamal
```

奇怪的是：启动容器并未失败，为何列举结果只有三个容器在运行？而且这三个容器并没有什么特别的特点。

另外一个未提及的是：mqtt服务的容器运行！

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/mqtt-amd64
D0813 04:04:20.242132755       1 env_linux.cc:71]            Warning: insecure environment read function 'getenv' used
{"level":30,"time":1565669060551,"msg":"listening","pid":1,"hostname":"4203c68fb764","address":"::","family":"IPv6","port":1883,"protocol":"tcp","v":1}
{"level":30,"time":1565669060552,"msg":"listening","pid":1,"hostname":"4203c68fb764","address":"::","family":"IPv6","port":8880,"protocol":"http","v":1}

events.js:174
      throw er; // Unhandled 'error' event
      ^
NatsError: Could not connect to server: Error: connect ECONNREFUSED 127.0.0.1:4222
    at Socket.stream.on (/node_modules/nats/lib/nats.js:722:32)
    at Socket.emit (events.js:189:13)
    at emitErrorNT (internal/streams/destroy.js:82:8)
    at emitErrorAndCloseNT (internal/streams/destroy.js:50:3)
    at process._tickCallback (internal/process/next_tick.js:63:19)
Emitted 'error' event at:
    at Socket.stream.on (/node_modules/nats/lib/nats.js:722:18)
    at Socket.emit (events.js:189:13)
    [... lines matching original stack trace ...]
    at process._tickCallback (internal/process/next_tick.js:63:19)
```

这是第一个尝试运行的镜像，失败了。本来想放到后边讨论。

但是，以后台方式运行试试？

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run -d mainflux/mqtt-amd64
544f66c4ed7445c62323ec37dd439ddf721629890e7a97f56bf9c7edcc1bb2d1
```

也得到一个容器哈希？？

猜测，前面列举正在运行容器时没有列举出来的也是遇错了，前台运行时有错误，但后台运行仍然给出了一个哈希值。

也就是说，真正运行成功的只有mainflux/ui-amd64、mainflux/mongodb-reader-amd64、mainflux/influxdb-reader-amd64

现在将前面没有在容器列表显示出来的所有镜像重新以前台运行方式运行一次：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/bootstrap-amd64
{"level":"error","message":"Failed to connect to postgres: dial tcp 127.0.0.1:5432: connect: connection refused","ts":"2019-08-13T04:11:25.13973277Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/cli-amd64
Usage:
  mainflux-cli [command]

Available Commands:
  channels    Channels management
  help        Help about any command
  messages    Send or read messages
  provision   Provision things and channels from config file
  things      Things management
  users       Users management
  version     Mainflux services version

Flags:
  -c, --content-type string    Mainflux message content type (default "application/senml+json")
  -h, --help                   help for mainflux-cli
  -a, --http-prefix string     Mainflux http adapter prefix (default "http")
  -i, --insecure               Do not check for TLS cert
  -l, --limit uint             limit query parameter (default 100)
  -m, --mainflux-url string    Mainflux host URL (default "http://localhost")
  -n, --name string            name query parameter
  -o, --offset uint            offset query parameter
  -t, --things-prefix string   Mainflux things service prefix
  -u, --users-prefix string    Mainflux users service prefix

Use "mainflux-cli [command] --help" for more information about a command.
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/postgres-reader-amd64
{"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-13T04:14:53.907162004Z"}
{"level":"error","message":"Failed to connect to Postgres: dial tcp 127.0.0.1:5432: connect: connection refused","ts":"2019-08-13T04:14:53.916560975Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/postgres-writer-amd64
2019/08/13 04:15:25 open /config/channels.toml: no such file or directory
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/cassandra-reader-amd64
{"level":"error","message":"Failed to connect to Cassandra cluster: gocql: unable to create session: unable to discover protocol version: dial tcp 127.0.0.1:9042: connect: connection refused","ts":"2019-08-13T04:15:51.903650669Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/cassandra-writer-amd64
2019/08/13 04:16:09 open /config/channels.toml: no such file or directory
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/mongodb-writer-amd64
2019/08/13 04:17:58 open /config/channels.toml: no such file or directory
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/influxdb-writer-amd64
2019/08/13 04:18:35 open /config/channels.toml: no such file or directory
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/lora-amd64
{"level":"error","message":"Failed to connect to NATS: nats: no servers available for connection","ts":"2019-08-13T04:18:54.906401806Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/coap-amd64
{"level":"error","message":"Failed to connect to NATS: nats: no servers available for connection","ts":"2019-08-13T04:19:11.762530473Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/ws-amd64
{"level":"error","message":"Failed to connect to NATS: nats: no servers available for connection","ts":"2019-08-13T04:19:29.355706676Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/normalizer-amd64
{"level":"error","message":"Failed to connect to NATS: nats: no servers available for connection","ts":"2019-08-13T04:19:45.747053109Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/http-amd64
{"level":"error","message":"Failed to connect to NATS: nats: no servers available for connection","ts":"2019-08-13T04:20:04.396013566Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/things-amd64
{"level":"error","message":"Failed to connect to postgres: dial tcp 127.0.0.1:5432: connect: connection refused","ts":"2019-08-13T04:20:19.80428741Z"}
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo docker run mainflux/users-amd64
{"level":"error","message":"Failed to connect to postgres: dial tcp 127.0.0.1:5432: connect: connection refused","ts":"2019-08-13T04:21:00.562341257Z"}
```

从输出信息可以看出：bootstrap无法连接至postgres；cli本身是没问题的，只不过它不是个后台进程，而是个交互式进程；postgres-reader连接postgres时被拒绝；postgres-writer找不到/config/channels.toml文件；cassandra-reader连接cassandra被拒绝；cassandra-writer找不到/config/channels.toml文件；mongodb-writer、influxdb-writer也找不到/config/channels.toml文件；lora、coap、ws、normalizer、http连接不到nats服务器；things和users连接不到postgres。

对了，mqtt的报错信息其实是在event.js文件中连接不到127.0.0.1:4222，并且在其调用路径中看到下一级是nats.go抛出错误，这说明mqtt其实也是连接不到nats服务器的问题。

综上： 需要安装并启动postgres、cassandra、nats，检查/config/channels.toml。

参照[10. postgres、cassandra、nats安装](#10-postgrescassandranats%e5%ae%89%e8%a3%85)安装完成这三个软件服务。

注意：在安装cassandra的过程中发现cassandra默认端口是7199，而在上面的报错信息中cassandra使用的是端口9042，这点后续需要注意！留待修改。 nats和postgres用的都是默认端口所以不用管。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ ls
api.go        cli              doc.go  Gopkg.lock      internal.proto  logger       message.go     mkdocs.yml    package-lock.json  scripts    ui          writers
bootstrap     cmd              docker  Gopkg.toml      k8s             lora         message.pb.go  mqtt          publisher.go       sdk        users       ws
build         coap             docs    http            LICENSE         MAINTAINERS  message.proto  node_modules  readers            things     vendor
CHANGELOG.md  CONTRIBUTING.md  env.go  internal.pb.go  load-test       Makefile     metrics        normalizer    README.md          topics.go  version.go
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ cd config
bash: cd: config: No such file or directory
```

显示mainflux项目目录下没有config文件夹 :(

现在我们切换到/home/eiger/gopath-default/src/github.com/mainflux/mainflux/config来看看这个channels.toml是个什么情况。

前边的输出信息显示四个数据库软件（postgres、cassandra、mongodb、influxdb）的writer服务都找不到config/channels.toml。只看其中一个服务就好了，选择最熟悉的mongodb-writer去看其源代码。

mongodb-writer部分代码主要分cmd/mongodb-writer/main.go（入口文件）和writers/api、writers/mongodb、writers/。

先看入口文件，在入口文件中有定义：

```go
const (
    svcName = "mongodb-writer"

    defNatsURL     = nats.DefaultURL
    defLogLevel    = "error"
    defPort        = "8180"
    defDBName      = "mainflux"
    defDBHost      = "localhost"
    defDBPort      = "27017"
    defChanCfgPath = "/config/channels.toml"

    envNatsURL     = "MF_NATS_URL"
    envLogLevel    = "MF_MONGO_WRITER_LOG_LEVEL"
    envPort        = "MF_MONGO_WRITER_PORT"
    envDBName      = "MF_MONGO_WRITER_DB_NAME"
    envDBHost      = "MF_MONGO_WRITER_DB_HOST"
    envDBPort      = "MF_MONGO_WRITER_DB_PORT"
    envChanCfgPath = "MF_MONGO_WRITER_CHANNELS_CONFIG"
)

func loadConfigs() config {
  chanCfgPath := mainflux.Env(envChanCfgPath, defChanCfgPath)
  return config{
    natsURL:  mainflux.Env(envNatsURL, defNatsURL),
    logLevel: mainflux.Env(envLogLevel, defLogLevel),
    port:     mainflux.Env(envPort, defPort),
    dbName:   mainflux.Env(envDBName, defDBName),
    dbHost:   mainflux.Env(envDBHost, defDBHost),
    dbPort:   mainflux.Env(envDBPort, defDBPort),
    channels: loadChansConfig(chanCfgPath),
  }
}

func loadChansConfig(chanConfigPath string) map[string]bool {
  data, err := ioutil.ReadFile(chanConfigPath)
  if err != nil {
    log.Fatal(err)
  }

  var chanCfg chanConfig
  if err := toml.Unmarshal(data, &chanCfg); err != nil {
    log.Fatal(err)
  }

  chans := map[string]bool{}
  for _, ch := range chanCfg.Channels.List {
    chans[ch] = true
  }

  return chans
}
```

直接可以看到其配置文件路径已经在代码文件中写入了。那说明config/channels.toml是代码内决定的配置文件，但是我们从github拉取项目却没得到这个文件。这是因为通常配置文件都不会上传到项目中。这表示我们现在需要进一步查看mainflux使用文档，想办法生成这个channels.toml。

在官方文档<https://mainflux.readthedocs.io/en/latest/getting-started/>中可以看到

## 8. mainflux安装

安装GO二进制可执行文件非常容易，只需要将build生成的所有可执行文件手动复制到`$GOBIN`目录下并确保`GOBIN`被添加到`$PATH`变量。

当然，也可以执行

```shell
make install
```

自动完成/mainflux/build下二进制文件复制到$GOBIN操作。

但是注意：mqtt adapter是nodejs脚本，仍保留在/mainflux/mqtt/aedes而不被复制。

执行完`make install`后，可以发现确实将文件复制到了$GOBIN下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ ls ~/gopath-default/bin
protoc-gen-go  protoc-gen-gofast
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make install
cp build/* /home/eiger/gopath-default/bin
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ ls ~/gopath-default/bin
mainflux-bootstrap         mainflux-cli   mainflux-influxdb-reader  mainflux-mongodb-reader  mainflux-postgres-reader  mainflux-users  protoc-gen-gofast
mainflux-cassandra-reader  mainflux-coap  mainflux-influxdb-writer  mainflux-mongodb-writer  mainflux-postgres-writer  mainflux-ws
mainflux-cassandra-writer  mainflux-http  mainflux-lora             mainflux-normalizer      mainflux-things           protoc-gen-go
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$
```

## 9. mainflux部署

### 9.1 前提

## 10. postgres、cassandra、nats安装

在[7. mainflux运行测试](#7-mainflux%e8%bf%90%e8%a1%8c%e6%b5%8b%e8%af%95)中我们发现需要安装postgres、cassandra、nats三个软件。

### 10.1.安装postgres

postgres，或者称PostgreSQL，是一个关系型数据库管理系统，提供SQL查询语言及其他各种高级功能。具体描述参见其官方资料。

安装方法：

```shell
sudo apt update
sudo apt install postgres postgres-contrib
```

安装输出：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo apt install postgresql postgresql-contrib
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libpq5 postgresql-10 postgresql-client-10 postgresql-client-common
  postgresql-common sysstat
Suggested packages:
  postgresql-doc locales-all postgresql-doc-10 libjson-perl isag
The following NEW packages will be installed:
  libpq5 postgresql postgresql-10 postgresql-client-10
  postgresql-client-common postgresql-common postgresql-contrib sysstat
0 upgraded, 8 newly installed, 0 to remove and 7 not upgraded.
Need to get 5,293 kB of archives.
After this operation, 20.9 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates/main amd64 libpq5 amd64 10.10-0ubuntu0.18.04.1 [108 kB]
Get:2 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/main amd64 postgresql-client-common all 190 [29.5 kB]
Get:3 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates/main amd64 postgresql-client-10 amd64 10.10-0ubuntu0.18.04.1 [935 kB]
Get:4 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/main amd64 postgresql-common all 190 [157 kB]
Get:5 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates/main amd64 postgresql-10 amd64 10.10-0ubuntu0.18.04.1 [3,758 kB]
Get:6 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/main amd64 postgresql all 10+190 [5,784 B]
Get:7 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/main amd64 postgresql-contrib all 10+190 [5,796 B]
Get:8 http://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/main amd64 sysstat amd64 11.6.1-1 [295 kB]
Fetched 5,293 kB in 5s (1,037 kB/s)
Preconfiguring packages ...
Selecting previously unselected package libpq5:amd64.
(Reading database ... 174645 files and directories currently installed.)
Preparing to unpack .../0-libpq5_10.10-0ubuntu0.18.04.1_amd64.deb ...
Unpacking libpq5:amd64 (10.10-0ubuntu0.18.04.1) ...
Selecting previously unselected package postgresql-client-common.
Preparing to unpack .../1-postgresql-client-common_190_all.deb ...
Unpacking postgresql-client-common (190) ...
Selecting previously unselected package postgresql-client-10.
Preparing to unpack .../2-postgresql-client-10_10.10-0ubuntu0.18.04.1_amd64.deb ...
Unpacking postgresql-client-10 (10.10-0ubuntu0.18.04.1) ...
Selecting previously unselected package postgresql-common.
Preparing to unpack .../3-postgresql-common_190_all.deb ...
Adding 'diversion of /usr/bin/pg_config to /usr/bin/pg_config.libpq-dev by postgresql-common'
Unpacking postgresql-common (190) ...
Selecting previously unselected package postgresql-10.
Preparing to unpack .../4-postgresql-10_10.10-0ubuntu0.18.04.1_amd64.deb ...
Unpacking postgresql-10 (10.10-0ubuntu0.18.04.1) ...
Selecting previously unselected package postgresql.
Preparing to unpack .../5-postgresql_10+190_all.deb ...
Unpacking postgresql (10+190) ...
Selecting previously unselected package postgresql-contrib.
Preparing to unpack .../6-postgresql-contrib_10+190_all.deb ...
Unpacking postgresql-contrib (10+190) ...
Selecting previously unselected package sysstat.
Preparing to unpack .../7-sysstat_11.6.1-1_amd64.deb ...
Unpacking sysstat (11.6.1-1) ...
Setting up sysstat (11.6.1-1) ...

Creating config file /etc/default/sysstat with new version
update-alternatives: using /usr/bin/sar.sysstat to provide /usr/bin/sar (sar) in auto mode
Created symlink /etc/systemd/system/multi-user.target.wants/sysstat.service → /lib/systemd/system/sysstat.service.
Processing triggers for ureadahead (0.100.0-21) ...
Setting up libpq5:amd64 (10.10-0ubuntu0.18.04.1) ...
Processing triggers for libc-bin (2.27-3ubuntu1) ...
Setting up postgresql-client-common (190) ...
Processing triggers for systemd (237-3ubuntu10.25) ...
Setting up postgresql-common (190) ...
Adding user postgres to group ssl-cert

Creating config file /etc/postgresql-common/createcluster.conf with new version
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
  en_us
Removing obsolete dictionary files:
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system/postgresql.service.
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Setting up postgresql-client-10 (10.10-0ubuntu0.18.04.1) ...
update-alternatives: using /usr/share/postgresql/10/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up postgresql-10 (10.10-0ubuntu0.18.04.1) ...
Creating new PostgreSQL cluster 10/main ...
/usr/lib/postgresql/10/bin/initdb -D /var/lib/postgresql/10/main --auth-local peer --auth-host md5
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/10/main ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default timezone ... America/New_York
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

Success. You can now start the database server using:

    /usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile start

Ver Cluster Port Status Owner    Data directory              Log file
10  main    5432 down   postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
update-alternatives: using /usr/share/postgresql/10/man/man1/postmaster.1.gz to provide /usr/share/man/man1/postmaster.1.gz (postmaster.1.gz) in auto mode
Setting up postgresql (10+190) ...
Setting up postgresql-contrib (10+190) ...
Processing triggers for ureadahead (0.100.0-21) ...
Processing triggers for systemd (237-3ubuntu10.25) ...
```

从中可以获取到一些postgres的默认信息。比较重要的是：

```shell
Success. You can now start the database server using:

    /usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile start

Ver Cluster Port Status Owner    Data directory              Log file
10  main    5432 down   postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
```

启动方法：

```shell
/usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile start
```

版本信息：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ psql --version
psql (PostgreSQL) 10.10 (Ubuntu 10.10-0ubuntu0.18.04.1)
```

测试服务器是否启动：

```shell
sudo apt install nmap  ## 安装nmap工具，安装过就不用再安装
nmap 127.0.0.1
```

执行结果：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ nmap 127.0.0.1

Starting Nmap 7.60 ( https://nmap.org ) at 2019-08-13 02:21 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00018s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
80/tcp   open  http
631/tcp  open  ipp
5432/tcp open  postgresql
7070/tcp open  realserver

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
```

可以看到，在127.0.0.1这个本机ip下开启了5432端口，用于postgresql的tcp监听。

注意，安装完成后，默认创建了postgres用户，且只有在该用户下才可以访问postgresql数据库。

通过`sudo nano /etc/shadow`可以查看到文件末尾有postgres用户名的存在。（ps: 关于linux用户名用户组相关，参阅<https://www.cnblogs.com/jackyyou/p/5498083.html>）

- 修改postgres用户密码（最开始是随机密码）：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo -u postgres psql
    psql (10.10 (Ubuntu 10.10-0ubuntu0.18.04.1))
    Type "help" for help.

    postgres=# ALTER USER postgres WITH PASSWORD 'YOURPASSWDSTRING';
    ALTER ROLE
    postgres=# \q
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$
    ```

    这样，postgres中postgres这个用户的密码就修改成我们想要的了，现在还需要修改电脑系统中postgres这个用户的密码，与前一致：

    ```shell
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo su
    [sudo] password for eiger:
    root@eiger-ThinkPad-X1-Carbon-3rd:/home/eiger# sudo passwd -d postgres
    passwd: password expiry information changed.
    root@eiger-ThinkPad-X1-Carbon-3rd:/home/eiger# sudo -u postgres passwd
    Enter new UNIX password:
    Retype new UNIX password:
    passwd: password updated successfully
    root@eiger-ThinkPad-X1-Carbon-3rd:/home/eiger# su postgres
    postgres@eiger-ThinkPad-X1-Carbon-3rd:/home/eiger$ psql
    psql (10.10 (Ubuntu 10.10-0ubuntu0.18.04.1))
    Type "help" for help.

    postgres=# \q
    postgres@eiger-ThinkPad-X1-Carbon-3rd:/home/eiger$ su eiger
    Password:
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ psql
    psql: FATAL:  role "eiger" does not exist
    eiger@eiger-ThinkPad-X1-Carbon-3rd:~$
    ```

    如此，切换成postgre系统用户，即可登录使用psql。

关于postgres的使用，暂略。

### 10.2. 安装cassandra

安装参考：官方文档及博文<https://blog.csdn.net/yamaxifeng_132/article/details/78824579>

Cassandra是Facebook开发的开源分布式NoSQL（非关系型）数据库，基于Java开发，没有提供windows安装版本。

安装Cassandra需要先安装好Java的环境，参见[10.4 jdk12安装](#104-%e5%ae%89%e8%a3%85java%e5%bc%80%e5%8f%91%e7%8e%af%e5%a2%83java-12)（后面发现安装错了，如果想一次到位，直接安装jdk8，参见[10.5 jdk8安装](#105-%e5%ae%89%e8%a3%85java%e5%bc%80%e5%8f%91%e7%8e%af%e5%a2%83java-8)）

cassandra下载：<http://cassandra.apache.org/download/>
选择最新的发行版下载即可。我下载的是当前最新发行版3.11.4。
解压后，打开/apache-cassandra-3.11.4/conf/cassandra-env.sh，可以看到：

```shell
if [ "$JVM_VERSION" \< "1.8" ] ; then
    echo "Cassandra 3.0 and later require Java 8u40 or later."
    exit 1;
fi

if [ "$JVM_VERSION" \< "1.8" ] && [ "$JVM_PATCH_VERSION" -lt 40 ] ; then
    echo "Cassandra 3.0 and later require Java 8u40 or later."
    exit 1;
fi

```

从中可看出当前版本的cassandra要求jdk 8u40或更高版本，前面所安装的java符合要求。

先将cassandra解压目录复制到/usr/local下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ sudo cp -r apache-cassandra-3.11.4 /usr/local
[sudo] password for eiger:
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ ls /usr/local
apache-cassandra-3.11.4  bin  etc  games  go  include  lib  man  protobuf-3.9.1-linux-x86_64  sbin  share  src
```

接下来设置环境变量，在/etc/profile文件末尾追加：

```shell
export CASSANDRA_HOME=/usr/local/apache-cassandra-3.11.4
export PATH=$PATH:${CASSANDRA_HOME}/bin
```

接下来是测试cassandra的安装：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ cassandra
cassandra: command not found
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4/bin$ ./cassandra
Cassandra 3.0 and later require Java 8u40 or later.
```

从结果可以看出，首先环境变量的设置是失败的，但仔细比对后，发现设置并未出错。再次输入`env`来查看环境变量：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4/bin$ env
CLUTTER_IM_MODULE=xim
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_MENU_PREFIX=gnome-
LANG=en_US.UTF-8
DISPLAY=:0
OLDPWD=/usr/local/apache-cassandra-3.11.4
GNOME_SHELL_SESSION_MODE=ubuntu
COLORTERM=truecolor
USERNAME=eiger
GOPROXY=https://goproxy.io
XDG_VTNR=1
SSH_AUTH_SOCK=/run/user/1000/keyring/ssh
XDG_SESSION_ID=1
USER=eiger
DESKTOP_SESSION=ubuntu
QT4_IM_MODULE=fcitx
GOPATH=/home/eiger/gopath-default
TEXTDOMAINDIR=/usr/share/locale/
GNOME_TERMINAL_SCREEN=/org/gnome/Terminal/screen/186c6fb3_93c3_47ec_820d_c58dcdd15bf3
PWD=/usr/local/apache-cassandra-3.11.4/bin
HOME=/home/eiger
GOROOT=/usr/local/go
TEXTDOMAIN=im-config
SSH_AGENT_PID=958
QT_ACCESSIBILITY=1
XDG_SESSION_TYPE=x11
XDG_DATA_DIRS=/usr/share/ubuntu:/usr/local/share:/usr/share:/var/lib/snapd/desktop
XDG_SESSION_DESKTOP=ubuntu
GJS_DEBUG_OUTPUT=stderr
GTK_MODULES=gail:atk-bridge
WINDOWPATH=1
TERM=xterm-256color
SHELL=/bin/bash
VTE_VERSION=5202
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
IM_CONFIG_PHASE=2
XDG_CURRENT_DESKTOP=ubuntu:GNOME
GPG_AGENT_INFO=/run/user/1000/gnupg/S.gpg-agent:0:1
GNOME_TERMINAL_SERVICE=:1.85
XDG_SEAT=seat0
SHLVL=1
GDMSESSION=ubuntu
GNOME_DESKTOP_SESSION_ID=this-is-deprecated
LOGNAME=eiger
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
XDG_RUNTIME_DIR=/run/user/1000
XAUTHORITY=/run/user/1000/gdm/Xauthority
GOBIN=/home/eiger/gopath-default/bin
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:/home/eiger/gopath-default/bin:/usr/local/protobuf-3.9.1-linux-x86_64/bin
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
SESSION_MANAGER=local/eiger-ThinkPad-X1-Carbon-3rd:@/tmp/.ICE-unix/868,unix/eiger-ThinkPad-X1-Carbon-3rd:/tmp/.ICE-unix/868
LESSOPEN=| /usr/bin/lesspipe %s
GTK_IM_MODULE=fcitx
_=/usr/bin/env
```

很不幸的是，PATH中不仅没有CASSANDRA，连之前设置了的JDK也没了。我们再次打开/etc/profile看下目前的环境变量设置情况：

```shell
# go environment
# export PATH=$PATH:/usr/local/go/bin
export GOROOT=/usr/local/go
export GOPATH=/home/eiger/gopath-default
export GOBIN=$GOPATH/bin
export GOPROXY=https://goproxy.io
export PATH=$PATH:$GOROOT/bin
export PATH=$PATH:$GOPATH/bin
# protoc
export PATH=$PATH:/usr/local/protobuf-3.9.1-linux-x86_64/bin
# cassandra
export CASSANDRA_HOME=/usr/local/apache-cassandra-3.11.4
export PATH=$PATH:${CASSANDRA_HOME}/bin
# jdk
export JAVA_HOME=/usr/lib/jvm/jdk-12.0.2
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=$PATH:${JAVA_HOME}/bin
```

看了下并没有问题，重新执行一遍`source /etc/profile`再查看env：

```shell
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:/home/eiger/gopath-default/bin:/usr/local/protobuf-3.9.1-linux-x86_64/bin:/usr/local/go/bin:/home/eiger/gopath-default/bin:/usr/local/protobuf-3.9.1-linux-x86_64/bin:/usr/local/apache-cassandra-3.11.4/bin:/usr/lib/jvm/jdk-12.0.2/bin
```

又正常了，神奇...

现在试试在任意位置直接调用cassandra：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ [0.000s][warning][gc] -Xloggc is deprecated. Will use -Xlog:gc:/usr/local/apache-cassandra-3.11.4/logs/gc.log instead.
intx ThreadPriorityPolicy=42 is outside the allowed range [ 0 ... 1 ]
Improperly specified VM option 'ThreadPriorityPolicy=42'
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```

结果是能调用，但是报错了。而且错误和前边`${CASSANDRA_HOME}/bin`下执行`./cassandra`报的错还不一样。

根据前面的错误，提示说需要JAVA 8u40或往后版本，可能前面的想法是错的，它要求的是统一大版本下的更高版本，而不能是更高大版本，也就是说我安装JAVA 12是安装了一个不符要求的版本。这样的话，参照session 10.5安装java 8，过程略过。

安装好java8后重新进行测试：

- 执行`cassandra`后输出了很长的信息，这里不展示，大致意思是各种权限不足，因此加上sudo，但蛋疼的是加上sudo之后提示cassandra命令找不到，这和前面使用docker时一样，都遇到了加上sudo找不到命令的问题。

```shell
# 输入cassandra后产生了很长的报错信息
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ cassandra
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ Java HotSpot(TM) 64-Bit Server VM warning: Cannot open file /usr/local/apache-cassandra-3.11.4/logs/gc.log due to No such file or directory
# sudo cassandra找不到命令
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo cassandra
sudo: cassandra: command not found
```

参照[11.1 sudo+xxx:找不到xxx命令](#111-sudoxxx%e6%89%be%e4%b8%8d%e5%88%b0xxx%e5%91%bd%e4%bb%a4)解决该问题后，重新输入`sudo cassandra`测试：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ sudo cassandra
Running Cassandra as root user or group is not recommended - please start Cassandra using a different system user.
If you really want to force running Cassandra as root, use -R command line option.
```

现在没有问题了，尽管cassandra发出警告说不建议使用root用户运行cassandra，但是不管了，反正只是测试用。

输入：

```shell
sudo cassandra -R
```

结果仍然是：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ sudo cassandra -R
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ Java HotSpot(TM) 64-Bit Server VM warning: Cannot open file /usr/local/apache-cassandra-3.11.4/logs/gc.log due to No such file or directory
```

还是尝试下提升当前用户权限`sudo chown -R $USER:$(id -gn $USER) /home/eiger/.config`是否可以让`cassandra`运行成功，但是仍然报错，从报错信息中分析得出，问题出在无论是eiger用户还是root用户都无法在/usr/local/cassandra_homedir下创建目录、写入数据，从这一点看，修改该目录及其子目录的读写权限应该是可以解决问题的。

同时在stackoverflow上发现一个问题求助： <https://stackoverflow.com/questions/47976248/gc-log-file-error-when-running-cassandra>，与当前遇到的问题一致。

其中给出的解决方法及解释是：

cassandra用一个gc.log文件来记录所有的gc（垃圾回收）日志， gc.log文件路径在cassandra-env.sh文件中定义：

```shell
#GC log path has to be defined here because it needs to access CASSANDRA_HOME
JVM_OPTS="$JVM_OPTS -Xloggc:${CASSANDRA_HOME}/logs/gc.log"
```

据此给出的解决方法就是：确保cassandra目录下有logs文件夹，并确保它能被cassandra修改写入。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ ls
bin                  conf  interface  LICENSE.txt  NOTICE.txt
CASSANDRA-14092.txt  data  javadoc    logs         pylib
CHANGES.txt          doc   lib        NEWS.txt     tools
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ ls logs
debug.log  system.log
```

先查看logs文件夹权限：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ ll logs
total 740
drwxr-xr-x  2 root root   4096 Aug 13 23:42 ./
drwxr-xr-x 12 root root   4096 Aug 13 23:58 ../
-rw-r--r--  1 root root 701303 Aug 13 23:43 debug.log
-rw-r--r--  1 root root  42050 Aug 13 23:43 system.log
```

显然，logs文件夹及内容仅归root用户所有。合理的做法当然是为需要使用cassandra的用户提升写权限，但是这里就直接提升所有用户权限了：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ sudo chmod -R 777 logs
[sudo] password for eiger:
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ ll logs
total 740
drwxrwxrwx  2 root root   4096 Aug 13 23:42 ./
drwxr-xr-x 12 root root   4096 Aug 13 23:58 ../
-rwxrwxrwx  1 root root 701303 Aug 13 23:43 debug.log*
-rwxrwxrwx  1 root root  42050 Aug 13 23:43 system.log*
```

我们再来执行下`cassandra`试试，这一次没有再出现找不到logs/gc.log的问题，但是在冗长的输出信息中有了一个新的错误：

```shell
# 前边省略
ERROR [main] 2019-08-14 00:40:31,492 CassandraDaemon.java:749 - Port already in use: 7199; nested exception is:
    java.net.BindException: Address already in use (Bind failed)
java.net.BindException: Address already in use (Bind failed)
# 后边省略
```

显示7199端口已经被占用。嗯...现在我们需要去查看下7199端口被谁用了，再考虑是否修改cassandra端口。

先使用nmap查看下localhost的端口使用情况：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ nmap 127.0.0.1

Starting Nmap 7.60 ( https://nmap.org ) at 2019-08-14 00:51 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00014s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
631/tcp  open  ipp
5432/tcp open  postgresql
7000/tcp open  afs3-fileserver
7070/tcp open  realserver

Nmap done: 1 IP address (1 host up) scanned in 0.06 seconds
```

奇怪的是，7199并未被占用。再用`lsof -i:7199`和`netstat -a | grep 7199`查看下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ lsof -i:7199
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ netstat -a | grep 7199
tcp        0      0 localhost:7199          0.0.0.0:*               LISTEN
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ netstat -ap | grep 7199
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 localhost:7199          0.0.0.0:*               LISTEN      -
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo netstat -ap | grep 7199
[sudo] password for eiger:
tcp        0      0 localhost:7199          0.0.0.0:*               LISTEN      26555/java
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$
```

这说明端口7199被一个java进程监听，但并未建立连接。按照目前的安装进度可以确定java只被cassandra调用了，也就是说7199应该就是被cassandra使用的，这很奇怪...

现在尝试将使用7199端口的程序关闭

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo kill -9 26555
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo netstat -ap | grep 7199
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$
```

再来启动下cassandra，输出了一长串信息，好的是不再报端口被占用的错，但出现了新状况：

```shell
# 直接运行`cassandra`
# 前边省略
WARN  [main] 2019-08-14 02:15:07,571 NativeLibrary.java:187 - Unable to lock JVM memory (ENOMEM). This can result in part of the JVM being swapped out, especially with mmapped I/O enabled. Increase RLIMIT_MEMLOCK or run Cassandra as root.
WARN  [main] 2019-08-14 02:15:07,572 StartupChecks.java:136 - jemalloc shared library could not be preloaded to speed up memory allocations
WARN  [main] 2019-08-14 02:15:07,573 StartupChecks.java:169 - JMX is not enabled to receive remote connections. Please see cassandra-env.sh for more info.
INFO  [main] 2019-08-14 02:15:07,576 SigarLibrary.java:44 - Initializing SIGAR library
WARN  [main] 2019-08-14 02:15:07,599 SigarLibrary.java:174 - Cassandra server running in degraded mode. Is swap disabled? : false,  Address space adequate? : true,  nofile limit adequate? : false, nproc limit adequate? : false
WARN  [main] 2019-08-14 02:15:07,606 StartupChecks.java:311 - Maximum number of memory map areas per process (vm.max_map_count) 65530 is too low, recommended value: 1048575, you can change it with sysctl.
ERROR [main] 2019-08-14 02:15:07,625 Directories.java:130 - Doesn't have write permissions for /usr/local/apache-cassandra-3.11.4/data/data directory
ERROR [main] 2019-08-14 02:15:07,626 CassandraDaemon.java:749 - Insufficient permissions on directory /usr/local/apache-cassandra-3.11.4/data/data
```

警告内容主要关乎cassandra的一些配置，当正式需要使用它时再关心这些问题。

错误内容是当前用户eiger对`/cassandra/data/data`没有写权限，嗯...设置下权限好了：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ cd /usr/local/apache-cassandra-3.11.4/
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ ls
bin  CASSANDRA-14092.txt  CHANGES.txt  conf  data  doc  interface  javadoc  lib  LICENSE.txt  logs  NEWS.txt  NOTICE.txt  pylib  tools
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ sudo chmod -R 777 data
eiger@eiger-ThinkPad-X1-Carbon-3rd:/usr/local/apache-cassandra-3.11.4$ ll data
total 24
drwxrwxrwx  6 root root 4096 Aug 13 23:43 ./
drwxr-xr-x 12 root root 4096 Aug 13 23:58 ../
drwxrwxrwx  2 root root 4096 Aug 13 23:43 commitlog/
drwxrwxrwx  7 root root 4096 Aug 13 23:43 data/
drwxrwxrwx  2 root root 4096 Aug 13 23:43 hints/
drwxrwxrwx  2 root root 4096 Aug 13 23:43 saved_caches/
```

再执行下`cassandra`试试：

```shell
# 直接运行`cassandra`
# 前边省略
INFO  [main] 2019-08-14 02:22:15,674 StorageService.java:1483 - JOINING: Finish joining ring
INFO  [main] 2019-08-14 02:22:15,835 StorageService.java:2327 - Node localhost/127.0.0.1 state jump to NORMAL
```

在输出信息中只看到几行警告（关于远程调用的一些东西，因为cassandra是个分布式的数据库，我们这里只是在本地开启了一个cassandra节点），其余正常，而且，根据网上关于安装cassandra的一些博客，当看到最后输出这两行信息，那说明cassandra正常启动了。

总算完成了，这就是个大坑！

### 10.3. 安装nats

nats是一个开源的消息中间件，或者叫消息系统。在这类系统中，一个或多个消息发布者将特定主题消息发送给一个消息终结者，消息中介者再将消息分发给任意客户端（主题的订阅者），这样的结构使得消息发布和消息订阅解耦合，大大提升了系统的伸缩性和扩容性。

下载地址：<https://github.com/nats-io/nats-server/releases>， 选择linux-amd64版本下载。
解压后可以看到内部只有一个nats-server可执行文件和license以及readme文件，那么只要把nats-server复制到指定目录即可（放哪都可以，只不过启动路径不同）。我选择将其复制到/usr/local/bin下，因为这个目录是默认的PATH，不需要自己再导出为环境变量了。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads$ cd nats-server-v2.0.2-linux-amd64/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads/nats-server-v2.0.2-linux-amd64$ ls
LICENSE  nats-server  README.md
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads/nats-server-v2.0.2-linux-amd64$ sudo cp nats-server /usr/local/bin
[sudo] password for eiger:
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/Downloads/nats-server-v2.0.2-linux-amd64$ cd ~
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ nats-server
[14426] 2019/08/13 04:08:08.391981 [INF] Starting nats-server version 2.0.2
[14426] 2019/08/13 04:08:08.392040 [INF] Git commit [not set]
[14426] 2019/08/13 04:08:08.392148 [INF] Listening for client connections on 0.0.0.0:4222
[14426] 2019/08/13 04:08:08.392156 [INF] Server id is NDPVONLH4TUGRYY357FPWWGIZWUQLGLIBJIDSDXFLVVPS2P72C7YZNA5
[14426] 2019/08/13 04:08:08.392160 [INF] Server is ready
```

现在nats-server就安装好了。默认端口是4222。

其他方式安装及用法等参考<https://nats-io.github.io/docs/nats_server/installation.html>

### 10.4. 安装Java开发环境（Java 12）

首先介绍下Java开发环境。经常看到JDK、JRE，而且还有SE、EE等等版本。他们的关系是什么呢？

JDK指Java Development Kit，开发套件；JRE指Java Runtime Environment，运行时环境。通常，开发时使用JDK来进行应用开发及测试，JRE则用于实际生产环境中的部署。用集合的概念描述的话，JRE是JDK的子集，JDK多了很多开发用的东西，比如说调试工具。

而EE、SE等版本，SE是标准版，免费使用，而EE是企业版，面向企业，收费应用。

因此，我们通常讲的配置Java开发环境往往指的是安装JDK SE。

首先到官网下载jdk包 <https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html>，
我所使用的电脑可以下载linux-amd64版本的.tar.gz包、.deb包、.rpm包。
这里建议下载.tar.gz压缩包，自己解压后放到一个路径比如说/usr/local下，这样自己很清楚装在哪。

当然我下载了.deb包，由于网速较差，就不再下.tar.gz了。

安装jdk的deb包或者rpm的坑在于：它的安装信息接近于0，既不告诉你安装到哪去了，也不自动帮你注册环境变量。

所以当你安装完成，兴冲冲地输入`java --version`，很遗憾，该命令不存在。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/tmp/mozilla_eiger0$ sudo dpkg -i jdk-12.0.2_linux-x64_bin.deb
(Reading database ... 176755 files and directories currently installed.)
Preparing to unpack jdk-12.0.2_linux-x64_bin.deb ...
Unpacking jdk-12.0.2 (12.0.2-1) over (12.0.2-1) ...
Setting up jdk-12.0.2 (12.0.2-1) ...
eiger@eiger-ThinkPad-X1-Carbon-3rd:/tmp/mozilla_eiger0$ java --version

Command 'java' not found, but can be installed with:

sudo apt install default-jre
sudo apt install openjdk-11-jre-headless
sudo apt install openjdk-8-jre-headless
```

(ps: 虽然apt提供了openjdk和openjre的直接下载，但要注意openjdk和jdk之间的关系，openjdk是jdk中最基本的部分，缺少一些内容，为了避免踩坑，还是老老实实安装jdk、jre)

好了，想办法找到jdk安装到哪个地方吧：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/tmp/mozilla_eiger0$ sudo dpkg -L jdk-12.0.2
/.
/usr
/usr/lib
/usr/lib/jvm
/usr/lib/jvm/jdk-12.0.2
/usr/lib/jvm/jdk-12.0.2/release
/usr/lib/jvm/jdk-12.0.2/jmods
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jlink.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jdwp.agent.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jstatd.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.unsupported.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.smartcardio.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.sctp.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.naming.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.management.agent.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.jvmstat.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.pack.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.le.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.naming.dns.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.transaction.xa.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.net.http.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.sql.rowset.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.localedata.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.security.jgss.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.ed.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.naming.rmi.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.management.rmi.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.security.sasl.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jshell.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.vm.compiler.management.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.hotspot.agent.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.vm.ci.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.desktop.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.security.auth.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.security.jgss.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jfr.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.rmic.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.rmi.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.scripting.nashorn.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.aot.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jconsole.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.crypto.cryptoki.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.base.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.xml.dom.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jdeps.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.management.jfr.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.se.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.zipfs.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.instrument.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.editpad.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.sql.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.management.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.javadoc.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.crypto.ec.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jartool.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.charsets.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.xml.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.attach.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.datatransfer.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jsobject.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.dynalink.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.logging.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.opt.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.unsupported.desktop.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.compiler.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.net.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jcmd.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.scripting.nashorn.shell.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.compiler.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.jdi.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.management.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.prefs.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.internal.vm.compiler.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.xml.crypto.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.accessibility.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/jdk.httpserver.jmod
/usr/lib/jvm/jdk-12.0.2/jmods/java.scripting.jmod
/usr/lib/jvm/jdk-12.0.2/bin
/usr/lib/jvm/jdk-12.0.2/bin/rmiregistry
/usr/lib/jvm/jdk-12.0.2/bin/rmic
/usr/lib/jvm/jdk-12.0.2/bin/jfr
/usr/lib/jvm/jdk-12.0.2/bin/java
/usr/lib/jvm/jdk-12.0.2/bin/jinfo
/usr/lib/jvm/jdk-12.0.2/bin/unpack200
/usr/lib/jvm/jdk-12.0.2/bin/jimage
/usr/lib/jvm/jdk-12.0.2/bin/jhsdb
/usr/lib/jvm/jdk-12.0.2/bin/javadoc
/usr/lib/jvm/jdk-12.0.2/bin/jps
/usr/lib/jvm/jdk-12.0.2/bin/jarsigner
/usr/lib/jvm/jdk-12.0.2/bin/jmod
/usr/lib/jvm/jdk-12.0.2/bin/jdb
/usr/lib/jvm/jdk-12.0.2/bin/jcmd
/usr/lib/jvm/jdk-12.0.2/bin/jjs
/usr/lib/jvm/jdk-12.0.2/bin/jmap
/usr/lib/jvm/jdk-12.0.2/bin/jdeprscan
/usr/lib/jvm/jdk-12.0.2/bin/javap
/usr/lib/jvm/jdk-12.0.2/bin/jconsole
/usr/lib/jvm/jdk-12.0.2/bin/jrunscript
/usr/lib/jvm/jdk-12.0.2/bin/jstat
/usr/lib/jvm/jdk-12.0.2/bin/keytool
/usr/lib/jvm/jdk-12.0.2/bin/jlink
/usr/lib/jvm/jdk-12.0.2/bin/jdeps
/usr/lib/jvm/jdk-12.0.2/bin/rmid
/usr/lib/jvm/jdk-12.0.2/bin/jstack
/usr/lib/jvm/jdk-12.0.2/bin/jar
/usr/lib/jvm/jdk-12.0.2/bin/jstatd
/usr/lib/jvm/jdk-12.0.2/bin/jaotc
/usr/lib/jvm/jdk-12.0.2/bin/serialver
/usr/lib/jvm/jdk-12.0.2/bin/jshell
/usr/lib/jvm/jdk-12.0.2/bin/pack200
/usr/lib/jvm/jdk-12.0.2/bin/javac
/usr/lib/jvm/jdk-12.0.2/conf
/usr/lib/jvm/jdk-12.0.2/conf/security
/usr/lib/jvm/jdk-12.0.2/conf/security/policy
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/unlimited
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/unlimited/default_local.policy
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/unlimited/default_US_export.policy
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/README.txt
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/limited
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/limited/exempt_local.policy
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/limited/default_local.policy
/usr/lib/jvm/jdk-12.0.2/conf/security/policy/limited/default_US_export.policy
/usr/lib/jvm/jdk-12.0.2/conf/security/java.policy
/usr/lib/jvm/jdk-12.0.2/conf/security/java.security
/usr/lib/jvm/jdk-12.0.2/conf/net.properties
/usr/lib/jvm/jdk-12.0.2/conf/sdp
/usr/lib/jvm/jdk-12.0.2/conf/sdp/sdp.conf.template
/usr/lib/jvm/jdk-12.0.2/conf/logging.properties
/usr/lib/jvm/jdk-12.0.2/conf/sound.properties
/usr/lib/jvm/jdk-12.0.2/conf/management
/usr/lib/jvm/jdk-12.0.2/conf/management/management.properties
/usr/lib/jvm/jdk-12.0.2/conf/management/jmxremote.access
/usr/lib/jvm/jdk-12.0.2/conf/management/jmxremote.password.template
/usr/lib/jvm/jdk-12.0.2/man
/usr/lib/jvm/jdk-12.0.2/man/man1
/usr/lib/jvm/jdk-12.0.2/man/man1/jmod.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jaccessinspector.1
/usr/lib/jvm/jdk-12.0.2/man/man1/rmic.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jhsdb.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jshell.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jconsole.1
/usr/lib/jvm/jdk-12.0.2/man/man1/rmid.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jmap.1
/usr/lib/jvm/jdk-12.0.2/man/man1/ktab.1
/usr/lib/jvm/jdk-12.0.2/man/man1/rmiregistry.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jdeps.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jar.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jdeprscan.1
/usr/lib/jvm/jdk-12.0.2/man/man1/klist.1
/usr/lib/jvm/jdk-12.0.2/man/man1/keytool.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jaccesswalker.1
/usr/lib/jvm/jdk-12.0.2/man/man1/pack200.1
/usr/lib/jvm/jdk-12.0.2/man/man1/javadoc.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jstat.1
/usr/lib/jvm/jdk-12.0.2/man/man1/kinit.1
/usr/lib/jvm/jdk-12.0.2/man/man1/javap.1
/usr/lib/jvm/jdk-12.0.2/man/man1/java.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jstatd.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jstack.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jps.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jlink.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jinfo.1
/usr/lib/jvm/jdk-12.0.2/man/man1/serialver.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jrunscript.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jarsigner.1
/usr/lib/jvm/jdk-12.0.2/man/man1/unpack200.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jcmd.1
/usr/lib/jvm/jdk-12.0.2/man/man1/javac.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jdb.1
/usr/lib/jvm/jdk-12.0.2/man/man1/jjs.1
/usr/lib/jvm/jdk-12.0.2/legal
/usr/lib/jvm/jdk-12.0.2/legal/jdk.unsupported.desktop
/usr/lib/jvm/jdk-12.0.2/legal/java.logging
/usr/lib/jvm/jdk-12.0.2/legal/java.naming
/usr/lib/jvm/jdk-12.0.2/legal/jdk.httpserver
/usr/lib/jvm/jdk-12.0.2/legal/jdk.accessibility
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdeps
/usr/lib/jvm/jdk-12.0.2/legal/jdk.localedata
/usr/lib/jvm/jdk-12.0.2/legal/jdk.localedata/thaidict.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.compiler
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jlink
/usr/lib/jvm/jdk-12.0.2/legal/java.net.http
/usr/lib/jvm/jdk-12.0.2/legal/java.datatransfer
/usr/lib/jvm/jdk-12.0.2/legal/java.security.sasl
/usr/lib/jvm/jdk-12.0.2/legal/java.scripting
/usr/lib/jvm/jdk-12.0.2/legal/java.sql
/usr/lib/jvm/jdk-12.0.2/legal/jdk.sctp
/usr/lib/jvm/jdk-12.0.2/legal/java.management
/usr/lib/jvm/jdk-12.0.2/legal/jdk.hotspot.agent
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.ec
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.ec/ecc.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jsobject
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.opt
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.opt/jopt-simple.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jartool
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.cryptoki
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.cryptoki/pkcs11wrapper.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.cryptoki/pkcs11cryptotoken.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.editpad
/usr/lib/jvm/jdk-12.0.2/legal/jdk.naming.dns
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.compiler.management
/usr/lib/jvm/jdk-12.0.2/legal/java.prefs
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.ci
/usr/lib/jvm/jdk-12.0.2/legal/java.xml.crypto
/usr/lib/jvm/jdk-12.0.2/legal/java.xml.crypto/santuario.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.zipfs
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jcmd
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/giflib.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/harfbuzz.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/colorimaging.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/jpeg.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/xwd.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/mesa3d.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/opengl.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/libpng.md
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/lcms.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jfr
/usr/lib/jvm/jdk-12.0.2/legal/jdk.attach
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn/double-conversion.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn/joni.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.xml.dom
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jstatd
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management.agent
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jconsole
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdwp.agent
/usr/lib/jvm/jdk-12.0.2/legal/jdk.net
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jshell
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management
/usr/lib/jvm/jdk-12.0.2/legal/jdk.security.jgss
/usr/lib/jvm/jdk-12.0.2/legal/java.smartcardio
/usr/lib/jvm/jdk-12.0.2/legal/java.smartcardio/pcsclite.md
/usr/lib/jvm/jdk-12.0.2/legal/java.sql.rowset
/usr/lib/jvm/jdk-12.0.2/legal/java.base
/usr/lib/jvm/jdk-12.0.2/legal/java.base/cldr.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/aes.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/unicode.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.base/icu.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/c-libutl.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/asm.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/public_suffix.md
/usr/lib/jvm/jdk-12.0.2/legal/java.base/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.xml
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/bcel.md
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/xalan.md
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/jcup.md
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/xerces.md
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/dom.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management.jfr
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdi
/usr/lib/jvm/jdk-12.0.2/legal/java.transaction.xa
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/jquery-migrate.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/pako.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/jquery.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/jqueryUI.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/jszip.md
/usr/lib/jvm/jdk-12.0.2/legal/java.security.jgss
/usr/lib/jvm/jdk-12.0.2/legal/java.se
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.le
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.le/jline.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.dynalink
/usr/lib/jvm/jdk-12.0.2/legal/jdk.dynalink/dynalink.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.naming.rmi
/usr/lib/jvm/jdk-12.0.2/legal/java.compiler
/usr/lib/jvm/jdk-12.0.2/legal/jdk.unsupported
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.jvmstat
/usr/lib/jvm/jdk-12.0.2/legal/jdk.pack
/usr/lib/jvm/jdk-12.0.2/legal/jdk.compiler
/usr/lib/jvm/jdk-12.0.2/legal/java.management.rmi
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn.shell
/usr/lib/jvm/jdk-12.0.2/legal/jdk.aot
/usr/lib/jvm/jdk-12.0.2/legal/java.instrument
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.ed
/usr/lib/jvm/jdk-12.0.2/legal/jdk.security.auth
/usr/lib/jvm/jdk-12.0.2/legal/jdk.rmic
/usr/lib/jvm/jdk-12.0.2/legal/jdk.charsets
/usr/lib/jvm/jdk-12.0.2/legal/java.rmi
/usr/lib/jvm/jdk-12.0.2/include
/usr/lib/jvm/jdk-12.0.2/include/classfile_constants.h
/usr/lib/jvm/jdk-12.0.2/include/jvmticmlr.h
/usr/lib/jvm/jdk-12.0.2/include/linux
/usr/lib/jvm/jdk-12.0.2/include/linux/jni_md.h
/usr/lib/jvm/jdk-12.0.2/include/linux/jawt_md.h
/usr/lib/jvm/jdk-12.0.2/include/jawt.h
/usr/lib/jvm/jdk-12.0.2/include/jdwpTransport.h
/usr/lib/jvm/jdk-12.0.2/include/jni.h
/usr/lib/jvm/jdk-12.0.2/include/jvmti.h
/usr/lib/jvm/jdk-12.0.2/lib
/usr/lib/jvm/jdk-12.0.2/lib/security
/usr/lib/jvm/jdk-12.0.2/lib/security/blacklisted.certs
/usr/lib/jvm/jdk-12.0.2/lib/security/cacerts
/usr/lib/jvm/jdk-12.0.2/lib/security/public_suffix_list.dat
/usr/lib/jvm/jdk-12.0.2/lib/security/default.policy
/usr/lib/jvm/jdk-12.0.2/lib/jvm.cfg
/usr/lib/jvm/jdk-12.0.2/lib/libinstrument.so
/usr/lib/jvm/jdk-12.0.2/lib/libprefs.so
/usr/lib/jvm/jdk-12.0.2/lib/jspawnhelper
/usr/lib/jvm/jdk-12.0.2/lib/libjli.so
/usr/lib/jvm/jdk-12.0.2/lib/libj2pcsc.so
/usr/lib/jvm/jdk-12.0.2/lib/jfr
/usr/lib/jvm/jdk-12.0.2/lib/jfr/profile.jfc
/usr/lib/jvm/jdk-12.0.2/lib/jfr/default.jfc
/usr/lib/jvm/jdk-12.0.2/lib/libnet.so
/usr/lib/jvm/jdk-12.0.2/lib/libnio.so
/usr/lib/jvm/jdk-12.0.2/lib/libextnet.so
/usr/lib/jvm/jdk-12.0.2/lib/librmi.so
/usr/lib/jvm/jdk-12.0.2/lib/src.zip
/usr/lib/jvm/jdk-12.0.2/lib/psfontj2d.properties
/usr/lib/jvm/jdk-12.0.2/lib/libj2gss.so
/usr/lib/jvm/jdk-12.0.2/lib/libsunec.so
/usr/lib/jvm/jdk-12.0.2/lib/libmanagement_agent.so
/usr/lib/jvm/jdk-12.0.2/lib/libjawt.so
/usr/lib/jvm/jdk-12.0.2/lib/ct.sym
/usr/lib/jvm/jdk-12.0.2/lib/libawt_headless.so
/usr/lib/jvm/jdk-12.0.2/lib/tzdb.dat
/usr/lib/jvm/jdk-12.0.2/lib/libjsig.so
/usr/lib/jvm/jdk-12.0.2/lib/libjdwp.so
/usr/lib/jvm/jdk-12.0.2/lib/jrt-fs.jar
/usr/lib/jvm/jdk-12.0.2/lib/libsaproc.so
/usr/lib/jvm/jdk-12.0.2/lib/libzip.so
/usr/lib/jvm/jdk-12.0.2/lib/libjaas.so
/usr/lib/jvm/jdk-12.0.2/lib/psfont.properties.ja
/usr/lib/jvm/jdk-12.0.2/lib/libfontmanager.so
/usr/lib/jvm/jdk-12.0.2/lib/libsctp.so
/usr/lib/jvm/jdk-12.0.2/lib/libjimage.so
/usr/lib/jvm/jdk-12.0.2/lib/libmanagement_ext.so
/usr/lib/jvm/jdk-12.0.2/lib/liblcms.so
/usr/lib/jvm/jdk-12.0.2/lib/modules
/usr/lib/jvm/jdk-12.0.2/lib/libsplashscreen.so
/usr/lib/jvm/jdk-12.0.2/lib/libawt.so
/usr/lib/jvm/jdk-12.0.2/lib/classlist
/usr/lib/jvm/jdk-12.0.2/lib/libverify.so
/usr/lib/jvm/jdk-12.0.2/lib/libjavajpeg.so
/usr/lib/jvm/jdk-12.0.2/lib/libmlib_image.so
/usr/lib/jvm/jdk-12.0.2/lib/libjava.so
/usr/lib/jvm/jdk-12.0.2/lib/libattach.so
/usr/lib/jvm/jdk-12.0.2/lib/libawt_xawt.so
/usr/lib/jvm/jdk-12.0.2/lib/libdt_socket.so
/usr/lib/jvm/jdk-12.0.2/lib/server
/usr/lib/jvm/jdk-12.0.2/lib/server/libjsig.so
/usr/lib/jvm/jdk-12.0.2/lib/server/classes.jsa
/usr/lib/jvm/jdk-12.0.2/lib/server/libjvm.so
/usr/lib/jvm/jdk-12.0.2/lib/server/Xusage.txt
/usr/lib/jvm/jdk-12.0.2/lib/libjsound.so
/usr/lib/jvm/jdk-12.0.2/lib/libj2pkcs11.so
/usr/lib/jvm/jdk-12.0.2/lib/jexec
/usr/lib/jvm/jdk-12.0.2/lib/libunpack.so
/usr/lib/jvm/jdk-12.0.2/lib/libmanagement.so
/usr/share
/usr/share/doc
/usr/share/doc/jdk-12.0.2
/usr/share/doc/jdk-12.0.2/copyright
/usr/lib/jvm/jdk-12.0.2/legal/jdk.unsupported.desktop/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.unsupported.desktop/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.logging/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.logging/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.naming/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.naming/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.httpserver/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.httpserver/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.accessibility/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.accessibility/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdeps/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdeps/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.localedata/cldr.md
/usr/lib/jvm/jdk-12.0.2/legal/jdk.localedata/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.localedata/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.compiler/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.compiler/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jlink/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jlink/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.net.http/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.net.http/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.datatransfer/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.datatransfer/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.security.sasl/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.security.sasl/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.scripting/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.scripting/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.sql/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.sql/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.sctp/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.sctp/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.management/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.management/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.hotspot.agent/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.hotspot.agent/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.ec/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.ec/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jsobject/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jsobject/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.opt/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.opt/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jartool/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jartool/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.cryptoki/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.crypto.cryptoki/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.editpad/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.editpad/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.naming.dns/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.naming.dns/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.compiler.management/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.compiler.management/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.prefs/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.prefs/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.ci/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.vm.ci/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.xml.crypto/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.xml.crypto/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.zipfs/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.zipfs/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jcmd/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jcmd/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.desktop/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jfr/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jfr/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.attach/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.attach/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.xml.dom/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.xml.dom/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jstatd/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jstatd/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management.agent/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management.agent/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jconsole/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jconsole/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdwp.agent/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdwp.agent/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.net/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.net/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jshell/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jshell/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.security.jgss/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.security.jgss/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.smartcardio/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.smartcardio/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.sql.rowset/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.sql.rowset/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.xml/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management.jfr/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.management.jfr/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdi/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.jdi/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.transaction.xa/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.transaction.xa/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.javadoc/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.security.jgss/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.security.jgss/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.se/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.se/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.le/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.le/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.dynalink/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.dynalink/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.naming.rmi/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.naming.rmi/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.compiler/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.compiler/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.unsupported/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.unsupported/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.jvmstat/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.jvmstat/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.pack/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.pack/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.compiler/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.compiler/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.management.rmi/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.management.rmi/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn.shell/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.scripting.nashorn.shell/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.aot/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.aot/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.instrument/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.instrument/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.ed/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.internal.ed/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.security.auth/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.security.auth/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.rmic/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.rmic/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/jdk.charsets/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/jdk.charsets/COPYRIGHT
/usr/lib/jvm/jdk-12.0.2/legal/java.rmi/LICENSE
/usr/lib/jvm/jdk-12.0.2/legal/java.rmi/COPYRIGHT
```

从输出信息可以看出，安装完成后，除了一个copyright文件放在了/usr/share/doc/jdk-12.0.2下，其余文件都在/usr/lib/jvm/jdk-12.0.2下，也就是说这个目录就是我的JAVA_HOME了。

找到了文件安装位置之后，当然是要设置环境变量了：

博文<https://blog.csdn.net/pxmxx/article/details/80106239>及其他很多安装JDK的博文中都指出，在设置环境变量时需要设置JRE_HOME（后面在[10.5 安装Java8](#105-%e5%ae%89%e8%a3%85java%e5%bc%80%e5%8f%91%e7%8e%af%e5%a2%83java-8)中看到安装jdk8后确实目录中含有jre子目录），奇葩的是，JDK安装目录中根本没有jre，于是查阅官方安装说明 <https://docs.oracle.com/en/java/javase/12/install/installation-jdk-linux-platforms.html>，官方安装文档中则更奇葩，只提及了.tar.gz和.rpm安装，且安装后自动设置好了环境变量，擦。

不管了，反正在/usr/lib/jvm/jdk-12.0.2/bin下找到了一堆java在内的可执行文件，说明没找错位置，自行设置环境变量了，在/etc/profile末尾追加：

```shell
export JAVA_HOME=/usr/lib/jvm/jdk-12.0.2
export CLASSPATH=.:${JAVA_HOME}/lib
export PATH=$PATH:${JAVA_HOME}/bin
```

执行`source /etc/profile`是变量生效后，进行测试：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:/tmp/mozilla_eiger0$ source /etc/profile
eiger@eiger-ThinkPad-X1-Carbon-3rd:/tmp/mozilla_eiger0$ java --version
java 12.0.2 2019-07-16
Java(TM) SE Runtime Environment (build 12.0.2+10)
Java HotSpot(TM) 64-Bit Server VM (build 12.0.2+10, mixed mode, sharing)
```

至此，Java开发环境配置完成。

### 10.5. 安装Java开发环境（Java 8）

由于Java SE Development Kit 8u221 （Java8里边的最高版本）没有提供.deb包，因此只能再开一小节讲。tar.gz压缩包方式的安装及多版本Java同时存在情况下的处理。

JDK 8u221下载链接：  <https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>

记得选择linux-x64版。

下载解压后，放置到/usr/local目录下（ps: 顺便下载了jdk8的demos&samples解压后放到jdk8目录下，这和我们的安装进程无关）

设置环境变量如下：

```shell
# jdk12
#export JAVA_HOME=/usr/lib/jvm/jdk-12.0.2
#export CLASSPATH=.:${JAVA_HOME}/lib
#export PATH=$PATH:${JAVA_HOME}/bin
# jdk8 # 与jdk12不可同时启用
export JAVA_HOME=/usr/local/jdk1.8.0_221
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$PATH:${JAVA_HOME}/bin
```

执行`source /etc/profile`并重启系统后（不重启始终无法生效）进行测试：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
```

(ps: java8的参数都是单横线开头)

至此，java8安装成功。

## 11. 其他一些问题

### 11.1. sudo+xxx:找不到xxx命令

问题描述： xxx命令在当前用户下用得好好的，一切换到sudo下就提示找不到该命令。比如说在本次mainflux安装过程中遇到的编译所有微服务和测试cassandra时的问题。

在[4.2 编译所有微服务](#42-%e7%bc%96%e8%af%91%e6%89%80%e6%9c%89%e6%9c%8d%e5%8a%a1)末尾遇到了`sudo make`报错找不到go命令，当时通过输入
`sudo chown -R $USER:$(id -gn $USER) /home/eiger/.config`来提升当前用户权限的方法解决当前用户权限不足而sudo(root)用户找不到go的尴尬。

现在解决`sudo xxx:找不到xxx命令`的问题：

出现这个问题的原因是，所要使用的应用程序是注册在`$PATH`变量下的，当使用sudo时会自动重置`$PATH`变量为初始状态（也就是我没有往里边添加变量、只有系统最开始的一些PATH值的状态），这样的话，当然`sudo xxx`会找不到命令。我们要做的就是修改相关设置，取消sudo自动重置`$PATH`：

```shell
sudo nano /etc/sudoers
# 将 Defaults  env_reset 修改为 Defaults  !env_reset
sudo nano /etc/profile
# 文件末追加 alias sudo='sudo env PATH=$PATH'
```

执行`source /etc/profile`后生效。

## 12. Getting started

在官网文档<https://mainflux.readthedocs.io/en/latest/getting-started/>介绍了如何开始使用mainflux。这里按照原文档顺序对其作翻译、补充及实践记录。

### 12.1 运行整个系统

首先，电脑上需安装好docker与docker-compose，并将源码拉取到本地，在mainflux根目录下输入`make run`，即可启动mainflux的docker容器组合，并且将在终端看到输出信息。

我们去看下mainflux/Makefile内`run`定义：

```Makefile
run:
  docker-compose -f docker/docker-compose.yml up
```

这告诉我们`make run`其实是`docker-compose -f docker/docker-compose.yml up`，根据mainflux/docker/docker-compose.yml文件强制拉起（运行）其内描述的所有容器，那么我们来看下docker-compose.yml内容：

```yml
###
# Copyright (c) 2015-2017 Mainflux
#
# Mainflux is licensed under an Apache license, version 2.0 license.
# All rights not explicitly granted in the Apache license, version 2.0 are reserved.
# See the included LICENSE file for more details.
###

version: "3"

networks:
  mainflux-base-net:
    driver: bridge

volumes:
  mainflux-users-db-volume:
  mainflux-things-db-volume:
  mainflux-mqtt-redis-volume:
  mainflux-things-redis-volume:
  mainflux-es-redis-volume:

services:
  nginx:
    image: nginx:1.16.0-alpine
    container_name: mainflux-nginx
    restart: on-failure
    volumes:
      - ./nginx/nginx-${AUTH-key}.conf:/etc/nginx/nginx.conf.template
      - ./nginx/entrypoint.sh:/entrypoint.sh
      - ./ssl/authorization.js:/etc/nginx/authorization.js
      - ./ssl/certs/mainflux-server.crt:/etc/ssl/certs/mainflux-server.crt
      - ./ssl/certs/ca.crt:/etc/ssl/certs/ca.crt
      - ./ssl/certs/mainflux-server.key:/etc/ssl/private/mainflux-server.key
      - ./ssl/dhparam.pem:/etc/ssl/certs/dhparam.pem
    ports:
      - ${MF_NGINX_HTTP_PORT}:${MF_NGINX_HTTP_PORT}
      - ${MF_NGINX_SSL_PORT}:${MF_NGINX_SSL_PORT}
      - ${MF_NGINX_MQTT_PORT}:${MF_NGINX_MQTT_PORT}
    networks:
      - mainflux-base-net
    environment:
      MF_UI_PORT: ${MF_UI_PORT}
    command: /entrypoint.sh

  nats:
    image: nats:1.3.0
    container_name: mainflux-nats
    restart: on-failure
    networks:
      - mainflux-base-net

  users-db:
    image: postgres:10.8-alpine
    container_name: mainflux-users-db
    restart: on-failure
    environment:
      POSTGRES_USER: ${MF_USERS_DB_USER}
      POSTGRES_PASSWORD: ${MF_USERS_DB_PASS}
      POSTGRES_DB: ${MF_USERS_DB}
    networks:
      - mainflux-base-net
    volumes:
      - mainflux-users-db-volume:/var/lib/postgresql/data

  users:
    image: mainflux/users:latest
    container_name: mainflux-users
    depends_on:
      - users-db
    expose:
      - ${MF_USERS_GRPC_PORT}
    restart: on-failure
    environment:
      MF_USERS_LOG_LEVEL: ${MF_USERS_LOG_LEVEL}
      MF_USERS_DB_HOST: users-db
      MF_USERS_DB_PORT: ${MF_USERS_DB_PORT}
      MF_USERS_DB_USER: ${MF_USERS_DB_USER}
      MF_USERS_DB_PASS: ${MF_USERS_DB_PASS}
      MF_USERS_DB: ${MF_USERS_DB}
      MF_USERS_HTTP_PORT: ${MF_USERS_HTTP_PORT}
      MF_USERS_GRPC_PORT: ${MF_USERS_GRPC_PORT}
      MF_USERS_SECRET: ${MF_USERS_SECRET}
      MF_JAEGER_URL: ${MF_JAEGER_URL}
    ports:
      - ${MF_USERS_HTTP_PORT}:${MF_USERS_HTTP_PORT}
    networks:
      - mainflux-base-net

  things-db:
    image: postgres:10.8-alpine
    container_name: mainflux-things-db
    restart: on-failure
    environment:
      POSTGRES_USER: ${MF_THINGS_DB_USER}
      POSTGRES_PASSWORD: ${MF_THINGS_DB_PASS}
      POSTGRES_DB: ${MF_THINGS_DB}
    networks:
      - mainflux-base-net
    volumes:
      - mainflux-things-db-volume:/var/lib/postgresql/data

  things-redis:
    image: redis:5.0-alpine
    container_name: mainflux-things-redis
    restart: on-failure
    networks:
      - mainflux-base-net
    volumes:
      - mainflux-things-redis-volume:/data

  things:
    image: mainflux/things:latest
    container_name: mainflux-things
    depends_on:
      - things-db
      - users
    restart: on-failure
    environment:
      MF_THINGS_LOG_LEVEL: ${MF_THINGS_LOG_LEVEL}
      MF_THINGS_DB_HOST: things-db
      MF_THINGS_DB_PORT: ${MF_THINGS_DB_PORT}
      MF_THINGS_DB_USER: ${MF_THINGS_DB_USER}
      MF_THINGS_DB_PASS: ${MF_THINGS_DB_PASS}
      MF_THINGS_DB: ${MF_THINGS_DB}
      MF_THINGS_CACHE_URL: things-redis:${MF_REDIS_TCP_PORT}
      MF_THINGS_ES_URL: es-redis:${MF_REDIS_TCP_PORT}
      MF_THINGS_HTTP_PORT: ${MF_THINGS_HTTP_PORT}
      MF_THINGS_AUTH_HTTP_PORT: ${MF_THINGS_AUTH_HTTP_PORT}
      MF_THINGS_AUTH_GRPC_PORT: ${MF_THINGS_AUTH_GRPC_PORT}
      MF_USERS_URL: users:${MF_USERS_GRPC_PORT}
      MF_THINGS_SECRET: ${MF_THINGS_SECRET}
      MF_JAEGER_URL: ${MF_JAEGER_URL}
    ports:
      - ${MF_THINGS_HTTP_PORT}:${MF_THINGS_HTTP_PORT}
      - ${MF_THINGS_AUTH_HTTP_PORT}:${MF_THINGS_AUTH_HTTP_PORT}
      - ${MF_THINGS_AUTH_GRPC_PORT}:${MF_THINGS_AUTH_GRPC_PORT}
    networks:
      - mainflux-base-net

  jaeger:
    image: jaegertracing/all-in-one:1.13
    container_name: mainflux-jaeger
    ports:
      - ${MF_JAEGER_PORT}:${MF_JAEGER_PORT}/udp
      - ${MF_JAEGER_FRONTEND}:${MF_JAEGER_FRONTEND}
      - ${MF_JAEGER_COLLECTOR}:${MF_JAEGER_COLLECTOR}
      - ${MF_JAEGER_CONFIGS}:${MF_JAEGER_CONFIGS}
    networks:
      - mainflux-base-net

  normalizer:
    image: mainflux/normalizer:latest
    container_name: mainflux-normalizer
    restart: on-failure
    depends_on:
      - nats
    environment:
      MF_NORMALIZER_LOG_LEVEL: ${MF_NORMALIZER_LOG_LEVEL}
      MF_NATS_URL: ${MF_NATS_URL}
      MF_NORMALIZER_PORT: ${MF_NORMALIZER_PORT}
    ports:
      - ${MF_NORMALIZER_PORT}:${MF_NORMALIZER_PORT}
    networks:
      - mainflux-base-net

  ws-adapter:
    image: mainflux/ws:latest
    container_name: mainflux-ws
    depends_on:
      - things
      - nats
    restart: on-failure
    environment:
      MF_WS_ADAPTER_LOG_LEVEL: ${MF_WS_ADAPTER_LOG_LEVEL}
      MF_WS_ADAPTER_PORT: ${MF_WS_ADAPTER_PORT}
      MF_NATS_URL: ${MF_NATS_URL}
      MF_THINGS_URL: things:${MF_THINGS_AUTH_GRPC_PORT}
      MF_JAEGER_URL: ${MF_JAEGER_URL}
    ports:
      - ${MF_WS_ADAPTER_PORT}:${MF_WS_ADAPTER_PORT}
    networks:
      - mainflux-base-net

  http-adapter:
    image: mainflux/http:latest
    container_name: mainflux-http
    depends_on:
      - things
      - nats
    restart: on-failure
    environment:
      MF_HTTP_ADAPTER_LOG_LEVEL: debug
      MF_HTTP_ADAPTER_PORT: ${MF_HTTP_ADAPTER_PORT}
      MF_NATS_URL: ${MF_NATS_URL}
      MF_THINGS_URL: things:${MF_THINGS_AUTH_GRPC_PORT}
      MF_JAEGER_URL: ${MF_JAEGER_URL}
    ports:
      - ${MF_HTTP_ADAPTER_PORT}:${MF_HTTP_ADAPTER_PORT}
    networks:
      - mainflux-base-net

  es-redis:
    image: redis:5.0-alpine
    container_name: mainflux-es-redis
    restart: on-failure
    networks:
      - mainflux-base-net
    volumes:
      - mainflux-es-redis-volume:/data

  mqtt-redis:
    image: redis:5.0-alpine
    container_name: mainflux-mqtt-redis
    restart: on-failure
    networks:
      - mainflux-base-net
    volumes:
      - mainflux-mqtt-redis-volume:/data

  mqtt-adapter:
    image: mainflux/mqtt:latest
    container_name: mainflux-mqtt
    depends_on:
      - things
      - nats
      - mqtt-redis
    restart: on-failure
    environment:
      MF_MQTT_ADAPTER_LOG_LEVEL: ${MF_MQTT_ADAPTER_LOG_LEVEL}
      MF_MQTT_INSTANCE_ID: mqtt-adapter-1
      MF_MQTT_ADAPTER_PORT: ${MF_MQTT_ADAPTER_PORT}
      MF_MQTT_ADAPTER_WS_PORT: ${MF_MQTT_ADAPTER_WS_PORT}
      MF_MQTT_ADAPTER_REDIS_HOST: mqtt-redis
      MF_MQTT_ADAPTER_ES_HOST: es-redis
      MF_NATS_URL: ${MF_NATS_URL}
      MF_THINGS_URL: things:${MF_THINGS_AUTH_GRPC_PORT}
      MF_JAEGER_URL: ${MF_JAEGER_URL}
    ports:
      - ${MF_MQTT_ADAPTER_PORT}:${MF_MQTT_ADAPTER_PORT}
      - ${MF_MQTT_ADAPTER_WS_PORT}:${MF_MQTT_ADAPTER_WS_PORT}
    networks:
      - mainflux-base-net

  coap-adapter:
    image: mainflux/coap:latest
    container_name: mainflux-coap
    depends_on:
      - things
      - nats
    restart: on-failure
    environment:
      MF_COAP_ADAPTER_LOG_LEVEL: ${MF_COAP_ADAPTER_LOG_LEVEL}
      MF_COAP_ADAPTER_PORT: ${MF_COAP_ADAPTER_PORT}
      MF_NATS_URL: ${MF_NATS_URL}
      MF_THINGS_URL: things:${MF_THINGS_AUTH_GRPC_PORT}
      MF_JAEGER_URL: ${MF_JAEGER_URL}
    ports:
      - ${MF_COAP_ADAPTER_PORT}:${MF_COAP_ADAPTER_PORT}/udp
    networks:
      - mainflux-base-net

  ui:
    image: mainflux/ui:latest
    container_name: mainflux-ui
    restart: on-failure
    ports:
      - ${MF_UI_PORT}:${MF_UI_PORT}
    networks:
      - mainflux-base-net
    environment:
      MF_UI_PORT: ${MF_UI_PORT}
```

当docker-compose在本地容器中无法找到文件中描述的容器时，会从docker镜像网站去拉取mainflux上传的相应镜像并容器化运行。该文件还描述了各个容器与本机真实物理环境之间的映射关系，之后在使用的过程中应多借助该文档。

好了，我们现在来实际操作一下。

首先，因为之前进行了，docker镜像的手动构建（完成）以及运行测试（部分容器得到启动，并逐次解决了相应的问题），我们需要列举下当前本机的容器及镜像：

```shell
# 查看本机docker、docker-compose版本
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker --version
Docker version 19.03.1, build 74b1e89
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker-compose --version
docker-compose version 1.24.1, build 4667896b
# 当前容器列表，使用docker ps也是一样的。
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker container ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
# 列举本机所有docker镜像
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
mainflux/mqtt-amd64               latest              63c2335da152        4 days ago          177MB
mainflux/ui-amd64                 latest              56818963b563        4 days ago          16.4MB
<none>                            <none>              776802a00b76        4 days ago          119MB
mainflux/bootstrap-amd64          latest              2d6bf08877ff        4 days ago          11.5MB
mainflux/cli-amd64                latest              9a95ca2d5e58        4 days ago          7.39MB
mainflux/postgres-reader-amd64    latest              0da4b651447e        4 days ago          10.4MB
mainflux/postgres-writer-amd64    latest              ca8dc5c8357c        4 days ago          10.2MB
mainflux/cassandra-reader-amd64   latest              a9abfb5cc14f        4 days ago          10.4MB
mainflux/cassandra-writer-amd64   latest              b9017e5c7bf7        4 days ago          10.2MB
mainflux/mongodb-reader-amd64     latest              83312eb43d4e        4 days ago          13.2MB
mainflux/mongodb-writer-amd64     latest              c2c20ded7746        4 days ago          13MB
mainflux/influxdb-reader-amd64    latest              602cca6b16df        4 days ago          9.64MB
mainflux/influxdb-writer-amd64    latest              8291fcbf79c7        4 days ago          9.57MB
mainflux/lora-amd64               latest              6ad78c136202        4 days ago          10.5MB
mainflux/coap-amd64               latest              436fb919b3ef        4 days ago          10.5MB
mainflux/ws-amd64                 latest              ea32ef946286        4 days ago          10.6MB
mainflux/normalizer-amd64         latest              f457472beb17        4 days ago          12.1MB
mainflux/http-amd64               latest              e9c5e8cca6d2        4 days ago          10.5MB
mainflux/things-amd64             latest              6b91b8561a30        4 days ago          11.7MB
mainflux/users-amd64              latest              64a299ad8ed2        4 days ago          10.6MB
nginx                             1.14.2-alpine       8a2fb25a19f5        4 months ago        16MB
golang                            1.10-alpine         7b53e4a31d21        6 months ago        259MB
node                              10.15.1-alpine      fe6ff768f798        6 months ago        70.7MB
hello-world                       latest              fce289e99eb9        7 months ago        1.84kB
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$
```

由于执行`make run`其实是从docker官方镜像源下载（本机所编译的镜像带有`-amd64`后缀，与doker-compose.yml描述不同，所以还是会远程拉取）

所以先将docker设置国内的官方镜像源：

编辑`/etc/default/docker`并在末尾追加：

```shell
DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"
```

保存后重启docker即可：

```shell
sudo service docker restart
```

接下来在mainflux根目录下输入`make run`，会将mainflux系统所有微服务自云端拉取到本地运行。

注意，在拉取的过程中可能经常显示拉取失败（TLS握手超时），不要紧，继续输`make run`直到命令行显示所有服务已拉取完成并正要启动。

启动的过程中，在我的电脑上遇到nginx无法启动，提示80端口被占用的情况：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make run
docker-compose -f docker/docker-compose.yml up
mainflux-ui is up-to-date
mainflux-things-redis is up-to-date
mainflux-mqtt-redis is up-to-date
mainflux-es-redis is up-to-date
mainflux-users-db is up-to-date
mainflux-things-db is up-to-date
Starting mainflux-nginx ...
mainflux-nats is up-to-date
mainflux-jaeger is up-to-date
mainflux-users is up-to-date
mainflux-normalizer is up-to-date
mainflux-things is up-to-date
mainflux-mqtt is up-to-date
mainflux-http is up-to-date
mainflux-coap is up-to-date
Starting mainflux-nginx ... error

ERROR: for mainflux-nginx  Cannot start service nginx: driver failed programming external connectivity on endpoint mainflux-nginx (2a91eb3c88807046073423c3a6baf44da0ad26dc65bcb57953b4c71fb094a154): Error starting userland proxy: listen tcp 0.0.0.0:80: bind: address already in use

ERROR: for nginx  Cannot start service nginx: driver failed programming external connectivity on endpoint mainflux-nginx (2a91eb3c88807046073423c3a6baf44da0ad26dc65bcb57953b4c71fb094a154): Error starting userland proxy: listen tcp 0.0.0.0:80: bind: address already in use
ERROR: Encountered errors while bringing up the project.
Makefile:151: recipe for target 'run' failed
make: *** [run] Error 1
```

检查下当前80端口被什么程序占用了：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ sudo netstat -lnp | grep 80
[sudo] password for eiger:
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1638/nginx: master
tcp6       0      0 :::8880                 :::*                    LISTEN      8432/docker-proxy
tcp6       0      0 :::80                   :::*                    LISTEN      1638/nginx: master
tcp6       0      0 :::8180                 :::*                    LISTEN      7979/docker-proxy
unix  2      [ ACC ]     STREAM     LISTENING     30380    1789/systemd         /run/user/1000/gnupg/S.gpg-agent.extra
unix  2      [ ACC ]     STREAM     LISTENING     256023   3607/code            /run/user/1000/vscode-git-askpass-3381c5ecc00801d2295b4bc73b69a9f6a2b09eb2.sock
unix  2      [ ACC ]     STREAM     LISTENING     280798   7989/containerd-shi  @/containerd-shim/moby/fea1599f1fafb50ec78e979f461f77bb99350205c33c622d1841938e586582ab/shim.sock@
unix  2      [ ACC ]     STREAM     LISTENING     275172   6812/containerd-shi  @/containerd-shim/moby/aef24619a59af74650841b80deca2addcf0f325659ff88b46e7a415308615aee/shim.sock@
unix  2      [ ACC ]     STREAM     LISTENING     28280    1333/gdm3            @/tmp/dbus-Iy5HJvQN
unix  2      [ ACC ]     STREAM     LISTENING     37218    2819/dockerd         /var/run/docker/libnetwork/9fc55081e3a86db3f1611707734cd1856801fe3891d2848d5e21010237bcbfd1.sock
```

什么情况？？？明明是被nginx使用的呀

我们再把当前启动的容器列举一下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                                                                                                                               NAMES
4bb5cd9596ea        mainflux/ws:latest              "/exe"                   16 hours ago        Up 2 minutes        0.0.0.0:8186->8186/tcp                                                                                                              mainflux-ws
8c0c4ccbbd55        mainflux/coap:latest            "/exe"                   16 hours ago        Up 2 minutes        0.0.0.0:5683->5683/udp                                                                                                              mainflux-coap
941c67d26e08        mainflux/mqtt:latest            "node mqtt.js"           16 hours ago        Up 2 minutes        0.0.0.0:1883->1883/tcp, 0.0.0.0:8880->8880/tcp                                                                                      mainflux-mqtt
51206daffd4f        mainflux/http:latest            "/exe"                   16 hours ago        Up 2 minutes        0.0.0.0:8185->8185/tcp                                                                                                              mainflux-http
eb9bc8ac2e0a        mainflux/normalizer:latest      "/exe"                   16 hours ago        Up 2 minutes        0.0.0.0:8184->8184/tcp                                                                                                              mainflux-normalizer
d1579ed65dff        mainflux/things:latest          "/exe"                   16 hours ago        Up 2 minutes        0.0.0.0:8182-8183->8182-8183/tcp, 0.0.0.0:8989->8989/tcp                                                                            mainflux-things
fea1599f1faf        mainflux/users:latest           "/exe"                   16 hours ago        Up 2 minutes        0.0.0.0:8180->8180/tcp, 8181/tcp                                                                                                    mainflux-users
d20f696b6a37        nats:1.3.0                      "/gnatsd -c gnatsd.c…"   16 hours ago        Up 2 minutes        4222/tcp, 6222/tcp, 8222/tcp                                                                                                        mainflux-nats
08b0d3a188f9        postgres:10.8-alpine            "docker-entrypoint.s…"   16 hours ago        Up 2 minutes        5432/tcp                                                                                                                            mainflux-users-db
310c048cb4f3        jaegertracing/all-in-one:1.13   "/go/bin/all-in-one-…"   16 hours ago        Up 2 minutes        5775/udp, 0.0.0.0:5778->5778/tcp, 0.0.0.0:14268->14268/tcp, 6832/udp, 0.0.0.0:16686->16686/tcp, 0.0.0.0:6831->6831/udp, 14250/tcp   mainflux-jaeger
d84ae3be3425        redis:5.0-alpine                "docker-entrypoint.s…"   16 hours ago        Up 2 minutes        6379/tcp                                                                                                                            mainflux-es-redis
aef24619a59a        redis:5.0-alpine                "docker-entrypoint.s…"   16 hours ago        Up 2 minutes        6379/tcp                                                                                                                            mainflux-things-redis
3afec1d3a3e0        postgres:10.8-alpine            "docker-entrypoint.s…"   16 hours ago        Up 2 minutes        5432/tcp                                                                                                                            mainflux-things-db
6f05dbae388f        redis:5.0-alpine                "docker-entrypoint.s…"   16 hours ago        Up 2 minutes        6379/tcp                                                                                                                            mainflux-mqtt-redis
0f9972dc12c5        mainflux/ui:latest              "/entrypoint.sh"         16 hours ago        Up 2 minutes        80/tcp, 0.0.0.0:3000->3000/tcp                                                                                                      mainflux-ui
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$
```

这里我们注意nginx服务未开启成功，但是列表中mainflux-ui使用了80/tcp端口。从mainflux的架构图可以看出：

<!--博客生成时使用/images开头，编辑器中使用/static开头-->
<!--![mainflux-architecture](/images/mainflux-architecture.jpg)-->

![mainflux-architecture](../../../static/images/mainflux-architecture.jpg)

nginx被用来做负载均衡，但是mainflux-ui也用到了nginx，但它只是用nginx做一个简单的服务器，另外我们去查看docker-compose.yml也可以看到services下nginx的environment项是MF_UI_PORT，这进一步佐证了mainflux-ui使用nginx作web服务器。

现在来查看下mainflux所设置的环境变量，位于mainflux/.env文件中，内容展示如下：

```shell
# Docker: Environment variables in Compose

## NGINX
MF_NGINX_HTTP_PORT=80
MF_NGINX_SSL_PORT=443
MF_NGINX_MQTT_PORT=8883

## NATS
MF_NATS_URL=nats://nats:4222

## REDIS
MF_REDIS_TCP_PORT=6379

## UI
MF_UI_PORT=3000

## Grafana
MF_GRAFANA_PORT=3000

## Jaeger
MF_JAEGER_PORT=6831
MF_JAEGER_FRONTEND=16686
MF_JAEGER_COLLECTOR=14268
MF_JAEGER_CONFIGS=5778
MF_JAEGER_URL=jaeger:6831

## Core Services
### Users
MF_USERS_LOG_LEVEL=debug
MF_USERS_HTTP_PORT=8180
MF_USERS_GRPC_PORT=8181
MF_USERS_DB_PORT=5432
MF_USERS_DB_USER=mainflux
MF_USERS_DB_PASS=mainflux
MF_USERS_DB=users
MF_USERS_SECRET=secret

### Things
MF_THINGS_LOG_LEVEL=debug
MF_THINGS_HTTP_PORT=8182
MF_THINGS_AUTH_HTTP_PORT=8989
MF_THINGS_AUTH_GRPC_PORT=8183
MF_THINGS_DB_PORT=5432
MF_THINGS_DB_USER=mainflux
MF_THINGS_DB_PASS=mainflux
MF_THINGS_DB=things
MF_THINGS_SECRET=secret

### Normalizer
MF_NORMALIZER_LOG_LEVEL=debug
MF_NORMALIZER_PORT=8184

### WS
MF_WS_ADAPTER_LOG_LEVEL=debug
MF_WS_ADAPTER_PORT=8186

### HTTP
MF_HTTP_ADAPTER_PORT=8185

### MQTT
MF_MQTT_ADAPTER_LOG_LEVEL=debug
MF_MQTT_ADAPTER_PORT=1883
MF_MQTT_ADAPTER_WS_PORT=8880

### COAP
MF_COAP_ADAPTER_LOG_LEVEL=debug
MF_COAP_ADAPTER_PORT=5683

## Addons Services
### Bootstrap
MF_BOOTSTRAP_LOG_LEVEL=debug
MF_BOOTSTRAP_PORT=8202
MF_BOOTSTRAP_DB_PORT=5432
MF_BOOTSTRAP_DB_USER=mainflux
MF_BOOTSTRAP_DB_PASS=mainflux
MF_BOOTSTRAP_DB=bootstrap
MF_BOOTSTRAP_DB_SSL_MODE=disable

### LoRa
MF_LORA_ADAPTER_LOG_LEVEL=debug
MF_LORA_ADAPTER_MESSAGES_URL=tcp://lora.mqtt.mainflux.io:1883
MF_LORA_ADAPTER_HTTP_PORT=8187

### Cassandra Writer
MF_CASSANDRA_WRITER_LOG_LEVEL=debug
MF_CASSANDRA_WRITER_PORT=8902
MF_CASSANDRA_WRITER_DB_PORT=9042
MF_CASSANDRA_WRITER_DB_CLUSTER=mainflux-cassandra
MF_CASSANDRA_WRITER_DB_KEYSPACE=mainflux

### Cassandra Reader
MF_CASSANDRA_READER_LOG_LEVEL=debug
MF_CASSANDRA_READER_PORT=8903
MF_CASSANDRA_READER_DB_PORT=9042
MF_CASSANDRA_READER_DB_CLUSTER=mainflux-cassandra
MF_CASSANDRA_READER_DB_KEYSPACE=mainflux

### InfluxDB Writer
MF_INFLUX_WRITER_LOG_LEVEL=debug
MF_INFLUX_WRITER_PORT=8900
MF_INFLUX_WRITER_BATCH_SIZE=5000
MF_INFLUX_WRITER_BATCH_TIMEOUT=5
MF_INFLUX_WRITER_DB_PORT=8086
MF_INFLUX_WRITER_DB_NAME=mainflux
MF_INFLUX_WRITER_DB_USER=mainflux
MF_INFLUX_WRITER_DB_PASS=mainflux
MF_INFLUX_WRITER_GRAFANA_PORT=3001

### InfluxDB Reader
MF_INFLUX_READER_LOG_LEVEL=debug
MF_INFLUX_READER_PORT=8905
MF_INFLUX_READER_DB_NAME=mainflux
MF_INFLUX_READER_DB_PORT=8086
MF_INFLUX_READER_DB_USER=mainflux
MF_INFLUX_READER_DB_PASS=mainflux

### MongoDB Writer
MF_MONGO_WRITER_LOG_LEVEL=debug
MF_MONGO_WRITER_PORT=8901
MF_MONGO_WRITER_DB_NAME=mainflux
MF_MONGO_WRITER_DB_PORT=27017

### MongoDB Reader
MF_MONGO_READER_LOG_LEVEL=debug
MF_MONGO_READER_PORT=8904
MF_MONGO_READER_DB_NAME=mainflux
MF_MONGO_READER_DB_PORT=27017

### Postgres Writer
MF_POSTGRES_WRITER_LOG_LEVEL=debug
MF_POSTGRES_WRITER_PORT=9104
MF_POSTGRES_WRITER_DB_PORT=5432
MF_POSTGRES_WRITER_DB_USER=mainflux
MF_POSTGRES_WRITER_DB_PASS=mainflux
MF_POSTGRES_WRITER_DB_NAME=messages
MF_POSTGRES_WRITER_DB_SSL_MODE=disable
MF_POSTGRES_WRITER_DB_SSL_CERT=""
MF_POSTGRES_WRITER_DB_SSL_KEY=""
MF_POSTGRES_WRITER_DB_SSL_ROOT_CERT=""

### Postgres Reader
MF_POSTGRES_READER_LOG_LEVEL=debug
MF_POSTGRES_READER_PORT=9204
MF_POSTGRES_READER_CLIENT_TLS=false
MF_POSTGRES_READER_CA_CERTS=""
MF_POSTGRES_READER_DB_PORT=5432
MF_POSTGRES_READER_DB_USER=mainflux
MF_POSTGRES_READER_DB_PASS=mainflux
MF_POSTGRES_READER_DB_NAME=messages
MF_POSTGRES_READER_DB_SSL_MODE=disable
MF_POSTGRES_READER_DB_SSL_CERT=""
MF_POSTGRES_READER_DB_SSL_KEY=""
MF_POSTGRES_READER_DB_SSL_ROOT_CERT=""
```

可以看出nginx使用了80端口，但是整个mainflux运行时相当于开了两个nginx进程，结果都在抢80端口，这就导致了mainflux-nginx启动失败。

除此之外，还有可能是因为我本机已经安装了nginx服务器（默认端口即是80），这导致真实环境的nginx和容器环境的nginx都想用80端口，从而产生冲突。

（ps: 都是我的推测，没有严格佐证）

现在既然关闭占用80端口的程序不太好办，那么修改nginx端口：

```shell
## NGINX
MF_NGINX_HTTP_PORT=8888  # 找个不容易冲突的就行
```

再次`make run`:

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ make run
docker-compose -f docker/docker-compose.yml up
Removing mainflux-nginx
mainflux-nats is up-to-date
mainflux-mqtt-redis is up-to-date
mainflux-things-db is up-to-date
mainflux-es-redis is up-to-date
mainflux-ui is up-to-date
mainflux-things-redis is up-to-date
mainflux-jaeger is up-to-date
Starting 26e7692f31b7_mainflux-nginx ...
mainflux-users-db is up-to-date
mainflux-normalizer is up-to-date
mainflux-users is up-to-date
mainflux-things is up-to-date
mainflux-ws is up-to-date
mainflux-http is up-to-date
mainflux-coap is up-to-date
Starting 26e7692f31b7_mainflux-nginx ... done
Attaching to mainflux-nats, mainflux-mqtt-redis, mainflux-things-db, mainflux-es-redis, mainflux-ui, mainflux-things-redis, mainflux-jaeger, mainflux-users-db, mainflux-normalizer, mainflux-users, mainflux-things, mainflux-ws, mainflux-http, mainflux-coap, mainflux-mqtt, 26e7692f31b7_mainflux-nginx
mainflux-mqtt-redis | 1:C 17 Aug 2019 14:22:43.095 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
mainflux-mqtt-redis | 1:C 17 Aug 2019 14:22:43.095 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
mainflux-mqtt-redis | 1:C 17 Aug 2019 14:22:43.095 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
mainflux-mqtt-redis | 1:M 17 Aug 2019 14:22:43.097 * Running mode=standalone, port=6379.
mainflux-mqtt-redis | 1:M 17 Aug 2019 14:22:43.097 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
mainflux-mqtt-redis | 1:M 17 Aug 2019 14:22:43.097 # Server initialized
mainflux-mqtt-redis | 1:M 17 Aug 2019 14:22:43.097 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
mainflux-mqtt-redis | 1:M 17 Aug 2019 14:22:43.097 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
mainflux-mqtt-redis | 1:M 17 Aug 2019 14:22:43.097 * Ready to accept connections
mainflux-mqtt-redis | 1:signal-handler (1566108428) Received SIGTERM scheduling shutdown...
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:08.140 # User requested shutdown...
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:08.146 * Saving the final RDB snapshot before exiting.
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:08.148 * DB saved on disk
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:08.148 # Redis is now ready to exit, bye bye...
mainflux-mqtt-redis | 1:C 18 Aug 2019 06:07:44.966 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
mainflux-mqtt-redis | 1:C 18 Aug 2019 06:07:44.966 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
mainflux-mqtt-redis | 1:C 18 Aug 2019 06:07:44.966 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 * Running mode=standalone, port=6379.
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 # Server initialized
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 * DB loaded from disk: 0.000 seconds
mainflux-mqtt-redis | 1:M 18 Aug 2019 06:07:44.968 * Ready to accept connections
mainflux-nats   | [1] 2019/08/17 14:22:45.027384 [INF] Starting nats-server version 1.3.0
mainflux-nats   | [1] 2019/08/17 14:22:45.027438 [INF] Git commit [eed4fbc]
mainflux-nats   | [1] 2019/08/17 14:22:45.027536 [INF] Starting http monitor on 0.0.0.0:8222
mainflux-nats   | [1] 2019/08/17 14:22:45.027579 [INF] Listening for client connections on 0.0.0.0:4222
mainflux-nats   | [1] 2019/08/17 14:22:45.027589 [INF] Server is ready
mainflux-nats   | [1] 2019/08/17 14:22:45.028198 [INF] Listening for route connections on 0.0.0.0:6222
mainflux-nats   | [1] 2019/08/18 06:07:47.155252 [INF] Starting nats-server version 1.3.0
mainflux-nats   | [1] 2019/08/18 06:07:47.155297 [INF] Git commit [eed4fbc]
mainflux-nats   | [1] 2019/08/18 06:07:47.155421 [INF] Starting http monitor on 0.0.0.0:8222
mainflux-nats   | [1] 2019/08/18 06:07:47.155498 [INF] Listening for client connections on 0.0.0.0:4222
mainflux-nats   | [1] 2019/08/18 06:07:47.155511 [INF] Server is ready
mainflux-nats   | [1] 2019/08/18 06:07:47.156378 [INF] Listening for route connections on 0.0.0.0:6222
mainflux-things-db | The files belonging to this database system will be owned by user "postgres".
mainflux-things-db | This user must also own the server process.
mainflux-things-db |
mainflux-things-db | The database cluster will be initialized with locale "en_US.utf8".
mainflux-things-db | The default database encoding has accordingly been set to "UTF8".
mainflux-things-db | The default text search configuration will be set to "english".
mainflux-things-db |
mainflux-things-db | Data page checksums are disabled.
mainflux-things-db |
mainflux-things-db | fixing permissions on existing directory /var/lib/postgresql/data ... ok
mainflux-things-db | creating subdirectories ... ok
mainflux-things-db | selecting default max_connections ... 100
mainflux-things-db | selecting default shared_buffers ... 128MB
mainflux-things-db | selecting dynamic shared memory implementation ... posix
mainflux-things-db | creating configuration files ... ok
mainflux-things-db | running bootstrap script ... ok
mainflux-things-db | sh: locale: not found
mainflux-things-db | 2019-08-17 14:22:42.759 UTC [26] WARNING:  no usable system locales were found
mainflux-things-db | performing post-bootstrap initialization ... ok
mainflux-things-db | syncing data to disk ... ok
mainflux-things-db |
mainflux-things-db | Success. You can now start the database server using:
mainflux-things-db |
mainflux-things-db |     pg_ctl -D /var/lib/postgresql/data -l logfile start
mainflux-things-db |
mainflux-things-db |
mainflux-things-db | WARNING: enabling "trust" authentication for local connections
mainflux-things-db | You can change this by editing pg_hba.conf or using the option -A, or
mainflux-things-db | --auth-local and --auth-host, the next time you run initdb.
mainflux-things-db | waiting for server to start....2019-08-17 14:22:43.440 UTC [30] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mainflux-things-db | 2019-08-17 14:22:43.480 UTC [31] LOG:  database system was shut down at 2019-08-17 14:22:43 UTC
mainflux-things-db | 2019-08-17 14:22:43.489 UTC [30] LOG:  database system is ready to accept connections
mainflux-things-db |  done
mainflux-things-db | server started
mainflux-things-db | CREATE DATABASE
mainflux-things-db |
mainflux-things-db |
mainflux-things-db | /usr/local/bin/docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*
mainflux-things-db |
mainflux-things-db | waiting for server to shut down....2019-08-17 14:22:43.785 UTC [30] LOG:  received fast shutdown request
mainflux-things-db | 2019-08-17 14:22:43.787 UTC [30] LOG:  aborting any active transactions
mainflux-things-db | 2019-08-17 14:22:43.788 UTC [30] LOG:  worker process: logical replication launcher (PID 37) exited with exit code 1
mainflux-things-db | 2019-08-17 14:22:43.788 UTC [32] LOG:  shutting down
mainflux-things-db | 2019-08-17 14:22:43.826 UTC [30] LOG:  database system is shut down
mainflux-things-db |  done
mainflux-things-db | server stopped
mainflux-things-db |
mainflux-things-db | PostgreSQL init process complete; ready for start up.
mainflux-things-db |
mainflux-things-db | 2019-08-17 14:22:43.893 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
mainflux-things-db | 2019-08-17 14:22:43.893 UTC [1] LOG:  listening on IPv6 address "::", port 5432
mainflux-things-db | 2019-08-17 14:22:43.910 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mainflux-things-db | 2019-08-17 14:22:43.947 UTC [41] LOG:  database system was shut down at 2019-08-17 14:22:43 UTC
mainflux-things-db | 2019-08-17 14:22:43.956 UTC [1] LOG:  database system is ready to accept connections
mainflux-things-db | 2019-08-18 06:07:08.879 UTC [1] LOG:  received smart shutdown request
mainflux-things-db | 2019-08-18 06:07:08.894 UTC [1] LOG:  worker process: logical replication launcher (PID 47) exited with exit code 1
mainflux-things-db | 2019-08-18 06:07:08.895 UTC [42] LOG:  shutting down
mainflux-things-db | 2019-08-18 06:07:08.932 UTC [1] LOG:  database system is shut down
mainflux-things-db | 2019-08-18 06:07:42.226 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
mainflux-things-db | 2019-08-18 06:07:42.226 UTC [1] LOG:  listening on IPv6 address "::", port 5432
mainflux-things-db | 2019-08-18 06:07:42.245 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mainflux-things-db | 2019-08-18 06:07:42.285 UTC [18] LOG:  database system was shut down at 2019-08-18 06:07:08 UTC
mainflux-things-db | 2019-08-18 06:07:42.299 UTC [1] LOG:  database system is ready to accept connections
mainflux-es-redis | 1:C 17 Aug 2019 14:22:41.792 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
mainflux-es-redis | 1:C 17 Aug 2019 14:22:41.793 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
mainflux-es-redis | 1:C 17 Aug 2019 14:22:41.793 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
mainflux-es-redis | 1:M 17 Aug 2019 14:22:41.796 * Running mode=standalone, port=6379.
mainflux-es-redis | 1:M 17 Aug 2019 14:22:41.796 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
mainflux-es-redis | 1:M 17 Aug 2019 14:22:41.796 # Server initialized
mainflux-es-redis | 1:M 17 Aug 2019 14:22:41.796 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
mainflux-es-redis | 1:M 17 Aug 2019 14:22:41.796 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
mainflux-es-redis | 1:M 17 Aug 2019 14:22:41.796 * Ready to accept connections
mainflux-es-redis | 1:signal-handler (1566108417) Received SIGTERM scheduling shutdown...
mainflux-es-redis | 1:M 18 Aug 2019 06:06:57.570 # User requested shutdown...
mainflux-es-redis | 1:M 18 Aug 2019 06:06:57.619 * Saving the final RDB snapshot before exiting.
mainflux-es-redis | 1:M 18 Aug 2019 06:06:57.629 * DB saved on disk
mainflux-es-redis | 1:M 18 Aug 2019 06:06:57.629 # Redis is now ready to exit, bye bye...
mainflux-es-redis | 1:C 18 Aug 2019 06:07:48.200 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
mainflux-es-redis | 1:C 18 Aug 2019 06:07:48.200 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
mainflux-es-redis | 1:C 18 Aug 2019 06:07:48.200 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.202 * Running mode=standalone, port=6379.
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.202 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.202 # Server initialized
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.202 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.202 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.209 * DB loaded from disk: 0.007 seconds
mainflux-es-redis | 1:M 18 Aug 2019 06:07:48.209 * Ready to accept connections
mainflux-jaeger | 2019/08/17 14:22:46 maxprocs: Leaving GOMAXPROCS=4: CPU quota undefined
mainflux-jaeger | {"level":"info","ts":1566051766.2253852,"caller":"flags/service.go:115","msg":"Mounting metrics handler on admin server","route":"/metrics"}
mainflux-jaeger | {"level":"info","ts":1566051766.2256029,"caller":"flags/admin.go:108","msg":"Mounting health check on admin server","route":"/"}
mainflux-jaeger | {"level":"info","ts":1566051766.225683,"caller":"flags/admin.go:114","msg":"Starting admin HTTP server","http-port":14269}
mainflux-jaeger | {"level":"info","ts":1566051766.2257037,"caller":"flags/admin.go:100","msg":"Admin server started","http-port":14269,"health-status":"unavailable"}
mainflux-jaeger | {"level":"info","ts":1566051766.2808497,"caller":"memory/factory.go:55","msg":"Memory storage initialized","configuration":{"MaxTraces":0}}
mainflux-jaeger | {"level":"info","ts":1566051766.2881448,"caller":"grpc/builder.go:75","msg":"Agent requested insecure grpc connection to collector(s)"}
mainflux-jaeger | {"level":"info","ts":1566051766.2888844,"caller":"grpc/clientconn.go:242","msg":"parsed scheme: \"\"","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.2898757,"caller":"grpc/clientconn.go:248","msg":"scheme \"\" not registered, fallback to default scheme","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.2899027,"caller":"grpc/resolver_conn_wrapper.go:113","msg":"ccResolverWrapper: sending update to cc: {[{127.0.0.1:14250 0  <nil>}] }","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.2901025,"caller":"base/balancer.go:76","msg":"base.baseBalancer: got new resolver state: {[{127.0.0.1:14250 0  <nil>}] }","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.290183,"caller":"base/balancer.go:130","msg":"base.baseBalancer: handle SubConn state change: 0xc000446040, CONNECTING","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.2915914,"caller":"all-in-one/main.go:198","msg":"Starting agent"}
mainflux-jaeger | {"level":"info","ts":1566051766.2918808,"caller":"grpc/clientconn.go:1139","msg":"grpc: addrConn.createTransport failed to connect to {127.0.0.1:14250 0  <nil>}. Err :connection error: desc = \"transport: Error while dialing dial tcp 127.0.0.1:14250: connect: connection refused\". Reconnecting...","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.2919507,"caller":"base/balancer.go:130","msg":"base.baseBalancer: handle SubConn state change: 0xc000446040, TRANSIENT_FAILURE","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566051766.2920654,"caller":"app/agent.go:68","msg":"Starting jaeger-agent HTTP server","http-port":5778}
mainflux-jaeger | {"level":"info","ts":1566051766.2961102,"caller":"all-in-one/main.go:241","msg":"Starting jaeger-collector TChannel server","port":14267}
mainflux-jaeger | {"level":"info","ts":1566051766.296204,"caller":"grpcserver/grpc_server.go:64","msg":"Starting jaeger-collector gRPC server","grpc-port":"14250"}
mainflux-jaeger | {"level":"info","ts":1566051766.2963338,"caller":"all-in-one/main.go:259","msg":"Starting jaeger-collector HTTP server","http-port":14268}
mainflux-jaeger | {"level":"info","ts":1566051766.2963643,"caller":"querysvc/query_service.go:130","msg":"Archive storage not created","reason":"archive storage not supported"}
mainflux-jaeger | {"level":"info","ts":1566051766.2967296,"caller":"all-in-one/main.go:341","msg":"Archive storage not initialized"}
mainflux-jaeger | {"level":"info","ts":1566051766.2974143,"caller":"healthcheck/handler.go:129","msg":"Health Check state change","status":"ready"}
mainflux-jaeger | {"level":"info","ts":1566051766.2974267,"caller":"app/server.go:111","msg":"Starting HTTP server","port":16686}
mainflux-jaeger | {"level":"info","ts":1566051766.2974463,"caller":"app/server.go:134","msg":"Starting CMUX server","port":16686}
mainflux-jaeger | {"level":"info","ts":1566051766.2974427,"caller":"app/server.go:124","msg":"Starting GRPC server","port":16686}
mainflux-jaeger | {"level":"info","ts":1566108417.5729327,"caller":"flags/service.go:145","msg":"Shutting down"}
mainflux-jaeger | {"level":"info","ts":1566108417.5810058,"caller":"healthcheck/handler.go:129","msg":"Health Check state change","status":"unavailable"}
mainflux-jaeger | {"level":"info","ts":1566108417.729596,"caller":"flags/service.go:153","msg":"Shutdown complete"}
mainflux-jaeger | 2019/08/18 06:07:46 maxprocs: Leaving GOMAXPROCS=4: CPU quota undefined
mainflux-jaeger | {"level":"info","ts":1566108466.326627,"caller":"flags/service.go:115","msg":"Mounting metrics handler on admin server","route":"/metrics"}
mainflux-jaeger | {"level":"info","ts":1566108466.3268385,"caller":"flags/admin.go:108","msg":"Mounting health check on admin server","route":"/"}
mainflux-jaeger | {"level":"info","ts":1566108466.3269293,"caller":"flags/admin.go:114","msg":"Starting admin HTTP server","http-port":14269}
mainflux-jaeger | {"level":"info","ts":1566108466.3269587,"caller":"flags/admin.go:100","msg":"Admin server started","http-port":14269,"health-status":"unavailable"}
mainflux-jaeger | {"level":"info","ts":1566108466.3384168,"caller":"memory/factory.go:55","msg":"Memory storage initialized","configuration":{"MaxTraces":0}}
mainflux-jaeger | {"level":"info","ts":1566108466.345234,"caller":"grpc/builder.go:75","msg":"Agent requested insecure grpc connection to collector(s)"}
mainflux-jaeger | {"level":"info","ts":1566108466.346065,"caller":"grpc/clientconn.go:242","msg":"parsed scheme: \"\"","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.347057,"caller":"grpc/clientconn.go:248","msg":"scheme \"\" not registered, fallback to default scheme","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.3471303,"caller":"grpc/resolver_conn_wrapper.go:113","msg":"ccResolverWrapper: sending update to cc: {[{127.0.0.1:14250 0  <nil>}] }","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.3473892,"caller":"base/balancer.go:76","msg":"base.baseBalancer: got new resolver state: {[{127.0.0.1:14250 0  <nil>}] }","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.3474433,"caller":"base/balancer.go:130","msg":"base.baseBalancer: handle SubConn state change: 0xc0003e0050, CONNECTING","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.3480797,"caller":"all-in-one/main.go:198","msg":"Starting agent"}
mainflux-jaeger | {"level":"info","ts":1566108466.3481708,"caller":"app/agent.go:68","msg":"Starting jaeger-agent HTTP server","http-port":5778}
mainflux-jaeger | {"level":"info","ts":1566108466.3481946,"caller":"grpc/clientconn.go:1139","msg":"grpc: addrConn.createTransport failed to connect to {127.0.0.1:14250 0  <nil>}. Err :connection error: desc = \"transport: Error while dialing dial tcp 127.0.0.1:14250: connect: connection refused\". Reconnecting...","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.3482506,"caller":"base/balancer.go:130","msg":"base.baseBalancer: handle SubConn state change: 0xc0003e0050, TRANSIENT_FAILURE","system":"grpc","grpc_log":true}
mainflux-jaeger | {"level":"info","ts":1566108466.3515522,"caller":"all-in-one/main.go:241","msg":"Starting jaeger-collector TChannel server","port":14267}
mainflux-jaeger | {"level":"info","ts":1566108466.3516686,"caller":"grpcserver/grpc_server.go:64","msg":"Starting jaeger-collector gRPC server","grpc-port":"14250"}
mainflux-jaeger | {"level":"info","ts":1566108466.3520057,"caller":"all-in-one/main.go:259","msg":"Starting jaeger-collector HTTP server","http-port":14268}
mainflux-jaeger | {"level":"info","ts":1566108466.3520448,"caller":"querysvc/query_service.go:130","msg":"Archive storage not created","reason":"archive storage not supported"}
mainflux-jaeger | {"level":"info","ts":1566108466.3523493,"caller":"all-in-one/main.go:341","msg":"Archive storage not initialized"}
mainflux-jaeger | {"level":"info","ts":1566108466.3539069,"caller":"healthcheck/handler.go:129","msg":"Health Check state change","status":"ready"}
mainflux-jaeger | {"level":"info","ts":1566108466.3539748,"caller":"app/server.go:134","msg":"Starting CMUX server","port":16686}
mainflux-jaeger | {"level":"info","ts":1566108466.3540401,"caller":"app/server.go:111","msg":"Starting HTTP server","port":16686}
mainflux-jaeger | {"level":"info","ts":1566108466.354081,"caller":"app/server.go:124","msg":"Starting GRPC server","port":16686}
mainflux-things-redis | 1:C 17 Aug 2019 14:22:44.664 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
mainflux-things-redis | 1:C 17 Aug 2019 14:22:44.664 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
mainflux-things-redis | 1:C 17 Aug 2019 14:22:44.664 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
mainflux-things-redis | 1:M 17 Aug 2019 14:22:44.666 * Running mode=standalone, port=6379.
mainflux-things-redis | 1:M 17 Aug 2019 14:22:44.667 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
mainflux-things-redis | 1:M 17 Aug 2019 14:22:44.667 # Server initialized
mainflux-things-redis | 1:M 17 Aug 2019 14:22:44.667 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
mainflux-things-redis | 1:M 17 Aug 2019 14:22:44.667 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
mainflux-things-redis | 1:M 17 Aug 2019 14:22:44.667 * Ready to accept connections
mainflux-things-redis | 1:signal-handler (1566108417) Received SIGTERM scheduling shutdown...
mainflux-things-redis | 1:M 18 Aug 2019 06:06:57.570 # User requested shutdown...
mainflux-things-redis | 1:M 18 Aug 2019 06:06:57.621 * Saving the final RDB snapshot before exiting.
mainflux-things-redis | 1:M 18 Aug 2019 06:06:57.653 * DB saved on disk
mainflux-things-redis | 1:M 18 Aug 2019 06:06:57.653 # Redis is now ready to exit, bye bye...
mainflux-things-redis | 1:C 18 Aug 2019 06:07:43.988 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
mainflux-things-redis | 1:C 18 Aug 2019 06:07:43.989 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
mainflux-things-redis | 1:C 18 Aug 2019 06:07:43.989 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.991 * Running mode=standalone, port=6379.
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.991 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.991 # Server initialized
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.991 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.991 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.992 * DB loaded from disk: 0.000 seconds
mainflux-things-redis | 1:M 18 Aug 2019 06:07:43.992 * Ready to accept connections
mainflux-users-db | The files belonging to this database system will be owned by user "postgres".
mainflux-users-db | This user must also own the server process.
mainflux-users-db |
mainflux-users-db | The database cluster will be initialized with locale "en_US.utf8".
mainflux-users-db | The default database encoding has accordingly been set to "UTF8".
mainflux-users-db | The default text search configuration will be set to "english".
mainflux-users-db |
mainflux-users-db | Data page checksums are disabled.
mainflux-users-db |
mainflux-users-db | fixing permissions on existing directory /var/lib/postgresql/data ... ok
mainflux-users-db | creating subdirectories ... ok
mainflux-users-db | selecting default max_connections ... 100
mainflux-users-db | selecting default shared_buffers ... 128MB
mainflux-users-db | selecting dynamic shared memory implementation ... posix
mainflux-users-db | creating configuration files ... ok
mainflux-users-db | running bootstrap script ... ok
mainflux-users-db | sh: locale: not found
mainflux-users-db | 2019-08-17 14:22:42.047 UTC [26] WARNING:  no usable system locales were found
mainflux-users-db | performing post-bootstrap initialization ... ok
mainflux-users-db | syncing data to disk ... ok
mainflux-users-db |
mainflux-users-db | Success. You can now start the database server using:
mainflux-users-db |
mainflux-users-db |     pg_ctl -D /var/lib/postgresql/data -l logfile start
mainflux-users-db |
mainflux-users-db |
mainflux-users-db | WARNING: enabling "trust" authentication for local connections
mainflux-users-db | You can change this by editing pg_hba.conf or using the option -A, or
mainflux-users-db | --auth-local and --auth-host, the next time you run initdb.
mainflux-users-db | waiting for server to start....2019-08-17 14:22:42.807 UTC [30] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mainflux-users-db | 2019-08-17 14:22:42.862 UTC [31] LOG:  database system was shut down at 2019-08-17 14:22:42 UTC
mainflux-users-db | 2019-08-17 14:22:42.871 UTC [30] LOG:  database system is ready to accept connections
mainflux-users-db |  done
mainflux-users-db | server started
mainflux-users-db | CREATE DATABASE
mainflux-users-db |
mainflux-users-db |
mainflux-users-db | /usr/local/bin/docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*
mainflux-users-db |
mainflux-users-db | waiting for server to shut down....2019-08-17 14:22:43.169 UTC [30] LOG:  received fast shutdown request
mainflux-users-db | 2019-08-17 14:22:43.171 UTC [30] LOG:  aborting any active transactions
mainflux-users-db | 2019-08-17 14:22:43.172 UTC [30] LOG:  worker process: logical replication launcher (PID 37) exited with exit code 1
mainflux-users-db | 2019-08-17 14:22:43.172 UTC [32] LOG:  shutting down
mainflux-users-db | 2019-08-17 14:22:43.216 UTC [30] LOG:  database system is shut down
mainflux-users-db |  done
mainflux-users-db | server stopped
mainflux-users-db |
mainflux-users-db | PostgreSQL init process complete; ready for start up.
mainflux-users-db |
mainflux-users-db | 2019-08-17 14:22:43.359 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
mainflux-users-db | 2019-08-17 14:22:43.359 UTC [1] LOG:  listening on IPv6 address "::", port 5432
mainflux-users-db | 2019-08-17 14:22:43.373 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mainflux-users-db | 2019-08-17 14:22:43.406 UTC [41] LOG:  database system was shut down at 2019-08-17 14:22:43 UTC
mainflux-users-db | 2019-08-17 14:22:43.421 UTC [1] LOG:  database system is ready to accept connections
mainflux-users-db | 2019-08-18 06:07:09.341 UTC [1] LOG:  received smart shutdown request
mainflux-users-db | 2019-08-18 06:07:09.349 UTC [1] LOG:  worker process: logical replication launcher (PID 47) exited with exit code 1
mainflux-users-db | 2019-08-18 06:07:09.349 UTC [42] LOG:  shutting down
mainflux-users-db | 2019-08-18 06:07:09.416 UTC [1] LOG:  database system is shut down
mainflux-users-db | 2019-08-18 06:07:50.280 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
mainflux-users-db | 2019-08-18 06:07:50.280 UTC [1] LOG:  listening on IPv6 address "::", port 5432
mainflux-users-db | 2019-08-18 06:07:50.310 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
mainflux-users-db | 2019-08-18 06:07:50.362 UTC [18] LOG:  database system was shut down at 2019-08-18 06:07:09 UTC
mainflux-users-db | 2019-08-18 06:07:50.373 UTC [1] LOG:  database system is ready to accept connections
mainflux-ws     | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-17T14:22:54.741095445Z"}
mainflux-ws     | {"level":"info","message":"WebSocket adapter service started, exposed port 8186","ts":"2019-08-17T14:22:54.750836216Z"}
mainflux-ws     | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-18T06:07:56.461422464Z"}
mainflux-ws     | {"level":"info","message":"WebSocket adapter service started, exposed port 8186","ts":"2019-08-18T06:07:56.464088709Z"}
mainflux-users  | {"level":"info","message":"Users service started using http, exposed port 8180","ts":"2019-08-17T14:22:44.079984747Z"}
mainflux-users  | {"level":"info","message":"Users gRPC service started using http on port 8181","ts":"2019-08-17T14:22:44.08013605Z"}
mainflux-users  | {"level":"info","message":"Users gRPC service started, exposed port 8181","ts":"2019-08-17T14:22:44.080197181Z"}
mainflux-users  | {"level":"info","message":"Users service started using http, exposed port 8180","ts":"2019-08-18T06:07:51.4680891Z"}
mainflux-users  | {"level":"info","message":"Users gRPC service started using http on port 8181","ts":"2019-08-18T06:07:51.46830819Z"}
mainflux-users  | {"level":"info","message":"Users gRPC service started, exposed port 8181","ts":"2019-08-18T06:07:51.468404853Z"}
mainflux-normalizer | {"level":"info","message":"Normalizer service started, exposed port 8184","ts":"2019-08-17T14:22:48.535022537Z"}
mainflux-normalizer | {"level":"info","message":"Normalizer service started, exposed port 8184","ts":"2019-08-18T06:07:49.373415242Z"}
mainflux-things | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-17T14:22:49.750079428Z"}
mainflux-things | {"level":"info","message":"Things service started using http on port 8182","ts":"2019-08-17T14:22:49.752361349Z"}
mainflux-things | {"level":"info","message":"Things service started using http on port 8182","ts":"2019-08-17T14:22:49.752361346Z"}
mainflux-things | {"level":"info","message":"Things gRPC service started using http on port 8183","ts":"2019-08-17T14:22:49.752597507Z"}
mainflux-things | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-18T06:07:52.727918949Z"}
mainflux-things | {"level":"info","message":"Things gRPC service started using http on port 8183","ts":"2019-08-18T06:07:52.738161107Z"}
mainflux-things | {"level":"info","message":"Things service started using http on port 8182","ts":"2019-08-18T06:07:52.738246397Z"}
mainflux-things | {"level":"info","message":"Things service started using http on port 8182","ts":"2019-08-18T06:07:52.7383652Z"}
mainflux-http   | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-17T14:22:52.73695003Z"}
mainflux-http   | {"level":"info","message":"HTTP adapter service started on port 8185","ts":"2019-08-17T14:22:52.748463145Z"}
mainflux-http   | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-18T06:07:55.515667114Z"}
mainflux-http   | {"level":"info","message":"HTTP adapter service started on port 8185","ts":"2019-08-18T06:07:55.519133562Z"}
mainflux-coap   | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-17T14:22:53.7389281Z"}
mainflux-coap   | {"level":"info","message":"CoAP adapter service started, exposed port 5683","ts":"2019-08-17T14:22:53.749800784Z"}
mainflux-coap   | {"level":"info","message":"CoAP service started, exposed port 5683","ts":"2019-08-17T14:22:53.74977476Z"}
mainflux-coap   | {"level":"info","message":"gRPC communication is not encrypted","ts":"2019-08-18T06:07:54.325773167Z"}
mainflux-coap   | {"level":"info","message":"CoAP service started, exposed port 5683","ts":"2019-08-18T06:07:54.335582739Z"}
mainflux-coap   | {"level":"info","message":"CoAP adapter service started, exposed port 5683","ts":"2019-08-18T06:07:54.33578897Z"}
mainflux-mqtt   | D0817 14:22:52.050663105       1 env_linux.cc:71]            Warning: insecure environment read function 'getenv' used
mainflux-mqtt   | {"level":30,"time":1566051772452,"msg":"listening","pid":1,"hostname":"941c67d26e08","address":"::","family":"IPv6","port":1883,"protocol":"tcp","v":1}
mainflux-mqtt   | {"level":30,"time":1566051772453,"msg":"listening","pid":1,"hostname":"941c67d26e08","address":"::","family":"IPv6","port":8880,"protocol":"http","v":1}
mainflux-mqtt   | node_redis: Warning: Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | [WARN] Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | [WARN] Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | [WARN] Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | {"name":"mqtt","hostname":"941c67d26e08","pid":1,"level":40,"msg":"error on redis connection: Redis connection to es-redis:6379 failed - connect ECONNREFUSED 172.18.0.3:6379","time":"2019-08-18T06:06:57.912Z","v":0}
mainflux-mqtt   | {"name":"mqtt","hostname":"941c67d26e08","pid":1,"level":40,"msg":"error on redis connection: Redis connection to es-redis:6379 failed - connect ECONNREFUSED 172.18.0.3:6379","time":"2019-08-18T06:06:58.259Z","v":0}
mainflux-mqtt   | {"name":"mqtt","hostname":"941c67d26e08","pid":1,"level":40,"msg":"error on redis connection: Redis connection to es-redis:6379 failed - getaddrinfo ENOTFOUND es-redis es-redis:6379","time":"2019-08-18T06:06:58.887Z","v":0}
mainflux-mqtt   | {"name":"mqtt","hostname":"941c67d26e08","pid":1,"level":40,"msg":"error on redis connection: Redis connection to es-redis:6379 failed - getaddrinfo ENOTFOUND es-redis es-redis:6379","time":"2019-08-18T06:06:59.904Z","v":0}
mainflux-mqtt   | {"name":"mqtt","hostname":"941c67d26e08","pid":1,"level":40,"msg":"error on redis connection: Redis connection to es-redis:6379 failed - getaddrinfo ENOTFOUND es-redis es-redis:6379","time":"2019-08-18T06:07:01.585Z","v":0}
mainflux-mqtt   | {"name":"mqtt","hostname":"941c67d26e08","pid":1,"level":40,"msg":"error on redis connection: Redis connection to es-redis:6379 failed - getaddrinfo ENOTFOUND es-redis es-redis:6379","time":"2019-08-18T06:07:04.444Z","v":0}
mainflux-mqtt   | D0818 06:07:58.025853349       1 env_linux.cc:71]            Warning: insecure environment read function 'getenv' used
mainflux-mqtt   | {"level":30,"time":1566108478429,"msg":"listening","pid":1,"hostname":"941c67d26e08","address":"::","family":"IPv6","port":1883,"protocol":"tcp","v":1}
mainflux-mqtt   | {"level":30,"time":1566108478430,"msg":"listening","pid":1,"hostname":"941c67d26e08","address":"::","family":"IPv6","port":8880,"protocol":"http","v":1}
mainflux-mqtt   | node_redis: Warning: Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | [WARN] Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | [WARN] Redis server does not require a password, but a password was supplied.
mainflux-mqtt   | [WARN] Redis server does not require a password, but a password was supplied.
```

这样就算是正常启动了，但是启动过程中一些警告（主要是Redis的警告）和有一条日志INFO报告连接gRPC端口14250时失败，完整看完日志信息后，认为这些应该是正常的。

现在再列举下容器列表：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                                                                                                                               NAMES
26e7692f31b7        nginx:1.16.0-alpine             "/entrypoint.sh"         About an hour ago   Up 12 minutes       0.0.0.0:443->443/tcp, 0.0.0.0:8883->8883/tcp, 80/tcp, 0.0.0.0:8888->8888/tcp                                                        26e7692f31b7_mainflux-nginx
4bb5cd9596ea        mainflux/ws:latest              "/exe"                   17 hours ago        Up 58 minutes       0.0.0.0:8186->8186/tcp                                                                                                              mainflux-ws
8c0c4ccbbd55        mainflux/coap:latest            "/exe"                   17 hours ago        Up 58 minutes       0.0.0.0:5683->5683/udp                                                                                                              mainflux-coap
941c67d26e08        mainflux/mqtt:latest            "node mqtt.js"           17 hours ago        Up 58 minutes       0.0.0.0:1883->1883/tcp, 0.0.0.0:8880->8880/tcp                                                                                      mainflux-mqtt
51206daffd4f        mainflux/http:latest            "/exe"                   17 hours ago        Up 58 minutes       0.0.0.0:8185->8185/tcp                                                                                                              mainflux-http
eb9bc8ac2e0a        mainflux/normalizer:latest      "/exe"                   17 hours ago        Up 58 minutes       0.0.0.0:8184->8184/tcp                                                                                                              mainflux-normalizer
d1579ed65dff        mainflux/things:latest          "/exe"                   17 hours ago        Up 58 minutes       0.0.0.0:8182-8183->8182-8183/tcp, 0.0.0.0:8989->8989/tcp                                                                            mainflux-things
fea1599f1faf        mainflux/users:latest           "/exe"                   17 hours ago        Up 58 minutes       0.0.0.0:8180->8180/tcp, 8181/tcp                                                                                                    mainflux-users
d20f696b6a37        nats:1.3.0                      "/gnatsd -c gnatsd.c…"   17 hours ago        Up 58 minutes       4222/tcp, 6222/tcp, 8222/tcp                                                                                                        mainflux-nats
08b0d3a188f9        postgres:10.8-alpine            "docker-entrypoint.s…"   17 hours ago        Up 58 minutes       5432/tcp                                                                                                                            mainflux-users-db
310c048cb4f3        jaegertracing/all-in-one:1.13   "/go/bin/all-in-one-…"   17 hours ago        Up 58 minutes       5775/udp, 0.0.0.0:5778->5778/tcp, 0.0.0.0:14268->14268/tcp, 6832/udp, 0.0.0.0:16686->16686/tcp, 0.0.0.0:6831->6831/udp, 14250/tcp   mainflux-jaeger
d84ae3be3425        redis:5.0-alpine                "docker-entrypoint.s…"   17 hours ago        Up 58 minutes       6379/tcp                                                                                                                            mainflux-es-redis
aef24619a59a        redis:5.0-alpine                "docker-entrypoint.s…"   17 hours ago        Up 58 minutes       6379/tcp                                                                                                                            mainflux-things-redis
3afec1d3a3e0        postgres:10.8-alpine            "docker-entrypoint.s…"   17 hours ago        Up 58 minutes       5432/tcp                                                                                                                            mainflux-things-db
6f05dbae388f        redis:5.0-alpine                "docker-entrypoint.s…"   17 hours ago        Up 58 minutes       6379/tcp                                                                                                                            mainflux-mqtt-redis
0f9972dc12c5        mainflux/ui:latest              "/entrypoint.sh"         17 hours ago        Up 58 minutes       80/tcp, 0.0.0.0:3000->3000/tcp                                                                                                      mainflux-ui
```

我们已经启动了mainflux-ui，那么就可以在浏览器查看下我们的mainflux-ui了，浏览器输入127.0.0.1:3000，得到如下结果：

<!--![mainflux-ui-01](/images/mainflux-ui-01.png)-->
![mainflux-ui-01](../../../static/images/mainflux-ui-01.png)

### 12.2 安装mainflux-cli

上一节中启动了mainflux的所有docker微服务，接下来要安装mainflux客户端来进行一些操作了。

mainflux-cli应当从github页面<https://github.com/mainflux/mainflux/releases>下载最新发行版，并将之解压后放到本机`$GOBIN`目录下。这就完成了安装。

但是我在前边手动编译的环节其实已经完成了mainflux-cli安装，所以这一步跳过。

### 12.3 系统测试配置（Provisioning）

在系统测试之前，应执行：

```shell
mainflux-cli provision test
```

这一步会生成一个临时测试用户`user`，并登陆，然后创建属于其的两个`thing`和两个`channel`。这将用作测试场景。关于系统准备`system provision`的更多内容参考：<https://mainflux.readthedocs.io/en/latest/provisioning/>

但是现在无论是在哪里执行`mainflux-cli provision test`都会报错：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ mainflux-cli provision test

failed to create entity
```

好了，现在让我们来看下mainflux-cli部分源码吧。

mainflux-cli主要包括`mainflux/cmd/cli/main.go`入口文件和`mainflux/cli/`下所有文件，我们现在主要关心`provision`命令，所以我们主要查看`mainflux/cli/provision.go`文件，在其中我们找到provision的test子命令所在：

```go
cobra.Command{
    Use:   "test",
    Short: "test",
    Long: `Provisions test setup: one test user, two things and two channels. \
            Connect both things to one of the channels, \
            and only on thing to other channel.`,
    Run: func(cmd *cobra.Command, args []string) {
        numThings := 2
        numChan := 2
        things := []mfxsdk.Thing{}
        channels := []mfxsdk.Channel{}

        if len(args) != 0 {
        logUsage(cmd.Short)
        return
        }

        un := fmt.Sprintf("%s@email.com", namesgenerator.GetRandomName(0))
        // Create test user
        user := mfxsdk.User{
            Email:    un,
            Password: "123",
        }
        if err := sdk.CreateUser(user); err != nil {
            logError(err)
            return
        }

        ut, err := sdk.CreateToken(user)
        if err != nil {
            logError(err)
            return
        }

        // Create things
        for i := 0; i < numThings; i++ {
            n := fmt.Sprintf("d%d", i)

            m, err := createThing(n, ut)
            if err != nil {
                logError(err)
                return
            }

            things = append(things, m)
        }
        // Create channels
        for i := 0; i < numChan; i++ {
            n := fmt.Sprintf("c%d", i)
            c, err := createChannel(n, ut)
            if err != nil {
                logError(err)
                return
            }

            channels = append(channels, c)
        }

        // Connect things to channels - first thing to both channels, second only to first
        for i := 0; i < numThings; i++ {
            if err := sdk.ConnectThing(things[i].ID, channels[i].ID, ut); err != nil {
                logError(err)
                return
            }

            if i%2 == 0 {
                if i+1 >= len(channels) {
                    break
                }
                if err := sdk.ConnectThing(things[i].ID, channels[i+1].ID, ut); err != nil {
                    logError(err)
                    return
                }
            }
        }

        logJSON(user, ut, things, channels)
    },
```

我在输入`mainflux-cli provision test`时报了无法创建实体的错，那么按照子命令中运行流程来看第一个可能出错的位置在于“Create test user”一段，那么我们进而去查看`sdk.CreateUser`方法的定义，在mainflux/sdk/go/sdk.go中我们看到SDK接口中定义了该方法，进而在mainflux/sdk/go/users.go中看到该方法的实现：

```go
func (sdk mfSDK) CreateUser(user User) error {
    data, err := json.Marshal(user)
    if err != nil {
        return ErrInvalidArgs
    }

    url := createURL(sdk.baseURL, sdk.usersPrefix, "users")

    resp, err := sdk.client.Post(url, string(CTJSON), bytes.NewReader(data))
    if err != nil {
        return err
    }

    if resp.StatusCode != http.StatusCreated {
        switch resp.StatusCode {
        case http.StatusBadRequest:
            return ErrInvalidArgs
        case http.StatusConflict:
            return ErrConflict
        default:
            return ErrFailedCreation
        }
    }

    return nil
}
```

这里的`ErrFailedCreation`看上去可能会是我们碰到的“无法创建实体”错误，我们查看下其定义：

```go
// mainflux/sdk/go/sdk.go
var (
    // ErrFailedCreation indicates that entity creation failed.
    ErrFailedCreation = errors.New("failed to create entity")
)
```

果然是这个错误！那么说明我们在执行`mainflux-cli provision test`时出错片段在：

```go
if resp.StatusCode != http.StatusCreated {
    switch resp.StatusCode {
    case http.StatusBadRequest:
        return ErrInvalidArgs
    case http.StatusConflict:
        return ErrConflict
    default:
        return ErrFailedCreation
    }
}
```

这说明`CreateUser`方法在向服务发起请求后得到的回应是有问题的，而且不是网络状态问题，也不是无效参数问题。所以我们得检查下user服务。

在进一步源码阅读之前，我们先再阅读下官方文档`provisioning`章节，在<https://mainflux.readthedocs.io/en/latest/provisioning/#users-management>中直接使用给出的请求命令，得到的结果是：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"john.doe@email.com", "password":"123"}'
HTTP/2 201
server: nginx/1.16.0
date: Sun, 18 Aug 2019 08:31:37 GMT
content-type: application/json
content-length: 0
strict-transport-security: max-age=63072000; includeSubdomains
x-frame-options: DENY
x-content-type-options: nosniff
access-control-allow-origin: *
access-control-allow-methods: *
access-control-allow-headers: *
```

连接被拒绝了，而且在该节还有个小提示：`Note that when using official docker-compose, all services are behind nginx proxy and all traffic is TLS encrypted.`，而我们启动正是以docker-compose方式启动，所以我们前面`test`失败也很可能是因为客户端是遵循非安全连接而服务端只接受安全连接导致连接请求被拒绝！

下面是几次尝试：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"john.doe@email.com", "password":"123"}'
HTTP/2 201
server: nginx/1.16.0
date: Sun, 18 Aug 2019 08:31:37 GMT
content-type: application/json
content-length: 0
strict-transport-security: max-age=63072000; includeSubdomains
x-frame-options: DENY
x-content-type-options: nosniff
access-control-allow-origin: *
access-control-allow-methods: *
access-control-allow-headers: *

eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --secure -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"john.doe@email.com", "password":"123"}'
curl: option --secure: is unknown
curl: try 'curl --help' or 'curl --manual' for more information
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"john.doe@email.com", "password":"123"}'
curl: (77) error setting certificate verify locations:
  CAfile: docker/ssl/certs/mainflux-server.crt
  CApath: /etc/ssl/certs
eiger@eiger-ThinkPad-X1-Carbon-3rd:~$ cd ~/gopath-default/src/github.com/mainflux/mainflux/
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"john.doe@email.com", "password":"123"}'
curl: (60) SSL certificate problem: unable to get local issuer certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$
```

尽管摸索出来需要在mainflux根目录运行，但仍然提示说验证失败无法建立安全连接`curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it.`

现在的情况是nginx反向代理服务器需要安全连接，而我们不知道怎样建立安全连接。所以有两条路走：

- 学习如何建立安全连接
- 修改dockercompose.yml使nginx不要求安全连接，也就是不检查证书

在mainflux根目录下输入发起注册用户请求：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"374192922@email.com", "password":"123456"}'
HTTP/2 201
server: nginx/1.16.0
date: Sun, 18 Aug 2019 09:12:35 GMT
content-type: application/json
content-length: 0
strict-transport-security: max-age=63072000; includeSubdomains
x-frame-options: DENY
x-content-type-options: nosniff
access-control-allow-origin: *
access-control-allow-methods: *
access-control-allow-headers: *
```

这里返回结果我也不清楚是成功还是失败。

同时在mainflux服务终端输出了相应信息：

```shell
mainflux-users  | {"level":"info","message":"Method register for user 374192922@email.com took 90.009818ms to complete without errors.","ts":"2019-08-18T09:12:35.369333654Z"}
```

好像又是注册成功了的意思？？根据官方文档<https://mainflux.readthedocs.io/en/latest/getting-started/#step-3-provision-the-system>处用户注册成功的案例描述，应该确实是注册成功了。

那么也就是通过这样的方式确实能够注册用户。

接下来我在mainflux-ui尝试登陆却出错了：

<!--![mainflux-ui-02](/images/mainflux-ui-02.png)-->
![mainflux-ui-02](../../../static/images/mainflux-ui-02.png)

同时mainflux系统终端输出：

```shell
mainflux-ui     | 172.18.0.1 - - [18/Aug/2019:09:34:05 +0000] "POST /tokens HTTP/1.1" 405 575 "http://127.0.0.1:3000/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36" "-"
```

同样，给出了405的http状态码。

----

在多次尝试无果后，开始分析问题，问题出在cli和ui都无法正常和nginx进行通信，也就无法和nginx代理的诸多服务进行连接。而官方文档却并未多作说明，是不是因为我修改了nginx端口，而其他服务和nginx建立连接的地方在代码中写死了是80端口呢？

尝试一下。

将`.env`文件中nginx的http端口改回80，执行`make run`，果不其然提示nginx的80端口被占用。这回我们选择杀掉占用该端口的进程！

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo netstat -lnp | grep 80
[sudo] password for eiger:
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1193/nginx: master
tcp6       0      0 :::8880                 :::*                    LISTEN      19939/docker-proxy
tcp6       0      0 :::80                   :::*                    LISTEN      1193/nginx: master
tcp6       0      0 :::8180                 :::*                    LISTEN      19576/docker-proxy
unix  2      [ ACC ]     SEQPACKET  LISTENING     13802    1/init               /run/udev/control
unix  2      [ ACC ]     STREAM     LISTENING     27808    1596/Xorg            /tmp/.X11-unix/X0
unix  2      [ ACC ]     STREAM     LISTENING     180757   8303/code            /run/user/1000/vscode-06f15111-1.37.0-main.sock
unix  2      [ ACC ]     STREAM     LISTENING     27807    1596/Xorg            @/tmp/.X11-unix/X0
unix  2      [ ACC ]     STREAM     LISTENING     184361   8973/code            /run/user/1000/vscode-git-askpass-830bbc748f92bec499b4d793b9a26bcc67f98049.sock
unix  2      [ ACC ]     STREAM     LISTENING     238009   19584/containerd-sh  @/containerd-shim/moby/fea1599f1fafb50ec78e979f461f77bb99350205c33c622d1841938e586582ab/shim.sock@
unix  2      [ ACC ]     STREAM     LISTENING     230354   18640/containerd-sh  @/conta
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo kill -9 1193
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ sudo netstat -lnp | grep 80
tcp6       0      0 :::8880                 :::*                    LISTEN      19939/docker-proxy
tcp6       0      0 :::8180                 :::*                    LISTEN      19576/docker-proxy
unix  2      [ ACC ]     SEQPACKET  LISTENING     13802    1/init               /run/udev/control
unix  2      [ ACC ]     STREAM     LISTENING     27808    1596/Xorg            /tmp/.X11-unix/X0
unix  2      [ ACC ]     STREAM     LISTENING     180757   8303/code            /run/user/1000/vscode-06f15111-1.37.0-main.sock
unix  2      [ ACC ]     STREAM     LISTENING     27807    1596/Xorg            @/tmp/.X11-unix/X0
unix  2      [ ACC ]     STREAM     LISTENING     184361   8973/code            /run/user/1000/vscode-git-askpass-830bbc748f92bec499b4d793b9a26bcc67f98049.sock
unix  2      [ ACC ]     STREAM     LISTENING     238009   19584/containerd-sh  @/containerd-shim/moby/fea1599f1fafb50ec78e979f461f77bb99350205c33c622d1841938e586582ab/shim.sock@
unix  2      [ ACC ]     STREAM     LISTENING     230354   18640/containerd-sh  @/containerd-shim/moby/aef24619a59af74650841b80deca2addcf0f325659ff88b46e7a415308615aee/shim.sock@
```

再执行`make run`这回全部服务就正常运行起来了。

现在我们需要分别试试mainflux-ui和mainflux-cli。

先试试mainflux-cli。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ mainflux-cli version

"0.9.0"

eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ mainflux-cli provision test

{
  "email": "distracted_goldberg@email.com",
  "password": "123"
}


"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYxNjkzNTMsImlhdCI6MTU2NjEzMzM1MywiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJkaXN0cmFjdGVkX2dvbGRiZXJnQGVtYWlsLmNvbSJ9.48juMD1G25swxXYHe_ydHAY5kUdOC76sF6TebxEIKw4"


[
  {
    "id": "472afd2e-eb93-4a35-a043-c766ae5bb340",
    "key": "f586a9d6-65b2-402a-991f-df3f589dde03",
    "name": "d0"
  },
  {
    "id": "b0963af4-9c86-4b1b-b66e-83225c3a61dc",
    "key": "4e7a2353-e190-474c-b870-09119540d07a",
    "name": "d1"
  }
]


[
  {
    "id": "56e9b2eb-1825-4405-9cfd-8f5da524bc5a",
    "name": "c0"
  },
  {
    "id": "ffd36101-2eca-4a1f-995e-40db52ae3546",
    "name": "c1"
  }
]
```

对应的mainflux系统输出：

```shell
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:50 +0000] "POST /users HTTP/1.1" 201 0 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method register for user pensive_pike@email.com took 97.212206ms to complete without errors.","ts":"2019-08-19T05:01:50.946048858Z"}
mainflux-users  | {"level":"info","message":"Method login for user pensive_pike@email.com took 91.421359ms to complete without errors.","ts":"2019-08-19T05:01:51.039202211Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "POST /tokens HTTP/1.1" 201 205 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 76.822µs to complete without errors.","ts":"2019-08-19T05:01:51.049014024Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "POST /things HTTP/1.1" 201 0 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 111.644µs to complete without errors.","ts":"2019-08-19T05:01:51.08432965Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "GET /things/c7d43640-0dcd-4906-ac6b-6e402977a5ae HTTP/1.1" 200 103 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 62.202µs to complete without errors.","ts":"2019-08-19T05:01:51.087101305Z"}
mainflux-things | {"level":"info","message":"Method add_thing for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4 and thing c7d43640-0dcd-4906-ac6b-6e402977a5ae took 25.942086ms to complete without errors.","ts":"2019-08-19T05:01:51.082656145Z"}
mainflux-things | {"level":"info","message":"Method view_thing for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4 and thing c7d43640-0dcd-4906-ac6b-6e402977a5ae took 1.893131ms to complete without errors.","ts":"2019-08-19T05:01:51.085790957Z"}
mainflux-es-redis | 1:M 19 Aug 2019 05:01:51.085 * 1 changes in 3600 seconds. Saving...
mainflux-es-redis | 1:M 19 Aug 2019 05:01:51.085 * Background saving started by pid 13
mainflux-things | {"level":"info","message":"Method add_thing for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4 and thing d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b took 13.657223ms to complete without errors.","ts":"2019-08-19T05:01:51.100444341Z"}
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 63.645µs to complete without errors.","ts":"2019-08-19T05:01:51.103297048Z"}
mainflux-es-redis | 13:C 19 Aug 2019 05:01:51.102 * DB saved on disk
mainflux-es-redis | 13:C 19 Aug 2019 05:01:51.102 * RDB: 0 MB of memory used by copy-on-write
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "POST /things HTTP/1.1" 201 0 "-" "Go-http-client/1.1"
mainflux-things | {"level":"info","message":"Method view_thing for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4 and thing d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b took 1.537343ms to complete without errors.","ts":"2019-08-19T05:01:51.104173072Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "GET /things/d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b HTTP/1.1" 200 103 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 68.712µs to complete without errors.","ts":"2019-08-19T05:01:51.108924879Z"}
mainflux-things | {"level":"info","message":"Method create_channel for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4 and channel  took 9.443113ms to complete without errors.","ts":"2019-08-19T05:01:51.118044827Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "POST /channels HTTP/1.1" 201 0 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 137.705µs to complete without errors.","ts":"2019-08-19T05:01:51.119680765Z"}
mainflux-things | {"level":"info","message":"Method create_channel for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4 and channel  took 2.605725ms to complete without errors.","ts":"2019-08-19T05:01:51.121788949Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "POST /channels HTTP/1.1" 201 0 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 70.85µs to complete without errors.","ts":"2019-08-19T05:01:51.129063142Z"}
mainflux-things | {"level":"info","message":"Method connect for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4, channel 3e307fb1-d41c-49d1-853f-bc48e0e2f734 and thing c7d43640-0dcd-4906-ac6b-6e402977a5ae took 15.118173ms to complete without errors.","ts":"2019-08-19T05:01:51.13813717Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "PUT /channels/3e307fb1-d41c-49d1-853f-bc48e0e2f734/things/c7d43640-0dcd-4906-ac6b-6e402977a5ae HTTP/1.1" 200 0 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 52.583µs to complete without errors.","ts":"2019-08-19T05:01:51.139484322Z"}
mainflux-things | {"level":"info","message":"Method connect for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4, channel af1045ac-fdc1-4091-b0ba-234ebe5e167b and thing c7d43640-0dcd-4906-ac6b-6e402977a5ae took 2.796857ms to complete without errors.","ts":"2019-08-19T05:01:51.141942135Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "PUT /channels/af1045ac-fdc1-4091-b0ba-234ebe5e167b/things/c7d43640-0dcd-4906-ac6b-6e402977a5ae HTTP/1.1" 200 0 "-" "Go-http-client/1.1"
mainflux-users  | {"level":"info","message":"Method identity for client pensive_pike@email.com took 52.562µs to complete without errors.","ts":"2019-08-19T05:01:51.143426123Z"}
mainflux-things | {"level":"info","message":"Method connect for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4, channel af1045ac-fdc1-4091-b0ba-234ebe5e167b and thing d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b took 2.297169ms to complete without errors.","ts":"2019-08-19T05:01:51.145410019Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:05:01:51 +0000] "PUT /channels/af1045ac-fdc1-4091-b0ba-234ebe5e167b/things/d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b HTTP/1.1" 200 0 "-" "Go-http-client/1.1"
mainflux-es-redis | 1:M 19 Aug 2019 05:01:51.185 * Background saving terminated with success
```

看上去成功了！

接下来将test随即注册的账号在mainflux-ui界面登录试试：

在127.0.0.1:3000页面下尝试登录前面随机生成的用户，依然返回405。

是不是要使用docker的虚拟ip呢？docker的虚拟ip默认为172.17.0.1，但是我们还是来手动查看一下。执行`ifconfig`，可以看到这样一段：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ ifconfig
br-1f6c96862db4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.0.0  broadcast 172.18.255.255
        inet6 fe80::42:aff:fe58:4f6  prefixlen 64  scopeid 0x20<link>
        ether 02:42:0a:58:04:f6  txqueuelen 0  (Ethernet)
        RX packets 1103  bytes 56812 (56.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1312  bytes 153384 (153.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:e5:24:90:7c  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

说明docker ip确实是172.17.0.1。现在我们试试浏览器访问172.17.0.1:3000：

<!--![mainflux-ui-03](/images/mainflux-ui-03.png)-->
![mainflux-ui-03](../../../static/images/mainflux-ui-03.png)

这时mainflux系统输出为：

```shell
mainflux-ui     | 172.18.0.1 - - [19/Aug/2019:05:21:10 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36" "-"
mainflux-ui     | 172.18.0.1 - - [19/Aug/2019:05:21:10 +0000] "GET /css/mainflux.css HTTP/1.1" 304 0 "http://172.17.0.1:3000/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36" "-"
mainflux-ui     | 172.18.0.1 - - [19/Aug/2019:05:21:10 +0000] "GET /main.js HTTP/1.1" 304 0 "http://172.17.0.1:3000/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36" "-"
mainflux-ui     | 172.18.0.1 - - [19/Aug/2019:05:21:10 +0000] "GET /src/Websocket.js HTTP/1.1" 304 0 "http://172.17.0.1:3000/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36" "-"
mainflux-ui     | 172.18.0.1 - - [19/Aug/2019:05:21:12 +0000] "GET /favicon.ico HTTP/1.1" 304 0 "http://172.17.0.1:3000/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36" "-"
```

这时注意到终端其实输出的ip为172.18.0.1，那么浏览器输入172.18.0.1:3000试试？

<!--![mainflux-ui-04](/images/mainflux-ui-04.png)-->
![mainflux-ui-04](../../../static/images/mainflux-ui-04.png)

也能行！

现在我们来试试在两个网址中与mainflux后端系统进行交互看是否能成功。我们准备了两个生成的用户（通过`mainflux-cli provision test`生成）：

```json
{
  "email": "distracted_goldberg@email.com",
  "password": "123"
}
{
  "email": "pensive_pike@email.com",
  "password": "123"
}
```

经过测试，浏览器输入以下链接均可进入mainflux-ui登录界面：

```shell
127.0.0.1
127.0.0.1:3000
172.17.0.1
172.17.0.1:3000
172.18.0.1
172.18.0.1:3000
```

但是只有输入

```shell
127.0.0.1
172.17.0.1
172.18.0.1
```

后的界面能正常登录。

其实从终端日志显示的`172.18.0.1`来看，应该是nginx里写好了解析规则，输入`127.0.0.1`或者`172.17.0.1`都会被解析到`172.18.0.1`。

这里放一下`172.18.0.1`的登陆成功页面：

<!--![mainflux-ui-06](/images/mainflux-ui-06.png)-->
![mainflux-ui-06](../../../static/images/mainflux-ui-06.png)

```shell
mainflux-users  | {"level":"info","message":"Method login for user distracted_goldberg@email.com took 92.773994ms to complete without errors.","ts":"2019-08-19T06:02:53.426102548Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:06:02:53 +0000] "POST /tokens HTTP/1.1" 201 214 "http://172.18.0.1/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:06:02:53 +0000] "GET /version HTTP/1.1" 200 38 "http://172.18.0.1/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"
mainflux-users  | {"level":"info","message":"Method identity for client distracted_goldberg@email.com took 84.523µs to complete without errors.","ts":"2019-08-19T06:02:53.433221746Z"}
mainflux-users  | {"level":"info","message":"Method identity for client distracted_goldberg@email.com took 70.071µs to complete without errors.","ts":"2019-08-19T06:02:53.434277438Z"}
mainflux-things | {"level":"info","message":"Method list_channels for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMzA1NzMsImlhdCI6MTU2NjE5NDU3MywiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJkaXN0cmFjdGVkX2dvbGRiZXJnQGVtYWlsLmNvbSJ9.lFhy2KPWE4jIkWvMMUxhDAsCqG-_cgHdqiI1e2vjj10 took 2.78473ms to complete without errors.","ts":"2019-08-19T06:02:53.435522551Z"}
mainflux-things | {"level":"info","message":"Method list_things for token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMzA1NzMsImlhdCI6MTU2NjE5NDU3MywiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJkaXN0cmFjdGVkX2dvbGRiZXJnQGVtYWlsLmNvbSJ9.lFhy2KPWE4jIkWvMMUxhDAsCqG-_cgHdqiI1e2vjj10 took 2.266344ms to complete without errors.","ts":"2019-08-19T06:02:53.436236164Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:06:02:53 +0000] "GET /channels?offset=0&limit=10 HTTP/1.1" 200 163 "http://172.18.0.1/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:06:02:53 +0000] "GET /things?offset=0&limit=10 HTTP/1.1" 200 251 "http://172.18.0.1/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"

```

(ps: 这里时间显示6:02，比本机系统时间快了4小时，可能是时区的设置问题，不影响我们的测试)

接下来我们试着在mainflux-ui界面注册一个用户：

```json
{
  "email": "374192922@qq.com",
  "password": "123456"
}
```

注册成功了，我们登录进去看看：

<!--![mainflux-ui-07](/images/mainflux-ui-07.png)-->
![mainflux-ui-07](../../../static/images/mainflux-ui-07.png)

是没有问题的。

### 12.4 发送消息测试

在上一节我们测试了系统的用户创建登录等，这一节将基于前边测试生成的一组账户数据进行设备的通信测试。

```json
{
  "email": "distracted_goldberg@email.com",
  "password": "123"
}


"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYxNjkzNTMsImlhdCI6MTU2NjEzMzM1MywiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJkaXN0cmFjdGVkX2dvbGRiZXJnQGVtYWlsLmNvbSJ9.48juMD1G25swxXYHe_ydHAY5kUdOC76sF6TebxEIKw4"


[
  {
    "id": "472afd2e-eb93-4a35-a043-c766ae5bb340",
    "key": "f586a9d6-65b2-402a-991f-df3f589dde03",
    "name": "d0"
  },
  {
    "id": "b0963af4-9c86-4b1b-b66e-83225c3a61dc",
    "key": "4e7a2353-e190-474c-b870-09119540d07a",
    "name": "d1"
  }
]


[
  {
    "id": "56e9b2eb-1825-4405-9cfd-8f5da524bc5a",
    "name": "c0"
  },
  {
    "id": "ffd36101-2eca-4a1f-995e-40db52ae3546",
    "name": "c1"
  }
]
```

先在mainflux-ui界面登录该账号，便于查看变化。

<!--![mainflux-ui-08](/images/mainflux-ui-08.png)-->
![mainflux-ui-08](../../../static/images/mainflux-ui-08.png)

现在我们可以看到是没有任何消息的。

当系统准备好（`provisioned`）之后，可以以下面类似的方式进行消息传递：

```shell
mainflux-cli messages send <channel_id> '[{"bn":"some-base-name:","bt":1.276020076001e+09, "bu":"A","bver":5, "n":"voltage","u":"V","v":120.1}, {"n":"current","t":-5,"v":1.2}, {"n":"current","t":-4,"v":1.3}]' <thing_key>
```

这里我在终端输入：

```shell
mainflux-cli messages send 56e9b2eb-1825-4405-9cfd-8f5da524bc5a '[{"bn":"some-base-name:","bt":1.276020076001e+09, "bu":"A","bver":5, "n":"voltage","u":"V","v":120.1}, {"n":"current","t":-5,"v":1.2}, {"n":"current","t":-4,"v":1.3}]' 472afd2e-eb93-4a35-a043-c766ae5bb340
```

`channel_id`使用的是上面的`c0`，`thing_id`使用的是上面的`d0`。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ mainflux-cli messages send 56e9b2eb-1825-4405-9cfd-8f5da524bc5a '[{"bn":"some-base-name:","bt":1.276020076001e+09, "bu":"A","bver":5, "n":"voltage","u":"V","v":120.1}, {"n":"current","t":-5,"v":1.2}, {"n":"current","t":-4,"v":1.3}]' 472afd2e-eb93-4a35-a043-c766ae5bb340

unauthorized access
```

而mainlfux系统的输出是：

```shell
mainflux-things | {"level":"warn","message":"Method can_access for channel 56e9b2eb-1825-4405-9cfd-8f5da524bc5a and thing  took 38.792069ms to complete with error: missing or invalid credentials provided.","ts":"2019-08-19T06:27:06.144145764Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:06:27:06 +0000] "POST /http/channels/56e9b2eb-1825-4405-9cfd-8f5da524bc5a/messages HTTP/1.1" 403 0 "-" "Go-http-client/1.1"
mainflux-http   | {"level":"warn","message":"Method publish to channel 56e9b2eb-1825-4405-9cfd-8f5da524bc5a took 60.581117ms to complete with error: rpc error: code = PermissionDenied desc = missing or invalid credentials provided.","ts":"2019-08-19T06:27:06.146926117Z"}
```

这里要说明下，我前后进行了两次`mainflux-cli provision test`，先后得到`dis_xxx`和`pen_xxx`两个user，使用`dis_xxx`用户进行发送消息测试时，发生了前面的错误。

再试试`pen_xxx`:

```json
{
  "email": "pensive_pike@email.com",
  "password": "123"
}


"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjYyMjY5MTEsImlhdCI6MTU2NjE5MDkxMSwiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJwZW5zaXZlX3Bpa2VAZW1haWwuY29tIn0.gt443-MvZP93VYzeIMn9tghlPHxpnYWOauhlGd6CLr4"


[
  {
    "id": "c7d43640-0dcd-4906-ac6b-6e402977a5ae",
    "key": "4a928b88-dc3f-4dd6-b8a0-71a72b8bfc49",
    "name": "d0"
  },
  {
    "id": "d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b",
    "key": "f8416f9d-4c88-4ffc-b3f9-3210db439494",
    "name": "d1"
  }
]


[
  {
    "id": "3e307fb1-d41c-49d1-853f-bc48e0e2f734",
    "name": "c0"
  },
  {
    "id": "af1045ac-fdc1-4091-b0ba-234ebe5e167b",
    "name": "c1"
  }
]

```

并在终端输入如下：

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ mainflux-cli messages send af1045ac-fdc1-4091-b0ba-234ebe5e167b '[{"bn":"some-base-name:","bt":1.276020076001e+09, "bu":"A","bver":5, "n":"voltage","u":"V","v":120.1}, {"n":"current","t":-5,"v":1.2}, {"n":"current","t":-4,"v":1.3}]' f8416f9d-4c88-4ffc-b3f9-3210db439494

ok
```

同时，mainflux系统终端输出如下：

```shell
mainflux-normalizer | {"level":"info","message":"Method normalize took 67.01µs to complete without errors.","ts":"2019-08-19T09:50:15.912996945Z"}
mainflux-http   | {"level":"info","message":"Method publish to channel af1045ac-fdc1-4091-b0ba-234ebe5e167b took 1.610837ms to complete without errors.","ts":"2019-08-19T09:50:15.912421876Z"}
mainflux-things | {"level":"info","message":"Method can_access for channel af1045ac-fdc1-4091-b0ba-234ebe5e167b and thing d3f98848-2c3f-4cb7-9b2d-83ee6cdcbd7b took 692.851µs to complete without errors.","ts":"2019-08-19T09:50:15.912035693Z"}
mainflux-nginx  | 172.18.0.1 - - [19/Aug/2019:09:50:15 +0000] "POST /http/channels/af1045ac-fdc1-4091-b0ba-234ebe5e167b/messages HTTP/1.1" 202 0 "-" "Go-http-client/1.1"
```

至此，mainflux官方文档Getting Startted一节完全跑通。

类比之下，我们前面在开发者文档里边所做的无非是编译了所有微服务程序，构建了必要的docker镜像及容器，只是nats等软件我们没有通过docker的方式安装而是真实安装在了我们的物理环境中。并不影响整个系统的运转，同样是没有问题的。

## 13. mainflux架构、概念

### 13.1 架构

mainflux架构如图：

<!--![mainflux-architeture](/images/mainflux-architecture.jpg)-->
![mainflux-architecture](../../../static/images/mainflux-architecture.jpg)

mainflux有三个主要的模型：user、thing、channel。user代表管理员，thing映射实际连接的设备，channel指一个通信频道，也可以理解为mqtt中的topic主题，连接到同一个channel的设备可以获取这个channel中的信息，也就是说实现了互相通信。

这里要注意两点：

- channel的物理实现是NATS消息系统的subject主题，nats基于channel_id来创建对应的主题名称。
- Normalizer负责将数据转换为SenML格式

### 13.2 Provisioning

`Provisioning`指的是物联网平台配置的过程，在mainflux中它指的是系统操作员创建配置user、channel、thing等实体的过程。

ps: 使用docker-compose.yml运行整个mainflux系统时，所有的服务都运行在nginx代理的后面，数据传输也都经过`TLS`加密。

#### 13.2.1 用户管理

- 创建user：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" https://localhost/users -d '{"email":"john.doe@email.com", "password":"123"}'
    ```

- 获取user授权密钥（authorization key）：

    为了让user能够管理整个系统，需要user创建授权令牌（token）。

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" https://localhost/tokens -d '{"email":"john.doe@email.com", "password":"123"}'
    ```

    响应形如：

    ```json
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MjMzODg0NzcsImlhdCI6MTUyMzM1MjQ3NywiaXNzIjoibWFpbmZsdXgiLCJzdWIiOiJqb2huLmRvZUBlbWFpbC5jb20ifQ.cygz9zoqD7Rd8f88hpQNilTCAS1DrLLgLg4PRcH-iAI"
    }
    ```

#### 13.2.2 系统配置（provision）

在进行这一步之前，确保已经创建了user并为其创建了授权秘钥。

- 配置thing

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" -H "Authorization: <user_auth_token>" https://localhost/things -d '{"name":"weio"}'
    ```

    `<user_auth_token>`用来指定thing归属于哪个user。

    响应形如：

    ```json
    HTTP/1.1 201 Created
    Content-Type: application/json
    Location: /things/81380742-7116-4f6f-9800-14fe464f6773
    Date: Tue, 10 Apr 2018 10:02:59 GMT
    Content-Length: 0
    ```

    响应中`Location`字段的值为所配置的thing的路径。

- 从数据库中读取并解析指定user配置的things

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -H "Authorization: <user_auth_token>" https://localhost/things
    ```

    响应形如：

    ```json
    HTTP/1.1 200 OK
    Content-Type: application/json
    Date: Tue, 10 Apr 2018 10:50:12 GMT
    Content-Length: 1105

    {
    "total": 2,
    "offset": 0,
    "limit": 10,
    "things": [
        {
        "id": "81380742-7116-4f6f-9800-14fe464f6773",
        "name": "weio",
        "key": "7aa91f7a-cbea-4fed-b427-07e029577590"
        },
        {
        "id": "cb63f852-2d48-44f0-a0cf-e450496c6c92",
        "name": "myapp",
        "key": "cbf02d60-72f2-4180-9f82-2c957db929d1"
        }
    ]
    }
    ```

    也可以指定`offset`（设置读取的数据条目间隔）和`limit`（设置读取的数据条目数）参数：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -H "Authorization: <user_auth_token>" https://localhost/things?offset=0&limit=5
    ```

    不指定`offset`和`limit`参数时其默认值分别为0和10，而且`limit`不可以超过100。

- 移除thing:

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X DELETE -H "Authorization: <user_auth_token>" https://localhost/things/<thing_id>
    ```

- 配置channel

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/json" -H "Authorization: <user_auth_token>" https://localhost/channels -d '{"name":"mychan"}'
    ```

    响应形如：

    ```json
    HTTP/1.1 201 Created
    Content-Type: application/json
    Location: /channels/19daa7a8-a489-4571-8714-ef1a214ed914
    Date: Tue, 10 Apr 2018 11:30:07 GMT
    Content-Length: 0
    ```

- 解析指定用户配置的channels：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -H "Authorization: <user_auth_token>" https://localhost/channels
    ```

    响应形如：

    ```json
    HTTP/1.1 200 OK
    Content-Type: application/json
    Date: Tue, 10 Apr 2018 11:38:06 GMT
    Content-Length: 139

    {
    "total": 1,
    "offset": 0,
    "limit": 10,
    "channels": [
        {
        "id": "19daa7a8-a489-4571-8714-ef1a214ed914",
        "name": "mychan"
        }
    ]
    }
    ```

    也可以指定`offset`和`limit`参数：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -H "Authorization: <user_auth_token>" https://localhost/channels?offset=0&limit=5
    ```

- 移除channel：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X DELETE -H "Authorization: <user_auth_token>" https://localhost/channels/<channel_id>
    ```

#### 13.2.3 访问控制

只有user能管理user名下的channel和thing!!!

- 将thing连接到channel上：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X PUT -H "Authorization: <user_auth_token>" https://localhost/channels/<channel_id>/things/<thing_id>
    ```

- 查看channel上有哪些thing连接

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -H "Authorization: <user_auth_token>" https://localhost/channels/<channel_id>/things
    ```

    得到的响应输出会是这样的：

    ```json
    {
        "total": 2,
        "offset": 0,
        "limit": 10,
        "things": [
            {
            "id": "3ffb3880-d1e6-4edd-acd9-4294d013f35b",
            "name": "d0",
            "key": "b1996995-237a-4552-94b2-83ec2e92a040",
            "metadata": "{}"
            },
            {
            "id": "94d166d6-6477-43dc-93b7-5c3707dbef1e",
            "name": "d1",
            "key": "e4588a68-6028-4740-9f12-c356796aebe8",
            "metadata": "{}"
            }
        ]
    }
    ```

- 也可以查看指定thing连接在了哪些channel上：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -H "Authorization: <user_auth_token>" https://localhost/things/<thing_id>/channels
    ```

    响应会像是这样的：

    ```json
    {
        "total": 2,
        "offset": 0,
        "limit": 10,
        "channels": [
            {
            "id": "5e62eb13-2695-4860-8d87-85b8a2f80fd4",
            "name": "c1",
            "metadata": "{}"
            },
            {
            "id": "c4b5e19a-7ffe-4172-b2c5-c8b9d570a165",
            "name": "c0",
            "metadata":"{}"
            }
        ]
    }
    ```

- 也可以取消某thing与某channel的连接：

    ```shell
    curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X DELETE -H "Authorization: <user_auth_token>" https://localhost/channels/<channel_id>/things/<thing_id>
    ```

ps: 除去涉及证书加密的部分，后边的url请求还是很容易懂的。

### 13.3 Messaging

网络通信协议处理的是客户端与nginx、nginx与网络协议适配服务的通信，在mainflux中有http、mqtt、websocket、coap四种。

#### 13.3.1 http

```shell
curl -s -S -i --cacert docker/ssl/certs/mainflux-server.crt --insecure -X POST -H "Content-Type: application/senml+json" -H "Authorization: <thing_token>" https://localhost/http/channels/<channel_id>/messages -d '[{"bn":"some-base-name:","bt":1.276020076001e+09, "bu":"A","bver":5, "n":"voltage","u":"V","v":120.1}, {"n":"current","t":-5,"v":1.2}, {"n":"current","t":-4,"v":1.3}]'
```

注意：如果使用SenML格式，所发送的数据需要是数组形式。（暂时不清楚是怎么样的数组）

#### 13.3.2 MQTT

为了通过mqtt发送消息，需要使用mosquitto工具<https://mosquitto.org/>。如果想使用基于websocket的mqtt，则需要使用paho<https://www.eclipse.org/paho/>。

- 在channel上发布消息（pub）：

    ```shell
    mosquitto_pub -u <thing_id> -P <thing_key> -t channels/<channel_id>/messages -h localhost -m '[{"bn":"some-base-name:","bt":1.276020076001e+09, "bu":"A","bver":5, "n":"voltage","u":"V","v":120.1}, {"n":"current","t":-5,"v":1.2}, {"n":"current","t":-4,"v":1.3}]'
    ```

- thing订阅channel：

    ```shell
    mosquitto_sub -u <thing_id> -P <thing_key> -t channels/<channel_id>/messages -h localhost
    ```

    In order to pass content type as part of topic, one should append it to the end of an existing topic. Content type value should always be prefixed with `/ct/`. If you want to use standard topic such as `channels/<channel_id>/messages` with SenML content type, you should use following topic `channels/<channel_id>/messages/ct/application_senml-json`. Characters like `_` and `-` in the content type will be replaced with `/` and `+` respectively.

    If you are using TLS to secure MQTT connection, add `--cafile docker/ssl/certs/ca.crt` to every command.

### 13.4 Storage

mainflux支持多种用于存储设备消息的数据库：cassandra/influxdb/mongodb

ps: 从docker-compose.yml文件可以看出，mainflux核心服务中使用了redis作为things、es、mqtt的缓存数据库，postgres作为users、things的数据库。和本节所讲的存储不同，本节主要指的是设备间通信`Message`的持久化存储方案。

这些存储`Storage`的安装需要通过docker-compose addons。

在`mainflux/docker/addons`目录下可以找到mainflux的非核心服务，其中就包括了storage的6个服务。

```shell
eiger@eiger-ThinkPad-X1-Carbon-3rd:~/gopath-default/src/github.com/mainflux/mainflux$ ls docker/addons
bootstrap  cassandra-reader  cassandra-writer  influxdb-reader  influxdb-writer  lora-adapter  mongodb-reader  mongodb-writer  postgres-reader  postgres-writer
```

注意，必须把核心服务先启动起来（`make run`）才能安装和启动这些插件服务。

#### 13.4.1 Writers

`Witers`包括cassandra-writer、influxdb-writer、mongodb-writer。`writer`负责使用、并且将SenML标准化后的消息存储到对应的数据库中。

`writer`会根据channels.toml中的配置来过滤对应channel的消息。如果想要监听所有channel的消息，channels.toml内容可以配置如下：

```toml
[channels]
filter = ["*"]
```

否则传入channels_id的列表。（这里还不确定传入的是会被过滤还是会被监听，这里有些含糊，有待后边去测试使用）

说得直白点是，writer运行起来后会监听channel的消息，并将消息进行持久化存储。

这里只介绍monggodb-writer，其他自行参考<https://mainflux.readthedocs.io/en/latest/storage/>

mongodb-writer安装：

```shell
docker-compose -f docker/addons/mongodb-writer/docker-compose.yml up -d
```

mongodb默认端口为27017，可以使用各种工具来进行数据库监视和数据可视化。

#### 13.4.2 Readers

`Readers`与`Writers`相对应，要想使用某个reader，必须先安装启用某个writer。reader负责从数据库中读取数据并将其从SenML格式解码，并且对外提供`HTTP API`用于消息访问或者说消息的使用。

同样的，这里也只介绍mongodb-reader。

安装和启动：

```shell
docker-compose -f docker/addons/mongodb-reader/docker-compose.yml up -d
```

mongodb-reader对外暴露8903端口来提供`HTTP API`<https://github.com/mainflux/mainflux/blob/master/readers/swagger.yml>以获取消息。

```shell
curl -s -S -i  -H "Authorization: <thing_token>" http://localhost:8904/channels/<channel_id>/messages
```

#### 13.4.2 Reader

### 13.5 LoRa

长范围通信协议。<https://mainflux.readthedocs.io/en/latest/lora/>

此节跳过。

### 13.6 安全通信

#### 13.6.1 安全的PostgreSQL连接

默认连接是不安全的连接。如果需要安全连接，可以选择`SSL`模式，并且设置相应的证书和秘钥文件的路径。

- `MF_USERS_DB_SSL_MODE` the SSL connection mode for Users.
- `MF_USERS_DB_SSL_CERT` the path to the certificate file for Users.
- `MF_USERS_DB_SSL_KEY` the path to the key file for Users.
- `MF_USERS_DB_SSL_ROOT_CERT` the path to the root certificate file for Users.

- `MF_THINGS_DB_SSL_MODE` the SSL connection mode for Things.
- `MF_THINGS_DB_SSL_CERT` the path to the certificate file for Things.
- `MF_THINGS_DB_SSL_KEY` the path to the key file for Things.
- `MF_THINGS_DB_SSL_ROOT_CERT` the path to the root certificate file for Things.

支持的数据库连接模式有: `disabled` (默认), `required`, `verify-ca` 和 `verify-full`。

#### 13.6.2 安全的gRPC

1. 服务器配置
    默认情况下gRPC通信是不安全的，因为mainflux通常运行在反向代理之后的私有网络中。

    但是可以配置其使用`TLS`加密传输

    - Users:
      - `MF_USERS_SERVER_CERT` the path to server certificate in pem format.
      - `MF_USERS_SERVER_KEY` the path to the server key in pem format.

    - Things:
      - `MF_THINGS_SERVER_CERT` the path to server certificate in pem format.
      - `MF_THINGS_SERVER_KEY` the path to the server key in pem format.

    注意：证书和秘钥必须两个都设置好才行，否则都是不安全的通信。
2. 客户端配置

    如果想为users和things提供安全的gRPC连接，必须定义可信的CA证书，而且不支持共同的身份验证（mutual certificate authentication）

    - Adapter适配器配置

        `MF_HTTP_ADAPTER_CA_CERTS`, `MF_MQTT_ADAPTER_CA_CERTS`, `MF_WS_ADAPTER_CA_CERTS`, `MF_COAP_ADAPTER_CA_CERTS` - the path to a file that contains the CAs in PEM format. If not set, the default connection will be insecure. If it fails to read the file, the adapter will fail to start up.

    - Things配置

        `MF_THINGS_CA_CERTS` - the path to a file that contains the CAs in PEM format. If not set, the default connection will be insecure. If it fails to read the file, the service will fail to start up.

### 13.7 Authentication（认证）

#### 13.7.1 使用Mainflux秘钥认证

默认情况下mainflux使用mainflux key进行认证。mainflux key指的是在thing创建时生成的一个秘钥。为了认证身份，thing在发送消息时需要加上它的这个秘钥。如何传递这个秘钥取决于所采用的传输协议。关于秘钥如何传递，应参考[Messaging](#133-messaging)。

注意当使用`docker-compose -f docker/docker-compose.yml up`启动mainflux时采用的就是这种认证方式。

#### 13.7.2 使用X.509证书进行相互TLS认证

<https://mainflux.readthedocs.io/en/latest/authentication/>

### 13.8 CLI

CLI指令很简单，之间看官方文档：
<https://mainflux.readthedocs.io/en/latest/cli/>

或者自己使用`mainflux-cli help`和`mainflux-cli [COMMAND] --help`查看帮助信息。

### 13.9 Bootstrap（引导）

Bootstrapping是指一个自启动过程，应该在没有外部输入的情况下继续进行。 Mainflux平台支持自举过程，但有些前提条件需要提前完成。设备可以在以下情况下触发引导：

- 设备仅包含引导凭证且没有Mainflux凭证
- 设备由于任何原因无法启动与配置的Mainflux服务（服务器无响应，身份验证失败等）的通信。
- 设备出于任何原因想要更新其配置

Bootstrapping和Provisioning的区别在于：

- Provisioning是由系统管理员进行的实体管理，包括user/thing/channel的创建、查看、删除与访问控制。
- Bootstrapping是实体配置，指的是device端管理员进行的的配置操作。包括创建channel/thing，修改thing的信息，使能thing等等。

参看官方文档：<https://mainflux.readthedocs.io/en/latest/bootstrap/>

## 14. mainflux应用

### 14.1 连接一个mqtt设备

大致思路：准备一个mqtt客户端，在mainflux系统为`374192922@qq.com`用户创建一个`channel`和一个`thing`，这个thing就对应着这个mqtt客户端
