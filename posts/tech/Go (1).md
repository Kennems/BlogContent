---
title : 'Go语言基础'
date : 2025-01-25T10:00:01+08:00
lastmod: 2025-11-02T10:00:01+08:00
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
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"os"
)

type DictRequest struct {
	TransType string `json:"trans_type"`
	Source    string `json:"source"`
	UserID    string `json:"user_id"`
}

type DictResponse struct {
	Rc   int `json:"rc"`
	Wiki struct {
		KnownInLaguages int `json:"known_in_laguages"`
		Description     struct {
			Source string      `json:"source"`
			Target interface{} `json:"target"`
		} `json:"description"`
		ID   string `json:"id"`
		Item struct {
			Source string `json:"source"`
			Target string `json:"target"`
		} `json:"item"`
		ImageURL  string `json:"image_url"`
		IsSubject string `json:"is_subject"`
		Sitelink  string `json:"sitelink"`
	} `json:"wiki"`
	Dictionary struct {
		Prons struct {
			EnUs string `json:"en-us"`
			En   string `json:"en"`
		} `json:"prons"`
		Explanations []string      `json:"explanations"`
		Synonym      []string      `json:"synonym"`
		Antonym      []string      `json:"antonym"`
		WqxExample   [][]string    `json:"wqx_example"`
		Entry        string        `json:"entry"`
		Type         string        `json:"type"`
		Related      []interface{} `json:"related"`
		Source       string        `json:"source"`
	} `json:"dictionary"`
}

func query(word string) {
	client := &http.Client{}
	request := DictRequest{TransType: "en2zh", Source: word}
	buf, err := json.Marshal(request)
	if err != nil {
		log.Fatal(err)
	}
	var data = bytes.NewReader(buf)
	req, err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict", data)
	if err != nil {
		log.Fatal(err)
	}
	req.Header.Set("Connection", "keep-alive")
	req.Header.Set("DNT", "1")
	req.Header.Set("os-version", "")
	req.Header.Set("sec-ch-ua-mobile", "?0")
	req.Header.Set("User-Agent", "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.51 Safari/537.36")
	req.Header.Set("app-name", "xy")
	req.Header.Set("Content-Type", "application/json;charset=UTF-8")
	req.Header.Set("Accept", "application/json, text/plain, */*")
	req.Header.Set("device-id", "")
	req.Header.Set("os-type", "web")
	req.Header.Set("X-Authorization", "token:xxx")
	req.Header.Set("Origin", "https://fanyi.caiyunapp.com")
	req.Header.Set("Sec-Fetch-Site", "cross-site")
	req.Header.Set("Sec-Fetch-Mode", "cors")
	req.Header.Set("Sec-Fetch-Dest", "empty")
	req.Header.Set("Referer", "https://fanyi.caiyunapp.com/")
	req.Header.Set("Accept-Language", "zh-CN,zh;q=0.9")
	req.Header.Set("Cookie", "_ym_uid=16456948721020430059; _ym_d=1645694872")
	resp, err := client.Do(req)
	if err != nil {
		log.Fatal(err)
	}
	defer resp.Body.Close()
	bodyText, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		log.Fatal(err)
	}
	if resp.StatusCode != 200 {
		log.Fatal("bad StatusCode:", resp.StatusCode, "body", string(bodyText))
	}
	var dictResponse DictResponse
	err = json.Unmarshal(bodyText, &dictResponse)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(word, "UK:", dictResponse.Dictionary.Prons.En, "US:", dictResponse.Dictionary.Prons.EnUs)
	for _, item := range dictResponse.Dictionary.Explanations {
		fmt.Println(item)
	}
}

