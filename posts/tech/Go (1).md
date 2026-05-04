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

### **3.11 接口**

Go 的接口（interface）通过**方法集合**定义行为。只要一个类型拥有接口要求的全部方法，它就**自动实现**该接口，无需显式声明。

接口的典型用途是：
 **解耦依赖、方便测试、实现多态。**

------

#### **3.11.1 接口定义**

使用 `type` 定义接口，接口中只包含方法签名：

```go
type DB interface {
    Query(sql string) string
}
```

任何类型只要实现了 `Query` 方法，就自动实现了 `DB` 接口。

------

#### **3.11.2 实现接口（隐式实现）**

无需写“implements”。结构体只要提供同名方法即可：

```go
type MySQL struct{}

func (m MySQL) Query(sql string) string {
    return "real data"
}
```

现在 `MySQL` 就实现了 `DB`。

------

#### **3.11.3 使用接口（面向接口编程）**

函数依赖接口，而不是具体实现：

```go
func FetchUser(db DB) string {
    return db.Query("SELECT * FROM user")
}
```

调用时可以传入任何实现了 `DB` 的对象：

```go
mysql := MySQL{}
fmt.Println(FetchUser(mysql))
```

------

#### **3.11.4 使用接口进行 Mock 测试**

接口最重要的价值之一是：
 **在测试中轻松替换依赖。**

示例：创建 Mock 实现

```go
type MockDB struct{}

func (m MockDB) Query(sql string) string {
    return "fake data"
}
```

测试时替换真实数据库：

```go
mock := MockDB{}
fmt.Println(FetchUser(mock)) // 输出 fake data
```

这样可以避免依赖真实数据库，提升测试效率。

### **3.12 反射**

**反射**是 Go 提供的一种强大机制，允许程序在运行时检查类型、获取值、甚至修改值。反射通过 `reflect` 包实现，可以动态地获取或修改对象的类型和数据。它主要用于需要动态决定对象类型和操作的场景，比如 JSON 序列化、数据库 ORM 等。

#### **3.12.1 反射的基本操作**

1. **TypeOf**：返回一个值的类型。
2. **ValueOf**：返回一个值的具体值，可以访问其底层数据。

```go
package main

import (
	"fmt"
	"reflect"
)

type myInt int
type Person struct {
	name string
	age  int
}

// 反射：获取类型信息
func reflectFn(x interface{}) {
	v := reflect.TypeOf(x)
	fmt.Printf("类型：%v, 类型名称:%v 类型种类:%v \n", v, v.Name(), v.Kind())
}

func main() {
	a := 10
	b := 23.99
	c := true
	d := "hello"
	reflectFn(a)
	reflectFn(b)
	reflectFn(c)
	reflectFn(d)

	var e myInt = 10
	var p Person = Person{
		"ken",
		22,
	}
	reflectFn(e)
	reflectFn(p)
	var h = 25
	reflectFn(&h)

	var i = [3]int{1, 2, 3}
	reflectFn(i)

	var j = []int{1, 2, 3}
	reflectFn(j)
}
```

**输出：**

```
类型：int, 类型名称:int 类型种类:int
类型：float64, 类型名称:float64 类型种类:float64
类型：bool, 类型名称:bool 类型种类:bool
类型：string, 类型名称:string 类型种类:string
类型：main.myInt, 类型名称:myInt 类型种类:int
类型：main.Person, 类型名称:Person 类型种类:struct
类型：*int, 类型名称: 类型种类:ptr
类型：[3]int, 类型名称: 类型种类:array
类型：[]int, 类型名称: 类型种类:slice
```

#### **3.12.2 使用反射修改值**

反射不仅可以获取类型和数据，还可以修改数据的值。为了修改一个对象的字段值，必须通过指针操作。

```go
package main

import (
	"fmt"
	"reflect"
)

func reflectSetValue1(x interface{}) {
	v := reflect.ValueOf(x)
	if v.Kind() == reflect.Ptr && v.Elem().Kind() == reflect.Int64 {
		v.Elem().SetInt(120)
	}
}

func main() {
	var a int64 = 100
	reflectSetValue1(&a)
	fmt.Println(a) // 输出 120
}
```

#### **3.12.3 通过反射获取结构体字段**

