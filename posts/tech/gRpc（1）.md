---
title : 'gRpc（1）'
date : 2025-11-23T22:30:13+08:00
lastmod: 2025-11-23T22:20:13+08:00
description : "gRpc（1）" 
categories : ["Go"]
tags : ["Go", "RPC"]
---

# gRpc（1）

## 1. gRPC 是什么 & 基本概念

**gRPC** 是 Google 开源的高性能 RPC 框架，基于 HTTP/2 和 Protobuf。简单理解：

- **RPC**（远程过程调用）：像调用本地函数一样调用远程服务。
- **Protobuf**（Protocol Buffers）：一种高效、结构化的二进制序列化格式，用 `.proto` 文件定义消息和服务接口。

在一个典型 gRPC 项目中你会看到：

- `xxx.proto`：接口定义文件（核心）
- 通过 `protoc` 从 `.proto` 生成：
  - 服务端接口代码（server stub）
  - 客户端调用代码（client stub）
- 你只需要实现 **服务端逻辑**，客户端就可以像调用本地方法一样调用。

------

## 2. 环境准备

### 2.1 安装 protobuf 编译器 `protoc`

根据操作系统选择：

**macOS（Homebrew）：**

```bash
brew install protobuf
protoc --version  # 确认版本输出
```

**Ubuntu / Debian：**

bash

```bash
sudo apt-get update
sudo apt-get install -y protobuf-compiler
protoc --version
```

------

### 2.2 安装 Go（如果你用 Go）

略写步骤：

1. 去 https://go.dev/dl 下载并安装
2. 确认：

```bash
go version
```

------

### 2.3 安装 gRPC & Protobuf 插件（以 Go 为例）

```bash
# 安装 gRPC-Go
go get google.golang.org/grpc

# 安装 Go 版 protobuf 工具链
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

> 注意：`$GOPATH/bin` 要在你的 `PATH` 里（否则找不到 `protoc-gen-go` 命令）。

检查：

bash

```bash
protoc-gen-go --version
```

------

### 如果你用其他语言（只看你需要的部分）

#### Java

```bash
# Maven 依赖（pom.xml）
<dependencies>
  <dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-netty-shaded</artifactId>
    <version>1.68.0</version>
  </dependency>
  <dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-protobuf</artifactId>
    <version>1.68.0</version>
  </dependency>
  <dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-stub</artifactId>
    <version>1.68.0</version>
  </dependency>
</dependencies>
```

生成代码时使用 `protoc` + `protoc-gen-grpc-java`（可用 Maven 插件或手动）。

#### Node.js

```bash
npm init -y
npm install @grpc/grpc-js @grpc/proto-loader
```

#### Python

```bash
pip install grpcio grpcio-tools
```

------

## 3. 定义一个最简单的 gRPC 服务（`.proto` 文件）

我们做一个**打招呼服务 Greeter**：

- 服务：`Greeter`
- 请求：`HelloRequest`（包含 `name`）
- 响应：`HelloReply`（包含 `message`）

创建项目目录（以 Go 为例）：

```bash
mkdir grpc-demo
cd grpc-demo
mkdir proto
```

在 `proto/helloworld.proto` 中写入：

```protobuf
syntax = "proto3";

package helloworld;

// Go 语言特有选项：指定生成的 Go 包路径
option go_package = "grpc-demo/proto;helloworld";