func main() {
	if len(os.Args) != 2 {
		fmt.Fprintf(os.Stderr, `usage: simpleDict WORD
example: simpleDict hello
		`)
		os.Exit(1)
	}
	word := os.Args[1]
	query(word)
}
```

```shell
ken@Ken:/mnt/d/Go/go-demo/02-Go语言的实战案例/02-simpledict/v4$ ./simpledict hello
hello UK: [ˈheˈləu] US: [həˈlo]
int.喂;哈罗
n.引人注意的呼声
v.向人呼(喂)
```

------

### 4.3 SOCKS5 代理服务器代码实现

#### 1. SOCKS5 协议概述

##### 1.1 SOCKS5 代理原理

SOCKS5 是一种代理协议，在网络中充当客户端与目标服务器之间的中介，允许客户端通过代理服务器访问远程资源，通常用于规避防火墙、提高安全性等。

**代理流程分为四个阶段：**

1. **握手阶段**：客户端向 SOCKS5 代理发送协议版本号及认证方式。
2. **认证阶段**：根据代理服务器的返回，进行相应的身份验证。
3. **请求阶段**：认证通过后，客户端向 SOCKS5 代理发送请求，要求建立与目标服务器的连接。
4. **中继阶段**：代理服务器转发客户端请求和目标服务器的响应，完成数据传输。

------

#### 2. TCP Echo 服务器

首先，我们需要实现一个简单的 TCP Echo 服务器，处理从客户端发送的数据，并返回相同的数据。

##### 2.1 TCP Echo 服务器代码

```go
package main

import (
	"bufio"
	"fmt"
	"net"
)

func main() {
	server, err := net.Listen("tcp", "127.0.0.1:1080")
	if err != nil {
		panic(err)
	}

	for {
		client, err := server.Accept()
		if err != nil {
			fmt.Errorf("accept error: %w", err)
			continue
		}
		go process(client)
	}
}

func process(conn net.Conn) {
	defer conn.Close()
	reader := bufio.NewReader(conn)

	for {
		buf, err := reader.ReadByte()
		if err != nil {
			fmt.Errorf("read buf error: %w", err)
			return
		}
		_, err = conn.Write([]byte{buf})
		if err != nil {
			fmt.Errorf("write buf back error: %w", err)
			return
		}
	}
}

```

##### 2.2 代码说明：

- **net.Listen**：在本地创建一个监听指定端口的服务器（这里是 `127.0.0.1:1080`）。
- **Accept**：接受客户端连接请求并返回一个连接对象。
- **process**：启动一个新的 goroutine 来处理客户端的请求。`bufio.NewReader` 用于提高从网络连接读取数据的效率。
- **conn.Write**：将客户端输入的字节数据发送回去，形成“echo”响应。

##### 2.3 测试方法：

可以使用 `nc`（netcat）工具来测试：

```bash
nc 127.0.0.1 1080
```

在终端中输入任意字符，TCP Echo 服务器将返回相同的字符。

------

#### 3. SOCKS5 代理认证实现

##### 3.1 代码

```go
package main

import (
	"bufio"
	"context"
	"fmt"
	"io"
	"log"
	"net"
	"time"
)

const (
	socks5Ver  = 0x05
	cmdConnect = 0x01
	atypeIPV4  = 0x01
	atypeHOST  = 0x03
	atypeIPV6  = 0x04
)

func main() {
	addr := "127.0.0.1:1080"
	listener, err := net.Listen("tcp", addr)
	if err != nil {
		log.Fatalf("listen failed: %v", err)
	}
	defer listener.Close()

	log.Println("SOCKS5 server listening on", addr)

	for {
		client, err := listener.Accept()
		if err != nil {
			log.Println("accept failed:", err)
			continue
		}
		go handleClient(client)
	}
}

func handleClient(conn net.Conn) {
	defer conn.Close()
	reader := bufio.NewReader(conn)

	// 1️⃣ 认证阶段
	if err := auth(reader, conn); err != nil {
		log.Println("auth failed:", err)
		return
	}
	log.Println("auth success from", conn.RemoteAddr())

	// 2️⃣ CONNECT 请求
	if err := connect(reader, conn); err != nil {
		log.Println("connect failed:", err)
		return
	}
}

func auth(reader *bufio.Reader, conn net.Conn) error {
	ver, err := reader.ReadByte()
	if err != nil {
		return fmt.Errorf("read ver failed:%w", err)
	}
	if ver != socks5Ver {
		return fmt.Errorf("unsupported ver:%d", ver)
	}
	methodSize, err := reader.ReadByte()
	if err != nil {
		return fmt.Errorf("read method size failed:%w", err)
	}
	method := make([]byte, methodSize)
	if _, err = io.ReadFull(reader, method); err != nil {
		return fmt.Errorf("read method failed:%w", err)
	}
	// 回复无需认证
	_, err = conn.Write([]byte{socks5Ver, 0x00})
	return err
}