反射可以用于获取结构体的字段信息，并通过 `Tag` 获取结构体字段的标签。

```go
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	Name  string `json:"name" form:"username"`
	Age   int    `json:"age"`
	Score int    `json:"score"`
}

func PrintStructField(s interface{}) {
	t := reflect.TypeOf(s)
	v := reflect.ValueOf(s)
	if t.Kind() != reflect.Struct {
		fmt.Println("传入的参数不是结构体")
		return
	}

	field0 := t.Field(0)
	fmt.Println("字段名称：", field0.Name)
	fmt.Println("字段类型：", field0.Type)
	fmt.Println("字段Tag：", field0.Tag)
	fmt.Println("字段Tag(json)：", field0.Tag.Get("json"))
}

func main() {
	s := Student{"Ken", 22, 100}
	PrintStructField(s)
}
```

**输出：**

```
字段名称： Name
字段类型： string
字段Tag： json:"name" form:"username"
字段Tag(json)： name
```

#### **3.12.4 反射动态调用结构体方法**

可以使用反射来动态调用结构体的方法。通过 `MethodByName` 可以获取结构体的方法，并使用 `Call` 来执行该方法。

```go
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	Name  string
	Age   int
	Score int
}

func (s Student) GetInfo() string {
	return fmt.Sprintf("Student: %s, Age: %d, Score: %d", s.Name, s.Age, s.Score)
}

func PrintStructFn(s interface{}) {
	t := reflect.TypeOf(s)
	v := reflect.ValueOf(s)

	method0 := t.Method(0)
	fmt.Println("方法名称：", method0.Name)
	fmt.Println("方法类型：", method0.Type)

	// 调用方法
	info := v.MethodByName("GetInfo").Call(nil)
	fmt.Println(info[0])
}

func main() {
	s := Student{"Ken", 22, 100}
	PrintStructFn(s)
}
```

**输出：**

```
方法名称： GetInfo
方法类型： func(main.Student) string
Student: Ken, Age: 22, Score: 100
```

#### **3.12.5 修改结构体字段的值**

通过反射，可以修改结构体的字段，前提是传入结构体的指针。

```go
package main

import (
	"fmt"
	"reflect"
)

func reflectChangeStruct(s interface{}) {
	t := reflect.TypeOf(s)
	v := reflect.ValueOf(s)
	if t.Kind() != reflect.Ptr || t.Elem().Kind() != reflect.Struct {
		fmt.Println("传入的不是结构体指针类型")
		return
	}

	v.Elem().FieldByName("Name").SetString("Li")
	v.Elem().FieldByName("Age").SetInt(11)
}

func main() {
	s := &Student{"Ken", 22, 100}
	reflectChangeStruct(s)
	fmt.Printf("%#v\n", s)
}
```

**输出：**

```
&main.Student{Name:"Li", Age:11, Score:100}
```

### **3.13 泛型**

在 Go 1.18 版本中，Go 引入了对泛型的支持，允许编写更加灵活和通用的代码。泛型使得你可以编写能够处理多种类型的数据结构和函数，而无需为每种类型编写单独的代码。

#### **3.13.1 泛型基本概念**

泛型使得函数、类型和数据结构可以操作任何类型的数据，而不仅仅是特定类型。泛型通过类型参数进行实现，这些类型参数在使用时才确定具体的类型。

泛型函数的定义格式如下：

```go
func functionName[T any](arg T) T {
    // implementation
}
```

其中：

- `T` 是类型参数，它可以代表任何类型（由 `any` 关键字表示）。
- 泛型函数在调用时会根据传入的具体类型来推断 `T` 的类型。

#### **3.13.2 泛型函数的使用**

泛型函数可以让你写一个函数，处理不同的数据类型。以下是一个简单的泛型函数示例：

```go
package main

// 定义一个getData的泛型方法
func getData[T any](value T) T {
	return value
}

func main() {
	str1 := getData[string]("Hello")
	println(str1)

	num := getData[int](123)
	println(num)

	str2 := getData("this is str")
	println(len(str2))
}
```

**输出**：

```
Hello
123
12
```

在上面的代码中，`getData` 是一个泛型函数，能够处理任何类型的参数，且返回该类型的数据。你可以显式地指定类型，或者让 Go 自动推断类型。

