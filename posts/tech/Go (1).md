---
title : 'Go语言基础'
date : 2025-01-25T15:37:01+08:00
lastmod: 2025-01-25T15:37:01+08:00
description : "Go语言基础" 
image : img/cat.jpg
draft : false    
categories : ["Go"]
tags : ["GoLang"]
---


# Go语言基础

## 1. Go语言概述

- **高性能**：适合并发编程，支持高并发、高性能场景。
- **简洁语法**：Go的语法简洁，快速上手，去除了复杂的面向对象特性，保留了函数式编程的一些优势。
- **丰富标准库**：内建强大的标准库，支持网络、文件操作、并发等。
- **静态链接**：生成独立的可执行文件，无需依赖外部库，简化部署。
- **跨平台**：支持Linux、Windows、macOS等多个平台。
- **垃圾回收**：自动管理内存，减少开发人员的负担。

## 2. 环境搭建

- **Go安装**：[官方安装指南](https://golang.org/doc/install)
- **推荐IDE**：VSCode、GoLand，或者Gitpod等云开发环境。

### Go Module

**`go.mod`**：用于管理 Go 项目的依赖，记录模块路径、Go 版本和所依赖的包及其版本。

**`go.sum`**：用于记录每个依赖包的校验和，确保依赖包的完整性与安全性。

### 命令行工具

### 1、`go run` 

无需生成二进制文件，适合用于快速测试和调试

```bash
go run <filename.go> [arguments]
```

可以传递多个文件名来运行多个Go源文件

```bash
go run file1.go file2.go
```

### 2、`go build`

编译代码生成二进制文件的工具，编译原代码并生成可执行文件，可以在没有Go环境的机器上运行

```bash
go build <filename.go>
```

#### 自定义可执行文件名

```bash
go build -o myapp main.go
```

```bash
go build file1.go file2.go
```

#### 构建一个包

```bash
go build
```

### 3、`go install` 

与 `go build` 类似，区别在于 `go install` 会将生成的二进制文件安装到Go的`GOPATH`下（或者模块模式下会安装到`GOBIN`)。它通常用于将编译后的工具或应用程序安装到系统中。

```bash
go install <package>
```

#### 示例：

你可以使用 `go install` 安装一个命令行工具：

```bash
go install github.com/spf13/cobra@latest
```

会将 `cobra` 安装到你的 `GOBIN` 目录下，方便你在命令行中使用。

### 4、`go test` 

`go test` 是 Go 提供的测试工具，可以自动化运行Go的单元测试。

```bash
go test <package>
```

假设你有一个测试文件 `main_test.go`：

```bash
package main

import "testing"

func TestAdd(t *testing.T) {
    result := 1 + 2
    if result != 3 {
        t.Errorf("期望 3，实际是 %d", result)
    }
}
```

你可以运行 `go test` 来运行这个测试：

```bash
go test
```

#### 查看测试详细输出：

默认情况下，`go test` 会隐藏详细输出。如果你想查看详细信息，可以使用 `-v` 参数：

```bash
go test -v
```

#### 运行特定的测试函数：

如果你只想运行特定的测试函数，可以使用 `-run` 参数：

```bash
go test -run TestAdd
```

### 5、`go get`

`go get` 用于安装和更新 Go 包。如果你需要下载第三方库或者工具，可以使用 `go get`。

```bash
go get <package>
```

#### 示例：

下载并安装一个包：

```bash
go get github.com/spf13/cobra
```

如果你在模块模式下，`go get` 会自动更新 `go.mod` 文件。

#### 获取最新版本：

默认情况下，`go get` 会下载最新的稳定版本。如果你需要获取一个特定的版本，可以使用：

```bash
go get github.com/spf13/cobra@v1.2.3
```

### 6、`go mod`

Go 1.11 以后引入了模块化（Module）系统，`go mod` 是 Go 语言中用于管理依赖的工具。以下是常见的 `go mod` 命令：

#### 6.1 `go mod init`

初始化一个新的 Go 模块，在当前目录创建 `go.mod` 文件：

```bash
go mod init <module-name>
```

#### 6.2 `go mod tidy`

清理模块文件，删除未使用的依赖，并且确保所有需要的依赖都在 `go.mod` 和 `go.sum` 文件中：

```bash
go mod tidy
```

#### 6.3 `go mod vendor`

将所有依赖复制到本地 `vendor` 目录中，这对于某些需要离线构建的情况很有用：

```
go mod vendor
```

### 7、`go fmt` 与代码格式化

`go fmt` 是 Go 的代码格式化工具，它可以自动调整 Go 源代码的格式，使其符合 Go 的代码风格。通常在提交代码前运行，以保持代码风格一致。