func connect(reader *bufio.Reader, conn net.Conn) error {
	ver, _ := reader.ReadByte()
	cmd, _ := reader.ReadByte()
	rsv, _ := reader.ReadByte()
	if ver != socks5Ver || cmd != cmdConnect || rsv != 0x00 {
		return fmt.Errorf("invalid header")
	}

	atype, _ := reader.ReadByte()
	var addr string
	switch atype {
	case atypeIPV4:
		addr = readIPV4(reader)
	case atypeHOST:
		addr = readHost(reader)
	case atypeIPV6:
		addr = readIPV6(reader)
	default:
		return fmt.Errorf("unknown atype %v", atype)
	}
	port := readPort(reader)
	targetAddr := fmt.Sprintf("%s:%d", addr, port)

	// 🔗 连接目标服务器
	target, err := net.Dial("tcp", targetAddr)
	if err != nil {
		conn.Write([]byte{socks5Ver, 0x01, 0x00, atypeIPV4, 0, 0, 0, 0, 0, 0})
		return fmt.Errorf("connect to target failed: %v", err)
	}
	defer target.Close()

	// 回复成功
	conn.Write([]byte{socks5Ver, 0x00, 0x00, atypeIPV4, 0, 0, 0, 0, 0, 0})

	// 🔁 开始转发
	ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()
	relay(reader, conn, target, ctx)
	return nil
}

func readIPV4(reader *bufio.Reader) string {
	buf := make([]byte, 4)
	io.ReadFull(reader, buf)
	return fmt.Sprintf("%d.%d.%d.%d", buf[0], buf[1], buf[2], buf[3])
}
func readHost(reader *bufio.Reader) string {
	l, _ := reader.ReadByte()
	buf := make([]byte, l)
	io.ReadFull(reader, buf)
	return string(buf)
}
func readIPV6(reader *bufio.Reader) string {
	buf := make([]byte, 16)
	io.ReadFull(reader, buf)
	return fmt.Sprintf("%x", buf)
}
func readPort(reader *bufio.Reader) uint16 {
	buf := make([]byte, 2)
	io.ReadFull(reader, buf)
	return (uint16(buf[0]) << 8) | uint16(buf[1])
}

func relay(reader *bufio.Reader, conn net.Conn, targetConn net.Conn, ctx context.Context) {
	go io.Copy(targetConn, conn)
	io.Copy(conn, targetConn)

	<-ctx.Done()
}

```

##### 3.2 代码说明：

**main()**：启动服务器，监听 `127.0.0.1:1080`，每有新连接就创建一个协程处理。

**handleClient()**：处理每个客户端连接，包括认证和建立转发。

**auth()**：执行 SOCKS5 认证，这里固定返回“无需认证”。

**connect()**：解析客户端请求，获取目标地址和端口，建立到目标服务器的 TCP 连接。

**relay()**：在客户端和目标服务器之间进行数据转发。

**readIPV4/readHost/readIPV6/readPort()**：负责解析不同格式的地址。

##### 3.3 最终测试

测试时，可以通过在浏览器中设置 SOCKS5 代理，或者使用 `curl` 命令来进行请求：

```bash
curl --socks5-hostname 127.0.0.1:1080 http://example.com
```

### 4.4 消息系统

由服务器和客户端构建的简易消息系统

#### main.go

```go
package main

func main() {
	server := NewServer("127.0.0.1", 8889)
	server.Start()
}
```

#### server.go

```go
package main

import (
	"fmt"
	"io"
	"net"
	"sync"
	"time"
)

type Server struct {
	Ip   string
	Port int

	OnlineMap map[string]*User
	mapLock   sync.RWMutex
	Message   chan string
}

func NewServer(ip string, port int) *Server {
	server := &Server{
		Ip:        ip,
		Port:      port,
		OnlineMap: make(map[string]*User),
		Message:   make(chan string),
	}
	return server
}

func (this *Server) BroadCast(user *User, msg string) {
	sendMsg := "[" + user.Addr + "]" + user.Name + ":" + " msg: " + msg
	this.Message <- sendMsg
	fmt.Printf(" 广播发送成功 %s \n", sendMsg)
}

func (this *Server) MessageListener() {
	for {
		msg := <-this.Message

		this.mapLock.Lock()
		for _, client := range this.OnlineMap {
			client.C <- msg
		}
		this.mapLock.Unlock()
	}
}