// 定义服务
service Greeter {
  // 一个简单的 RPC
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// 请求消息
message HelloRequest {
  string name = 1;
}

// 响应消息
message HelloReply {
  string message = 1;
}
```

解释几个关键点：

- `syntax = "proto3";`：使用 proto3 语法（推荐）。
- `package helloworld;`：protobuf 内部包名。
- `option go_package = "grpc-demo/proto;helloworld";`
  - `grpc-demo/proto`：Go 模块路径（确保与你 go module 一致）
  - `helloworld`：Go 包名
- `rpc SayHello (HelloRequest) returns (HelloReply)`：定义 RPC 方法。

> 如果你已经有 Go module，可以先 `go mod init your/module/path`，然后 `go_package` 用你的真实路径。

------

## 4. 使用 `protoc` 生成代码

先初始化 Go 模块（在项目根目录 `grpc-demo`）：

```bash
go mod init grpc-demo
go mod tidy
```

### 4.1 生成 Go 代码

在项目根目录执行：

```bash
protoc \
  --go_out=. \
  --go-grpc_out=. \
  proto/helloworld.proto
```

成功后会生成两个文件：

- `proto/helloworld.pb.go`：消息结构、序列化等
- `proto/helloworld_grpc.pb.go`：gRPC 服务接口、客户端 stub 等

你可以简单看一下 `proto/helloworld_grpc.pb.go`，里面会有类似：

```go
type GreeterServer interface {
    SayHello(context.Context, *HelloRequest) (*HelloReply, error)
}
```

说明：你只需要实现这个接口。

------

## 5. 实现服务端（Go）

新建目录：

```bash
mkdir server
```

创建文件 `server/main.go`：

```go
package main

import (
    "context"
    "log"
    "net"

    "google.golang.org/grpc"

    // 注意：这里的路径要与你的 module 和 go_package 对齐
    "grpc-demo/proto" // 生成的代码包名（helloworld）
)

// 实现 GreeterServer 接口
type greeterServer struct {
    proto.UnimplementedGreeterServer
}

// 实现 SayHello 方法
func (s *greeterServer) SayHello(ctx context.Context, req *proto.HelloRequest) (*proto.HelloReply, error) {
    log.Printf("Received request: name=%s", req.GetName())
    return &proto.HelloReply{
        Message: "Hello, " + req.GetName(),
    }, nil
}

func main() {
    // 1. 监听端口
    lis, err := net.Listen("tcp", ":50051")
    if err != nil {
        log.Fatalf("failed to listen: %v", err)
    }

    // 2. 创建 gRPC server
    grpcServer := grpc.NewServer()

    // 3. 注册服务
    proto.RegisterGreeterServer(grpcServer, &greeterServer{})

    log.Println("gRPC server listening on :50051")
    // 4. 启动服务
    if err := grpcServer.Serve(lis); err != nil {
        log.Fatalf("failed to serve: %v", err)
    }
}
```

结构说明：

- `greeterServer` 结构体实现 `GreeterServer` 接口。
- 在 `main` 中：
  - `net.Listen("tcp", ":50051")`：监听 50051 端口（gRPC 常用端口之一）。
  - `grpc.NewServer()`：创建 gRPC 服务器。
  - `RegisterGreeterServer`：将你的实现注册到 gRPC 服务器。
  - `Serve(lis)`：阻塞式运行服务。

------

## 6. 实现客户端（Go）

新建目录：

```bash
mkdir client
```

创建文件 `client/main.go`：

```go
package main

import (
    "context"
    "log"
    "time"

    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials/insecure"

    "grpc-demo/proto"
)

func main() {
    // 1. 连接 gRPC 服务端
    conn, err := grpc.Dial(
        "localhost:50051",
        grpc.WithTransportCredentials(insecure.NewCredentials()), // demo 使用非 TLS
    )
    if err != nil {
        log.Fatalf("did not connect: %v", err)
    }
    defer conn.Close()

    // 2. 创建客户端 stub
    c := proto.NewGreeterClient(conn)

    // 3. 设置上下文和超时
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    defer cancel()

    // 4. 发起 RPC 调用
    r, err := c.SayHello(ctx, &proto.HelloRequest{Name: "World"})
    if err != nil {
        log.Fatalf("could not greet: %v", err)
    }

    // 5. 打印响应
    log.Printf("Greeting: %s", r.GetMessage())
}
```

说明：

- `grpc.Dial` 连接远程服务：
  - demo 使用 `WithTransportCredentials(insecure.NewCredentials())`，**生产环境要用 TLS**。
- `NewGreeterClient`：从生成的代码中创建客户端对象。
- `SayHello` 就像本地函数调用一样。

------

## 7. 运行 Demo

### 7.1 启动服务端

在项目根目录：

```bash
go run ./server
```

看到日志类似：

```text
2025/11/18 10:00:00 gRPC server listening on :50051
```

说明服务端启动成功。

### 7.2 启动客户端

新开一个终端，在项目根目录：

```bash
go run ./client
```

你应该看到：

```text
2025/11/18 10:00:05 Greeting: Hello, World
```

服务端窗口会打印：

```text
2025/11/18 10:00:05 Received request: name=World
```

------

## 拓展

实现：

1. **服务端流（Server Streaming）**
2. **客户端流（Client Streaming）**
3. **双向流（Bidirectional Streaming）**

### helloworld.proto

```protobuf
  // 1. 服务端流 (Server streaming)
  rpc LotsOfReplies(HelloRequest) returns (stream HelloReply) {}

  // 2. 客户端流 (Client streaming)
  rpc LotsOfGreetings(stream HelloRequest) returns (HelloReply) {}

  // 3. 双向流 (Bidirectional streaming)
  rpc BidiHello(stream HelloRequest) returns (stream HelloReply) {}
```

### client.go

```go
package main

import (
	"context"
	"io"
	"log"
	"time"

	helloworld "gRpcDemo/proto"
	"google.golang.org/grpc"
)

func main() {
	conn, err := grpc.Dial("localhost:50051", grpc.WithInsecure()) // prod 请使用 TLS
	if err != nil {
		log.Fatalf("dial: %v", err)
	}
	defer conn.Close()
	client := helloworld.NewGreeterClient(conn)

	// 1. 服务端流调用
	serverStreamExample(client)

	// 2. 客户端流调用
	clientStreamExample(client)

	// 3. 双向流调用
	bidiStreamExample(client)
}

// 服务端流客户端：接收多条回复
func serverStreamExample(client helloworld.GreeterClient) {
	log.Println("=== server stream example ===")
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	stream, err := client.LotsOfReplies(ctx, &helloworld.HelloRequest{Name: "kennem"})
	if err != nil {
		log.Printf("LotsOfReplies error: %v", err)
		return
	}
	for {
		resp, err := stream.Recv()
		if err == io.EOF {
			log.Println("server stream ended")
			break
		}
		if err != nil {
			log.Printf("recv error: %v", err)
			break
		}
		log.Printf("Recv: %s", resp.GetMessage())
	}
}

// 客户端流客户端：持续发送多条请求，最后接收一次回复
func clientStreamExample(client helloworld.GreeterClient) {
	log.Println("=== client stream example ===")
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	stream, err := client.LotsOfGreetings(ctx)
	if err != nil {
		log.Printf("LotsOfGreetings error: %v", err)
		return
	}

	names := []string{"alice", "bob", "carol"}
	for _, n := range names {
		if err := stream.Send(&helloworld.HelloRequest{Name: n}); err != nil {
			log.Printf("send error: %v", err)
			return
		}
		time.Sleep(200 * time.Millisecond)
	}
	// 关闭发送流并接收服务端的汇总回复
	reply, err := stream.CloseAndRecv()
	if err != nil {
		log.Printf("CloseAndRecv error: %v", err)
		return
	}
	log.Printf("Got reply: %s", reply.GetMessage())
}

// 双向流客户端：并行发送和接收
func bidiStreamExample(client helloworld.GreeterClient) {
	log.Println("=== bidi stream example ===")
	ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()

	stream, err := client.BidiHello(ctx)
	if err != nil {
		log.Printf("BidiHello error: %v", err)
		return
	}

	// 并行接收
	done := make(chan struct{})
	go func() {
		for {
			in, err := stream.Recv()
			if err == io.EOF {
				log.Println("bidi recv EOF")
				close(done)
				return
			}
			if err != nil {
				log.Printf("bidi recv error: %v", err)
				close(done)
				return
			}
			log.Printf("Got from server: %s", in.GetMessage())
		}
	}()

	// 发送几条消息
	names := []string{"x", "y", "z"}
	for _, n := range names {
		if err := stream.Send(&helloworld.HelloRequest{Name: n}); err != nil {
			log.Printf("bidi send error: %v", err)
			break
		}
		time.Sleep(300 * time.Millisecond)
	}
	// 发送完毕，关闭发送方向（等待接收协程结束）
	if err := stream.CloseSend(); err != nil {
		log.Printf("CloseSend error: %v", err)
	}
	<-done
	log.Println("bidi example done")
}

```

```bash
ken@Ken:/mnt/d/goland/gRpcDemo$ go run ./client/                                         │ken@Ken:/mnt/d/goland/gRpcDemo$ go run ./server/
2025/11/23 22:16:08 === server stream example ===                                        │2025/11/23 22:16:02 gRPC server listening :50051
2025/11/23 22:16:08 Recv: Reply 1 to kennem                                              │2025/11/23 22:16:08 [LotsOfReplies] start name=kennem
2025/11/23 22:16:08 Recv: Reply 2 to kennem                                              │2025/11/23 22:16:10 [LotsOfReplies] done
2025/11/23 22:16:09 Recv: Reply 3 to kennem                                              │2025/11/23 22:16:10 [LotsOfGreetings] start
2025/11/23 22:16:09 Recv: Reply 4 to kennem                                              │2025/11/23 22:16:10 [LotsOfGreetings] got alice
2025/11/23 22:16:10 Recv: Reply 5 to kennem                                              │2025/11/23 22:16:10 [LotsOfGreetings] got bob
2025/11/23 22:16:10 server stream ended                                                  │2025/11/23 22:16:10 [LotsOfGreetings] got carol
2025/11/23 22:16:10 === client stream example ===                                        │2025/11/23 22:16:11 [BidiHello] start
2025/11/23 22:16:11 Got reply: Received 3 greetings: [alice bob carol]                   │2025/11/23 22:16:12 [BidiHello] client closed send
2025/11/23 22:16:11 === bidi stream example ===                                          │
2025/11/23 22:16:11 Got from server: Hi x (from server)                                  │
2025/11/23 22:16:11 Got from server: Hi y (from server)                                  │
2025/11/23 22:16:11 Got from server: Hi z (from server)                                  │
2025/11/23 22:16:12 bidi recv EOF                                                        │
2025/11/23 22:16:12 bidi example done                                                    │
```

### server.go

```go
package main

import (
	"context"
	"fmt"
	"io"
	"log"
	"net"
	"time"

	helloworld "gRpcDemo/proto"
	"google.golang.org/grpc"
)

// server struct embedding generated UnimplementedGreeterServer
type greeterServer struct {
	helloworld.UnimplementedGreeterServer
}

// 1. 原始单次调用（保留）
func (s *greeterServer) SayHello(ctx context.Context, req *helloworld.HelloRequest) (*helloworld.HelloReply, error) {
	log.Printf("[SayHello] name=%s", req.GetName())
	return &helloworld.HelloReply{Message: "Hello, " + req.GetName()}, nil
}

// 2. 服务端流实现：对一个请求，返回多个响应（stream.Send）
func (s *greeterServer) LotsOfReplies(req *helloworld.HelloRequest, stream helloworld.Greeter_LotsOfRepliesServer) error {
	log.Printf("[LotsOfReplies] start name=%s", req.GetName())
	// 示例：发送 5 条回复，每条间隔 500ms
	for i := 0; i < 5; i++ {
		// 检查上下文是否被取消（客户端断开 / 超时）
		if err := stream.Context().Err(); err != nil {
			log.Printf("[LotsOfReplies] context error: %v", err)
			return err
		}

		resp := &helloworld.HelloReply{
			Message: fmt.Sprintf("Reply %d to %s", i+1, req.GetName()),
		}
		if err := stream.Send(resp); err != nil {
			log.Printf("[LotsOfReplies] send error: %v", err)
			return err
		}
		time.Sleep(500 * time.Millisecond)
	}
	log.Printf("[LotsOfReplies] done")
	return nil
}

// 3. 客户端流实现：客户端持续发送请求，服务端在结束后返回一次汇总
func (s *greeterServer) LotsOfGreetings(stream helloworld.Greeter_LotsOfGreetingsServer) error {
	log.Println("[LotsOfGreetings] start")
	count := 0
	names := []string{}
	for {
		// 接收客户端发送的请求
		req, err := stream.Recv()
		if err == io.EOF {
			// 客户端发送完毕，返回汇总
			reply := &helloworld.HelloReply{
				Message: fmt.Sprintf("Received %d greetings: %v", count, names),
			}
			return stream.SendAndClose(reply)
		}
		if err != nil {
			log.Printf("[LotsOfGreetings] recv error: %v", err)
			return err
		}
		// 检查取消
		if err := stream.Context().Err(); err != nil {
			log.Printf("[LotsOfGreetings] context error: %v", err)
			return err
		}
		count++
		names = append(names, req.GetName())
		log.Printf("[LotsOfGreetings] got %s", req.GetName())
	}
}

// 4. 双向流实现：服务端和客户端并行收发（Send/Recv 可同时进行）
func (s *greeterServer) BidiHello(stream helloworld.Greeter_BidiHelloServer) error {
	log.Println("[BidiHello] start")
	// 一个常见模式：启动接收 goroutine，同时做发送，或按轮询循环接收后马上发送
	recvCh := make(chan *helloworld.HelloRequest)
	errCh := make(chan error, 1)

	// 接收协程
	go func() {
		defer close(recvCh)
		for {
			req, err := stream.Recv()
			if err == io.EOF {
				// 客户端关闭发送端
				return
			}
			if err != nil {
				errCh <- err
				return
			}
			recvCh <- req
		}
	}()

	// 处理接收并即时回复
	for {
		select {
		case <-stream.Context().Done():
			// 客户端取消或超时
			log.Println("[BidiHello] context done:", stream.Context().Err())
			return stream.Context().Err()
		case err := <-errCh:
			return err
		case req, ok := <-recvCh:
			if !ok {
				// recvCh 关闭，表示客户端已关闭发送，结束
				log.Println("[BidiHello] client closed send")
				return nil
			}
			// 处理并回复
			resp := &helloworld.HelloReply{
				Message: "Hi " + req.GetName() + " (from server)",
			}
			if err := stream.Send(resp); err != nil {
				return err
			}
		}
	}
}

func main() {
	lis, err := net.Listen("tcp", ":50051")
	if err != nil {
		log.Fatalf("listen: %v", err)
	}
	grpcServer := grpc.NewServer()
	helloworld.RegisterGreeterServer(grpcServer, &greeterServer{})
	log.Println("gRPC server listening :50051")
	if err := grpcServer.Serve(lis); err != nil {
		log.Fatalf("serve: %v", err)
	}
}

```

```bash
ken@Ken:/mnt/d/goland/gRpcDemo$ go run ./server/
2025/11/23 22:16:08 === server stream example ===                                        │2025/11/23 22:16:02 gRPC server listening :50051
2025/11/23 22:16:08 Recv: Reply 1 to kennem                                              │2025/11/23 22:16:08 [LotsOfReplies] start name=kennem
2025/11/23 22:16:08 Recv: Reply 2 to kennem                                              │2025/11/23 22:16:10 [LotsOfReplies] done
2025/11/23 22:16:09 Recv: Reply 3 to kennem                                              │2025/11/23 22:16:10 [LotsOfGreetings] start
2025/11/23 22:16:09 Recv: Reply 4 to kennem                                              │2025/11/23 22:16:10 [LotsOfGreetings] got alice
2025/11/23 22:16:10 Recv: Reply 5 to kennem                                              │2025/11/23 22:16:10 [LotsOfGreetings] got bob
2025/11/23 22:16:10 server stream ended                                                  │2025/11/23 22:16:10 [LotsOfGreetings] got carol
2025/11/23 22:16:10 === client stream example ===                                        │2025/11/23 22:16:11 [BidiHello] start
2025/11/23 22:16:11 Got reply: Received 3 greetings: [alice bob carol]                   │2025/11/23 22:16:12 [BidiHello] client closed send
```

![image-20251123221752298](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20251123221752298.png)