#### 基本语法：

```bash
go fmt <filename.go>
```

#### 格式化整个包：

```bash
go fmt ./...
```

## 3. Go基础语法

### 3.1 Hello World

```go
package main // 定义包名，main包是Go程序的入口

import "fmt" // 引入fmt包，用于格式化输入输出

// main函数是Go程序的入口
func main() {
    fmt.Println("Hello, World!") // 输出Hello, World!
}
```

### 3.2 变量声明与初始化

#### 3.2.1 常规声明

```go
var a = "initial" // 声明变量a，并初始化为"initial"
var b, c int = 1, 2 // 同时声明b和c并初始化
var d = true // 自动推断d的类型为bool
```

#### 3.2.2 使用`:=`简洁声明

```go
e := 3.14 // :=用于声明并初始化变量，类型由编译器推断
```

#### 3.2.3 空白标识符

```go
var x, y int
_, err := os.Open("file.txt") // 通过空白标识符忽略某个返回值
```

### 3.3 控制结构

#### 3.3.1 if-else语句

```go
num := 9
if num < 0 {
    fmt.Println(num, "is negative") // 如果num小于0，输出负数
} else if num < 10 {
    fmt.Println(num, "has 1 digit") // 如果num小于10，输出1位数
} else {
    fmt.Println(num, "has multiple digits") // 否则输出多位数
}
```

#### 3.3.2 switch语句

```go
switch day := "Monday"; day {
case "Monday":
    fmt.Println("Start of the week")
case "Friday":
    fmt.Println("End of the week")
default:
    fmt.Println("Midweek") // 默认情况
}
```

#### 3.3.3 for循环

```go
// Go只有一个循环结构for，其他的如while都可以通过for实现
for i := 0; i < 3; i++ { // 从0到2的循环
    fmt.Println(i) // 输出循环的每个值
}
```

### 3.4 数组与切片

#### 3.4.1 数组

```go
var arr [5]int // 定义一个固定长度为5的数组
arr[0] = 10    // 给数组第一个元素赋值
fmt.Println(arr) // 输出整个数组
```

#### 3.4.2 切片

```go
// 切片是Go的一个灵活数据结构，不像数组大小固定
s := []string{"a", "b", "c"}
s = append(s, "d") // append函数动态扩展切片
fmt.Println(s) // 输出切片
```

#### 3.4.3 切片的创建

```go
s := make([]int, 5) // 创建一个长度为5的切片
fmt.Println(s) // 输出: [0 0 0 0 0]
```

### 3.5 函数

#### 3.5.1 基本函数

```go
func add(a int, b int) int { // 函数声明，返回a+b的结果
    return a + b
}

fmt.Println(add(1, 2)) // 调用函数并打印返回值
```

#### 3.5.2 多返回值

```go
func divide(a, b int) (int, int) {
    return a / b, a % b // 返回商和余数
}

quotient, remainder := divide(10, 3) // 解构返回值
fmt.Println(quotient, remainder)
```

#### 3.5.3 可变参数

```go
func sum(nums ...int) int { // nums是一个整数切片
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}
fmt.Println(sum(1, 2, 3, 4)) // 传入多个参数
```

### 3.6 结构体与方法

#### 3.6.1 结构体

```go
type User struct {
    Name     string
    Password string
}

u := User{"wang", "password123"} // 初始化结构体
fmt.Println(u)
```

#### 3.6.2 方法

```go
// 为User类型定义一个方法
func (u *User) ResetPassword(password string) {
    u.Password = password // 修改结构体实例的字段
}

u.ResetPassword("newpassword") // 调用方法修改Password字段
fmt.Println(u.Password)
```

### 3.7 错误处理

```go
func findUser(users []User, name string) (*User, error) {
    for _, u := range users {
        if u.Name == name {
            return &u, nil // 找到用户，返回指针和nil错误
        }
    }
    return nil, fmt.Errorf("user not found") // 没找到，返回错误
}

u, err := findUser([]User{{"wang", "1024"}}, "wang")
if err != nil {
    fmt.Println(err) // 打印错误
    return
}
fmt.Println(u.Name) // 如果没有错误，打印用户信息
```

### 3.8 字符串操作

#### 3.8.1 常用字符串函数

```go
import "strings"

str := "Hello, World!"
fmt.Println(strings.Contains(str, "World")) // 检查字符串是否包含"World"
fmt.Println(strings.ToUpper(str)) // 将字符串转换为大写
fmt.Println(strings.Split(str, ", ")) // 按指定分隔符切割字符串
```