func (this *Server) Handler(conn net.Conn) {
	//fmt.Println("链接建立成功")
	//defer conn.Close()

	user := NewUser(conn, this)
	user.Online()

	isLive := make(chan bool)

	go func() {
		buf := make([]byte, 4096)
		for {
			n, err := conn.Read(buf)
			if n == 0 {
				user.Offline()
				return
			}
			if err != nil && err != io.EOF {
				fmt.Println("conn.Read err:", err)
				return
			}
			// 去除 \n
			user.DoMessage(string(buf[:n-1]))

			isLive <- true
		}
	}()

	// 当前handler阻塞
	for {
		select {
		case <-isLive:
			{

			}
		case <-time.After(time.Second * 60):
			{
				user.SendMsg("你被踢了")
				close(user.C)
				conn.Close()

				fmt.Printf("用户 %s 被踢了 \n", user.Name)
				// 退出当前handler
				return // runtime.Goexit()
			}
		}
	}
}

func (this *Server) Start() {

	// socket listen
	listener, err := net.Listen("tcp", fmt.Sprintf("%s:%d", this.Ip, this.Port))
	if err != nil {
		fmt.Println("net.Listen err:", err)
		return
	}

	// close listener
	defer listener.Close()

	go this.MessageListener()

	// accept
	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("listener.Accept err:", err)
			continue
		} else {
			fmt.Println("链接建立成功")
		}

		// do handler
		go this.Handler(conn)
	}
}
```

#### client.go

```go
package main

import (
	"bufio"
	"flag"
	"fmt"
	"io"
	"net"
	"os"
	"strings"
)

type Client struct {
	ServerIp   string
	ServerPort int
	Name       string
	conn       net.Conn
	flag       int
}

var reader *bufio.Reader

func NewClient(serverIp string, serverPort int) *Client {

	client := &Client{
		ServerIp:   serverIp,
		ServerPort: serverPort,
		flag:       9999,
	}

	conn, err := net.Dial("tcp", fmt.Sprintf("%s:%d", serverIp, serverPort))
	if err != nil {
		fmt.Println("net.Dial error:", err)
		return nil
	}
	client.conn = conn

	return client
}

func (client *Client) DealResponse() {
	io.Copy(os.Stdout, client.conn)
}

func (client *Client) menu() bool {
	var flag int
	fmt.Println("1、公聊模式")
	fmt.Println("2、私聊模式")
	fmt.Println("3、更新用户名")
	fmt.Println("0、退出")
	fmt.Scanln(&flag)

	if flag >= 0 && flag <= 3 {
		client.flag = flag
		return true
	} else {
		fmt.Println("输入有误，请重新输入")
		return false
	}
}

func (client *Client) UpdateName() bool {
	fmt.Println("请输入用户名:")
	client.Name, _ = reader.ReadString('\n')
	client.Name = strings.TrimSpace(client.Name)

	sendMsg := "rename|" + client.Name + "\n"
	_, err := client.conn.Write([]byte(sendMsg))
	if err != nil {
		fmt.Println("UpdateName error:", err)
		return false
	}

	return true
}

func (client *Client) PublicChat() {
	var chatMsg string
	fmt.Println("请输入聊天内容，exit退出:")
	chatMsg, _ = reader.ReadString('\n')
	chatMsg = strings.TrimSpace(chatMsg)

	for chatMsg != "exit" {
		if len(chatMsg) != 0 {
			sendMsg := chatMsg + "\n"
			_, err := client.conn.Write([]byte(sendMsg))
			if err != nil {
				fmt.Println("write error:", err)
				break
			}
		}

		chatMsg = ""
		fmt.Println("请输入聊天内容，exit退出:")
		chatMsg, _ = reader.ReadString('\n')
		chatMsg = strings.TrimSpace(chatMsg)
	}
}

func (client *Client) selectUsers() {
	sendMsg := "who\n"
	_, err := client.conn.Write([]byte(sendMsg))
	if err != nil {
		fmt.Println("selectUsers error:", err)
	}
}

