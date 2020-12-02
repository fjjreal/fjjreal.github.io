---
title: "gRpc 入门"
date: 2020-11-23T15:40:02+08:00
description: "gRpc 入门"
cover: /images/default2.jpg
tags: [
    "go",
    "grpc",
]
categories: [
    "Go",
]
---

## 环境

```
win10
go 1.15 (mod 关闭) go env -w GO111MODULE=off
```

- protoc
- grpc包
- protoc-gen-go （包版本不对时切换git checkout tags）
- protoc生成go代码 （直接使用 src\google.golang.org\grpc\examples 也行）
- 启动server
- 启动client 调用 server
<!--more-->

## protoc

```
https://github.com/protocolbuffers/protobuf/releases
随便下载一个win版本的zip压缩包,解压将bin文件夹的protoc.exe放入GROOT的bin文件夹（或者其他地方，需要配置环境变量）
打开 cmd 输入 protoc --version正常显示版本信息即可
```

编译安装 [《Grpc+Grpc Gateway实践一 介绍与环境安装》](https://segmentfault.com/a/1190000013339403)

## grpc包

```
{{GOPATH}} : go env (GOPATH=C:\Users\***\go)
git clone https://github.com/grpc/grpc-go.git {{GOPATH}}/src/google.golang.org/grpc
```

## protoc-gen-go <font color=#FF000 ># proto3 2020-11-24</font>

```
git clone https://github.com/golang/protobuf.git  {{GOPATH}}/src/github.com/golang/protobuf
go install github.com/golang/protobuf/protoc-gen-go
如果报 找不到包错误 就 git reset --hard d23c5127

2020-11-24 注：
如果使用proto3 最好切换 tag v1.3.* 再 go install 生成 {{GOPATH}}/bin/protoc-gen-go.exe
cd {{GOPATH}}/src/github.com/golang/protobuf 
git checkout tags/v1.3.0 -b v1.3.0
cd protoc-gen-go 
go install

```

## protoc生成go代码 <font color=#FF000 ># proto3 2020-11-24</font>

在{{GOPATH}}/src 下新建grpc-learn项目,在其下新建helloworld文件夹里面放 helloworld.proto（{{GOPATH}}\src\google.golang.org\grpc\examples\helloworld\helloworld）

```
protoc --go_out=plubins=grpc:. helloworld.proto
helloworld 文件夹下生成 helloworld.pb.go
```

- 生成 proto4 代码 <font color=#FF000 ># 2020-11-24</font>

![protobuf](/images/protobuf_error.png)

protobuf.git 说明文档说该模块已被 google.golang.org/protobuf 代替
 * [https://github.com/protocolbuffers/protobuf-go](https://github.com/protocolbuffers/protobuf-go) 
 * [https://e.coding.net/robinqiwei/googleprotobuf.git](https://e.coding.net/robinqiwei/googleprotobuf.git)
```
clone google.golang.org/protobuf 到 {{GOPATH}}/src/google.golang.org/protobuf
cd cmd/protoc-gen-go
go install // 重新生成{{GOPATH}}/bin/protoc-gen-go.exe

helloworld.proto 的 proto3 改为 proto4
再去 protoc --go_out=plubins=grpc:. helloworld.proto 即可

```

## 启动server

将 {{GOPATH}}\src\google.golang.org\grpc\examples\helloworld中greeter_server\main.go重命名server.go放入grpc-learn（与helloworld文件夹同级）。import 中的pb需要修改为grpc-learn/helloworld

```
// 包不存在先下载
git clone https://github.com/golang/net.git {{GOPATH}}/src/golang.org/x/net
git clone https://github.com/golang/text.git {{GOPATH}}/src/golang.org/x/text
git clone https://github.com/google/go-genproto.git {{GOPATH}}/src/google.golang.org/genproto

git run server.go
```

- genproto ProtoPackageIsVersion4 error 2020-11-24 注：

[gitee的genproto日志](https://gitee.com/mirrors/go-genproto/commits/master/googleapis/rpc/status/status.pb.go)

![genproto](/images/genproto_error.png)

```
...google.golang.org\genproto\googleapis\rpc\status\status.pb.go:42:11: undefined: "github.com/golang/protobuf/proto".ProtoPack
ageIsVersion4

git reset --hard 3f1135a 

```

- grpc ProtoPackageIsVersion4 error 2020-11-24 注：

查到资料说 ProtoPackageIsVersion4 在 v1.26.x 后面版本才支持，切换分支为 v1.26.x 即可

```
...google.golang.org\grpc\binarylog\grpc_binarylog_v1\binarylog.pb.go:46:11: undefined: "github.com/golang/protobuf/proto".Prot
   oPackageIsVersion4

```


## 启动client

将 {{GOPATH}}\src\google.golang.org\grpc\examples\helloworld中greeter_client\main.go重命名client.go放入grpc-learn（与helloworld文件夹同级）。import 中的pb需要修改为grpc-learn/helloworld

```
go run client // 正常打印 hello world // server端显示receive world
go run client "hello_value" // hello hello_value // server端显示receive hello_value
```


## 膜拜大佬

- [煎鱼 - GO系列 - GRPC应用](https://eddycjy.com/go-categories/)
- [Grpc+Grpc Gateway实践一 介绍与环境安装](https://segmentfault.com/a/1190000013339403)
- [go gRPC初体验(win10+普通网络)](https://blog.csdn.net/u013536232/article/details/105395172/)
- [git 加速](https://gitee.com/killf/cgit)