### 3.9 JSON处理

#### 3.9.1 编码与解码

```go
import "encoding/json"

type UserInfo struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

a := UserInfo{Name: "wang", Age: 18}
buf, err := json.Marshal(a) // 将结构体编码为JSON
if err != nil {
    fmt.Println("Error encoding JSON:", err)
}
fmt.Println(string(buf)) // 输出JSON字符串

var b UserInfo
err = json.Unmarshal(buf, &b) // 将JSON字符串解码为结构体
if err != nil {
    fmt.Println("Error decoding JSON:", err)
}
fmt.Println(b)
```

### 3.10 时间与数字解析

#### 3.10.1 时间操作

```go
import "time"

t := time.Now() // 获取当前时间
fmt.Println(t.Format("2006-01-02 15:04:05")) // 格式化时间
```

#### 3.10.2 字符串转数字

```go
import "strconv"

str := "123"
n, err := strconv.Atoi(str) // 将字符串转为整数
if err != nil {
    fmt.Println("Error converting string to number:", err)
}
fmt.Println(n) // 输出123
```

## 4. Go工程实践

### 4.1 猜数字游戏

```go
import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UnixNano()) // 使用时间戳作为随机数种子
    secretNumber := rand.Intn(100)  // 生成一个0到99之间的随机数
    fmt.Println("Guess the secret number between 0 and 100!")
    var guess int
    for {
        fmt.Print("Enter your guess: ")
        fmt.Scan(&guess) // 读取用户输入的猜测值
        if guess < secretNumber {
            fmt.Println("Too low!")
        } else if guess > secretNumber {
            fmt.Println("Too high!")
        } else {
            fmt.Println("You guessed it!")
            break
        }
    }
}
```

### 4.2 在线词典

```go
import (
    "encoding/json"
    "fmt"
    "net/http"
)

func getDefinition(word string) {
    url := "https://api.dictionaryapi.dev
```



好的，以下是我为你准备的关于 **SOCKS5 代理服务器** 的 Go 语言代码的详细注释版，力求保持代码的完整性并加上详细的解释。

------

# SOCKS5 代理服务器代码实现

## 1. SOCKS5 协议概述

### 1.1 SOCKS5 代理原理

SOCKS5 是一种代理协议，在网络中充当客户端与目标服务器之间的中介，允许客户端通过代理服务器访问远程资源，通常用于规避防火墙、提高安全性等。

**代理流程分为四个阶段：**

1. **握手阶段**：客户端向 SOCKS5 代理发送协议版本号及认证方式。
2. **认证阶段**：根据代理服务器的返回，进行相应的身份验证。
3. **请求阶段**：认证通过后，客户端向 SOCKS5 代理发送请求，要求建立与目标服务器的连接。
4. **中继阶段**：代理服务器转发客户端请求和目标服务器的响应，完成数据传输。

------

## 2. TCP Echo 服务器

首先，我们需要实现一个简单的 TCP Echo 服务器，处理从客户端发送的数据，并返回相同的数据。

### 2.1 TCP Echo 服务器代码

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "net"
)

func main() {
    server, err := net.Listen("tcp", "127.0.0.1:1080") // 监听本地1080端口
    if err != nil {
        panic(err) // 如果监听失败，则程序崩溃
    }
    for {
        client, err := server.Accept() // 接受来自客户端的连接
        if err != nil {
            log.Printf("Accept failed %v", err)
            continue
        }
        go process(client) // 启动一个新的goroutine处理该连接
    }
}