func (client *Client) PrivateChat() {
	var remoteName string
	var chatMsg string

	client.selectUsers()
	fmt.Println("请输入聊天对象的用户名， exit退出")
	remoteName, _ = reader.ReadString('\n')
	remoteName = strings.TrimSpace(remoteName)

	for remoteName != "exit" {
		fmt.Println("请输入聊天内容，exit退出:")
		chatMsg, _ = reader.ReadString('\n')
		chatMsg = strings.TrimSpace(chatMsg)

		for chatMsg != "exit" {
			if len(chatMsg) != 0 {
				sendMsg := "to|" + remoteName + "|" + chatMsg + "\n"
				_, err := client.conn.Write([]byte(sendMsg))
				if err != nil {
					fmt.Println("write error:", err)
					break
				}
			}

			chatMsg = ""
			fmt.Println("请输入聊天内容，exit退出:")
			chatMsg, _ = reader.ReadString('\n')
			chatMsg = strings.TrimSpace(chatMsg)
		}

		client.selectUsers()
		fmt.Println("请输入聊天对象，exit退出")
		remoteName, _ = reader.ReadString('\n')
		remoteName = strings.TrimSpace(remoteName)
	}
}

func (client *Client) Run() {
	for client.flag != 0 {
		for client.menu() != true {

		}
		switch client.flag {
		case 1:
			client.PublicChat()
			break
		case 2:
			fmt.Println("私聊模式")
			client.PrivateChat()
			break
		case 3:
			client.UpdateName()
			break
		}
	}
}

var serverIp string
var serverPort int

func init() {
	flag.StringVar(&serverIp, "ip", "127.0.0.1", "set server ip")
	flag.IntVar(&serverPort, "port", 8889, "set server port")
}

func main() {
	// 命令行解析
	flag.Parse()
	reader = bufio.NewReader(os.Stdin) // 初始化全局 reader

	client := NewClient(serverIp, serverPort)
	if client == nil {
		fmt.Println("Connect to the server successfully")
		return
	}

	go client.DealResponse()

	fmt.Println("Connect to the server success")
	client.Run()
}
```

#### user.go

```go
package main

import (
	"net"
	"strings"
)

type User struct {
	Name string
	Addr string
	C    chan string
	conn net.Conn

	server *Server
}

func NewUser(conn net.Conn, server *Server) *User {
	userAddr := conn.RemoteAddr().String()
	user := &User{
		Name: userAddr,
		Addr: userAddr,
		C:    make(chan string),
		conn: conn,

		server: server,
	}

	go user.ListenMessage()

	return user
}

func (this *User) Online() {
	// 用户上线， 将用户加到onlineMap中
	this.server.mapLock.Lock()
	this.server.OnlineMap[this.Name] = this
	this.server.mapLock.Unlock()
	this.server.BroadCast(this, "已上线")
}

func (this *User) Offline() {
	this.server.mapLock.Lock()
	delete(this.server.OnlineMap, this.Name)
	this.server.mapLock.Unlock()
	this.server.BroadCast(this, "已下线")
}

func (this *User) SendMsg(msg string) {
	this.conn.Write([]byte(msg))
}

func (this *User) DoMessage(msg string) {
	if msg == "who" {
		this.server.mapLock.Lock()

		for _, user := range this.server.OnlineMap {
			onlineMsg := "[" + user.Addr + "]" + user.Name + ":" + "在线。。。\n"
			this.SendMsg(onlineMsg)
		}

		this.server.mapLock.Unlock()
	} else if len(msg) > 7 && msg[:7] == "rename|" {
		newName := strings.Split(msg, "|")[1]

		_, ok := this.server.OnlineMap[newName]
		if ok {
			this.SendMsg("当前用户名被使用\n")
		} else {
			this.server.mapLock.Lock()
			delete(this.server.OnlineMap, this.Name)
			this.server.OnlineMap[newName] = this
			this.server.mapLock.Unlock()

			this.Name = newName
			this.SendMsg("您已更新用户名" + newName + "\n")
		}
	} else if len(msg) > 4 && msg[:3] == "to|" {
		remoteName := strings.Split(msg, "|")[1]
		if remoteName == "" {
			this.SendMsg("消息格式不正确， 请使用 \"to|张三|你好啊\" 格式。 \n")
			return
		}

		remoteUser, ok := this.server.OnlineMap[remoteName]
		if !ok {
			this.SendMsg("该用户不存在\n")
			return
		}

		content := strings.Split(msg, "|")[2]
		if content == "" {
			this.SendMsg("消息格式不正确， 请使用 \"to|张三|你好啊\" 形式发送消息。 \n")
			return
		}
		remoteUser.SendMsg(this.Name + "对你说：" + content + "\n")

	} else {
		this.server.BroadCast(this, msg)
	}
}

func (this *User) ListenMessage() {
	for {
		msg := <-this.C
		this.conn.Write([]byte(msg + "\n"))
	}
}
```