#### **3.13.3 泛型与接口约束**

你可以使用接口约束来限制泛型类型参数的范围。Go 中有多种方式来实现接口约束。例如，我们可以定义一个 `Number` 类型接口，来限制一个泛型函数只能接受整数或浮动类型的参数。

```go
package main

import "fmt"

// 定义一个 Number 接口，限制为 int、int32 或 float64 类型
type Number interface {
	int | int32 | float64
}

// 定义一个泛型方法
func Add[T Number](a, b T) T {
	return a + b
}

func main() {
	// 使用 Add 函数处理 int 类型
	fmt.Println(Add(1, 2)) // 输出: 3
	
	// 使用 Add 函数处理 float64 类型
	fmt.Println(Add(1, 2.1)) // 输出: 3.1
}
```

**输出**：

```
3
3.1
```

在上面的代码中，`Number` 类型接口约束 `T` 必须是 `int`、`int32` 或 `float64` 类型。这使得 `Add` 函数只能接受这些类型的参数。

#### **3.13.4 泛型类型**

Go 的泛型不仅限于函数，也可以用于定义泛型类型。下面是一个使用泛型类型来创建容器类型的例子：

```go
package main

import "fmt"

// 定义一个泛型容器类型
type Container[T any] struct {
	value T
}

// 为容器类型添加 Set 和 Get 方法
func (c *Container[T]) Set(value T) {
	c.value = value
}

func (c Container[T]) Get() T {
	return c.value
}

func main() {
	// 创建一个 int 类型的容器
	intContainer := Container[int]{}
	intContainer.Set(10)
	fmt.Println("Int value:", intContainer.Get()) // 输出: Int value: 10

	// 创建一个 string 类型的容器
	stringContainer := Container[string]{}
	stringContainer.Set("hello")
	fmt.Println("String value:", stringContainer.Get()) // 输出: String value: hello
}
```

**输出**：

```
Int value: 10
String value: hello
```

在上面的代码中，`Container` 是一个泛型类型，它可以保存任何类型的数据。我们使用 `Set` 和 `Get` 方法来设置和获取容器中的值。

#### **3.13.5 泛型与集合**

泛型对于集合类型（如数组、切片、映射等）特别有用，因为它们通常需要支持多种数据类型。以下是使用泛型定义一个支持不同类型集合的示例：

```go
package main

import "fmt"

// 泛型函数：返回集合的第一个元素
func getFirst[T any](items []T) T {
	return items[0]
}

func main() {
	ints := []int{1, 2, 3}
	strings := []string{"a", "b", "c"}

	// 使用泛型函数获取 int 类型集合的第一个元素
	fmt.Println(getFirst(ints)) // 输出: 1
	
	// 使用泛型函数获取 string 类型集合的第一个元素
	fmt.Println(getFirst(strings)) // 输出: a
}
```

**输出**：

```
1
a
```

### **3.14 格式化**

在 Go 语言中，格式化输出主要由标准库 `fmt` 提供。`fmt` 包实现了类似 C 语言 `printf` 风格的格式化能力，用于将变量按照指定格式转换为字符串，并输出到终端、字符串或其他输出流中。
格式化在 **日志输出、调试、字符串构造、错误信息展示** 等场景中被广泛使用。

------

#### **3.14.1 fmt 包概述**

`fmt` 包提供了一组用于格式化 I/O 的函数，常用的包括：

- `fmt.Printf`：格式化输出到标准输出
- `fmt.Sprintf`：格式化并返回字符串
- `fmt.Fprintf`：格式化输出到指定的 `io.Writer`

这些函数都基于 **格式化占位符（format specifier）** 工作。

示例：

```go
package main

import "fmt"

func main() {
	name := "Go"
	version := 1.22

	fmt.Printf("Language: %s, Version: %.2f\n", name, version)
}
```

**输出：**

```
Language: Go, Version: 1.22
```

------

#### **3.14.2 格式化占位符基本语法**

格式化字符串由普通文本和占位符组成，占位符以 `%` 开头，其基本形式为：

```
%[flags][width][.precision]verb
```

其中：

- `flags`：控制对齐、填充、符号等
- `width`：最小输出宽度
- `precision`：精度（常用于浮点数和字符串）
- `verb`：格式化动词，决定数据的输出形式