// 处理连接的函数
func process(conn net.Conn) {
    defer conn.Close()  // 函数退出时关闭连接，避免资源泄漏
    reader := bufio.NewReader(conn) // 创建一个缓冲读取器，优化性能

    for {
        b, err := reader.ReadByte() // 读取单个字节
        if err != nil {
            break // 如果读取错误，则退出循环
        }
        _, err = conn.Write([]byte{b}) // 将字节写回客户端
        if err != nil {
            break // 如果写入错误，则退出循环
        }
    }
}
```

### 2.2 代码说明：

- **net.Listen**：在本地创建一个监听指定端口的服务器（这里是 `127.0.0.1:1080`）。
- **Accept**：接受客户端连接请求并返回一个连接对象。
- **process**：启动一个新的 goroutine 来处理客户端的请求。`bufio.NewReader` 用于提高从网络连接读取数据的效率。
- **conn.Write**：将客户端输入的字节数据发送回去，形成“echo”响应。

### 2.3 测试方法：

可以使用 `nc`（netcat）工具来测试：

```bash
nc 127.0.0.1 1080
```

在终端中输入任意字符，TCP Echo 服务器将返回相同的字符。

------

## 3. SOCKS5 代理认证实现

### 3.1 定义常量

```go
const socks5Ver = 0x05 // SOCKS5 协议版本
const cmdBind = 0x01   // 绑定命令
const atypIPV4 = 0x01  // IPv4 地址类型
const atypeHOST = 0x03 // 主机名类型
const atypeIPV6 = 0x04 // IPv6 地址类型
```

### 3.2 认证函数

```go
func auth(reader *bufio.Reader, conn net.Conn) (err error) {
    // 读取 SOCKS5 协议版本
    ver, err := reader.ReadByte()
    if err != nil {
        return fmt.Errorf("read ver failed:%w", err)
    }
    if ver != socks5Ver {
        return fmt.Errorf("not supported ver: %v", ver)
    }

    // 读取认证方法的数量
    methodSize, err := reader.ReadByte()
    if err != nil {
        return fmt.Errorf("read methodSize failed:%w", err)
    }

    // 读取所有认证方法
    method := make([]byte, methodSize)
    _, err = io.ReadFull(reader, method)
    if err != nil {
        return fmt.Errorf("read method failed:%w", err)
    }

    // 打印协议版本与认证方法
    log.Panicln("ver", ver, "method", method)

    // 返回不需要认证的响应
    _, err = conn.Write([]byte{socks5Ver, 0x00}) // 0x00 表示无需认证
    if err != nil {
        return fmt.Errorf("write failed:%w", err)
    }
    return nil
}
```

### 3.3 代码说明：

- **ReadByte**：逐字节读取协议中的数据，例如版本号和认证方法的数量。
- **io.ReadFull**：确保将指定长度的数据完全读取。
- **conn.Write**：向客户端返回响应，表示认证成功。

------

## 4. SOCKS5 请求阶段

在请求阶段，代理服务器接收到客户端发来的连接请求，并解析其中的目标地址和端口信息。

### 4.1 连接请求解析

```go
func connect(reader *bufio.Reader, conn net.Conn) (err error) {
    ver, err := reader.ReadByte() // 读取版本号
    if err != nil {
        return fmt.Errorf("read ver failed:%w", err)
    }

    cmd, err := reader.ReadByte() // 读取命令类型
    if err != nil {
        return fmt.Errorf("read cmd failed:%w", err)
    }

    rsv, err := reader.ReadByte() // 保留字段，忽略
    if err != nil {
        return fmt.Errorf("read rsv failed:%w", err)
    }

    atyp, err := reader.ReadByte() // 地址类型
    if err != nil {
        return fmt.Errorf("read atyp failed:%w", err)
    }

    var addr string
    switch atyp {
    case atypIPV4:
        addr = readIPV4(reader) // 读取IPv4地址
    case atypeHOST:
        addr = readHost(reader) // 读取主机名
    case atypeIPV6:
        addr = readIPV6(reader) // 读取IPv6地址
    }

    // 读取端口
    port := readPort(reader)

    log.Printf("Connecting to %s:%d", addr, port) // 打印目标地址和端口

    return nil
}
```

### 4.2 代码说明：

- **cmd**：检查请求类型，SOCKS5 代理服务器通常只处理 `CONNECT` 请求。
- **atyp**：根据目标地址类型（IPv4、主机名、IPv6）分别处理。
- **readIPV4/readHost/readIPV6/readPort**：读取不同地址类型的数据。

------

## 5. SOCKS5 中继阶段

在中继阶段，代理服务器通过双向数据转发来实现代理服务，直到数据传输结束或发生错误。

### 5.1 数据转发

```go
func relay(reader *bufio.Reader, conn net.Conn, targetConn net.Conn, ctx context.Context) {
    go io.Copy(targetConn, conn) // 从客户端读取数据并发送到目标服务器
    go io.Copy(conn, targetConn) // 从目标服务器读取数据并发送到客户端

    <-ctx.Done() // 等待任意一个数据转发完成或发生错误
}
```

### 5.2 代码说明：

- **io.Copy**：标准库提供的用于数据转发的函数。
- **ctx.Done()**：使用 `context` 来控制数据传输的终止条件。当 `context` 被取消时，数据传输终止。

------

## 6. 最终测试

测试时，可以通过在浏览器中设置 SOCKS5 代理，或者使用 `curl` 命令来进行请求：

```bash
curl --socks5 127.0.0.1:1080 http://example.com
```

在测试时，代理服务器将转发请求，最终浏览器或工具可以通过 SOCKS5 代理成功访问目标服务器。

------