------

#### **3.14.3 通用占位符**

Go 提供了一组通用占位符，可用于多种类型。

```go
type User struct {
	ID   int
	Name string
}
```

| 占位符 | 说明              |
| ------ | ----------------- |
| `%v`   | 默认格式          |
| `%+v`  | 结构体字段名 + 值 |
| `%#v`  | Go 语法表示       |
| `%T`   | 值的类型          |
| `%%`   | 输出 `%`          |

示例：

```go
u := User{ID: 1, Name: "Tom"}

fmt.Printf("%v\n", u)
fmt.Printf("%+v\n", u)
fmt.Printf("%#v\n", u)
fmt.Printf("%T\n", u)
```

**输出：**

```
{1 Tom}
{ID:1 Name:Tom}
main.User{ID:1, Name:"Tom"}
main.User
```

------

#### **3.14.4 布尔类型格式化**

布尔类型使用 `%t` 输出：

```go
fmt.Printf("%t\n", true)
```

**输出：**

```
true
```

------

#### **3.14.5 整数类型格式化**

Go 支持多种整数进制格式化方式。

| 占位符 | 说明             |
| ------ | ---------------- |
| `%d`   | 十进制           |
| `%b`   | 二进制           |
| `%o`   | 八进制           |
| `%x`   | 十六进制（小写） |
| `%X`   | 十六进制（大写） |

示例：

```go
n := 42
fmt.Printf("%d %b %o %x %X\n", n, n, n, n, n)
```

**输出：**

```
42 101010 52 2a 2A
```

配合 `#` 标志可以输出进制前缀：

```go
fmt.Printf("%#x %#o\n", n, n)
```

**输出：**

```
0x2a 052
```

------

#### **3.14.6 浮点数格式化**

浮点数支持多种输出形式。

| 占位符 | 说明                  |
| ------ | --------------------- |
| `%f`   | 十进制小数            |
| `%e`   | 科学计数法（e）       |
| `%E`   | 科学计数法（E）       |
| `%g`   | 自动选择 `%f` 或 `%e` |
| `%G`   | 自动选择（大写）      |

示例：

```go
x := 3.1415926
fmt.Printf("%f\n", x)
fmt.Printf("%.2f\n", x)
fmt.Printf("%e\n", x)
```

**输出：**

```
3.141593
3.14
3.141593e+00
```

------

#### **3.14.7 字符串与字符格式化**

##### 字符串

| 占位符 | 说明             |
| ------ | ---------------- |
| `%s`   | 普通字符串       |
| `%q`   | 带引号字符串     |
| `%x`   | 十六进制字节表示 |

示例：

```go
fmt.Printf("%s\n", "hello")
fmt.Printf("%q\n", "hello\n")
```

**输出：**

```
hello
"hello\n"
```

##### 字符（rune / byte）

```go
fmt.Printf("%c\n", 'A')
fmt.Printf("%q\n", '中')
```

------

#### **3.14.8 指针格式化**

指针使用 `%p` 输出内存地址：

```go
x := 10
fmt.Printf("%p\n", &x)
```

------

#### **3.14.9 宽度、对齐与填充**

##### 宽度控制

```go
fmt.Printf("%6d\n", 42)
```

输出右对齐：

```
    42
```

##### 左对齐

```go
fmt.Printf("%-6d\n", 42)
42    
```

##### 补零

```go
fmt.Printf("%06d\n", 42)
000042
```

------

#### **3.14.10 自定义格式化（Stringer 接口）**

如果类型实现了 `String()` 方法，`fmt` 会自动调用它。

```go
type User struct {
	Name string
}

func (u User) String() string {
	return "User: " + u.Name
}

func main() {
	u := User{Name: "Tom"}
	fmt.Printf("%v\n", u)
}
```

**输出：**

```
User: Tom
```

------

#### **3.14.11 格式化函数对比**

| 函数      | 说明              |
| --------- | ----------------- |
| `Printf`  | 输出到标准输出    |
| `Sprintf` | 返回格式化字符串  |
| `Fprintf` | 输出到指定 Writer |

```go
s := fmt.Sprintf("id=%d", 10)
fmt.Println(s)
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

