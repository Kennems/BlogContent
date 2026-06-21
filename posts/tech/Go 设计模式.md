---
title : 'Go ——设计模式'
date : 2026-01-02T10:00:01+08:00
lastmod: 2026-01-02T10:00:01+08:00
description : "Go ——设计模式"
categories : ["Go"]
tags : ["GoLang"]
---

# Go ——设计模式

## 引言

设计模式（Design Pattern）是软件开发中经过反复验证的最佳实践，用于解决特定场景下的常见问题。Go 语言以其简洁的语法、强大的并发支持和丰富的标准库，非常适合实践经典设计模式。

Go 的设计模式特点：
- **接口隐式实现**：Go 的接口是隐式实现的，无需显式声明 `implements`，这使得解耦更加自然
- **组合优于继承**：Go 没有继承，通过结构体嵌入（Embedding）实现组合，更符合大多数设计模式的思想
- **goroutine 与 channel**：Go 的并发原语为观察者、中介者等模式提供了新的实现思路

本文档涵盖 GoF（Gang of Four）提出的 23 种经典设计模式，按创建型、结构型、行为型三大类组织，每种模式包含定义、使用场景、经典案例、Go 实现代码和单元测试。

---

## 设计模式分类总览

| 分类 | 序号 | 模式名称 | 核心思想 |
|------|------|----------|----------|
| **创建型** | 1 | 简单工厂 | 由工厂方法根据参数决定创建哪个产品 |
| **创建型** | 2 | 工厂方法 | 将对象创建延迟到子类 |
| **创建型** | 3 | 抽象工厂 | 创建一系列相关产品的接口 |
| **创建型** | 4 | 单例模式 | 确保一个类只有一个实例 |
| **创建型** | 5 | 建造者模式 | 分步构建复杂对象 |
| **创建型** | 6 | 原型模式 | 通过拷贝创建新对象 |
| **结构型** | 7 | 门面模式 | 为子系统提供统一入口 |
| **结构型** | 8 | 适配器模式 | 将不兼容的接口转换为兼容接口 |
| **结构型** | 9 | 代理模式 | 为对象提供代理以控制访问 |
| **结构型** | 10 | 装饰器模式 | 动态地为对象添加职责 |
| **结构型** | 11 | 桥接模式 | 将抽象与实现分离 |
| **结构型** | 12 | 组合模式 | 将对象组合成树形结构 |
| **结构型** | 13 | 享元模式 | 通过共享技术支持大量细粒度对象 |
| **行为型** | 14 | 观察者模式 | 一对多的依赖通知机制 |
| **行为型** | 15 | 解释器模式 | 定义语言文法并解释执行 |
| **行为型** | 16 | 模板方法 | 定义算法骨架，子类实现具体步骤 |
| **行为型** | 17 | 责任链模式 | 请求沿链传递直到被处理 |
| **行为型** | 18 | 状态模式 | 状态改变时行为随之改变 |
| **行为型** | 19 | 备忘录模式 | 保存和恢复对象内部状态 |
| **行为型** | 20 | 迭代器模式 | 统一遍历聚合对象 |
| **行为型** | 21 | 访问者模式 | 在不改变类的前提下定义新操作 |
| **行为型** | 22 | 命令模式 | 将请求封装为对象 |
| **行为型** | 23 | 中介者模式 | 用中介封装对象间交互 |
| **行为型** | 24 | 策略模式 | 封装可互换的算法 |

---

## 1. simple_factory 简单工厂

### 定义
定义一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类的实例化延迟到子类。

### 使用场景
- 创建对象的逻辑比较复杂，需要集中管理
- 客户端不需要知道具体产品类的类名，只需要知道参数
- 通过参数来创建不同类型的对象

### 经典案例
- **Go `database/sql`**：`sql.Open()` 根据驱动名称返回不同的数据库连接
- **Java `Calendar`**：`Calendar.getInstance()` 返回不同地区的日历实现
- **日志框架**：根据配置参数创建不同的日志输出器（文件、控制台、远程）
- **支付系统**：根据支付方式参数创建不同的支付处理器（微信、支付宝、银联）

### 代码

```go
package test

const (
	APITypeHi = iota + 1
	APITypeHello
)

type Greeting interface {
	Say(msg string) string
}

func NewGreeting(t int) Greeting {
	if t == APITypeHi {
		return &Hi{}
	} else if t == APITypeHello {
		return &Hello{}
	}
	return nil
}

type Hi struct{}

func (h *Hi) Say(msg string) string {
	return "Hi! " + msg
}

type Hello struct{}

func (h *Hello) Say(msg string) string {
	return "Hello! " + msg
}

```

```go
package test

import "testing"

func TestGreeting(t *testing.T) {
	tests := []struct {
		name     string
		apiType  int
		msg      string
		expected string
	}{
		{"HiType", APITypeHi, "2026", "Hi! 2026"},
		{"HelloType", APITypeHello, "2026", "Hello! 2026"},
		{"NilType", 999, "2026", ""},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			g := NewGreeting(tt.apiType)
			if g == nil {
				if tt.expected != "" {
					t.Errorf("NewGreeting(%d) returned nil, expected non-nil", tt.apiType)
				}
				return
			}

			out := g.Say(tt.msg)
			if out != tt.expected {
				t.Errorf("Say() = %q, want %q", out, tt.expected)
			}
		})
	}
}

```

## 2. factory_method 工厂方法

### 定义
定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。

### 使用场景
- 一个类无法预知需要创建哪种对象
- 子类需要指定创建的对象类型
- 将对象的创建逻辑委托给子类

### 经典案例
- **Go `net/http`**：`http.Handler` 接口的创建通过不同的 Handler 工厂实现
- **JDBC `Connection`**：`DriverManager.getConnection()` 通过注册的 Driver 工厂创建连接
- **日志框架**：`LoggerFactory.getLogger()` 返回不同 Logger 实现
- **集合框架**：`Iterator` 通过工厂方法创建，每种集合提供不同的迭代器

### 核心概念
与简单工厂的区别：简单工厂将所有创建逻辑集中在一个方法中；工厂方法将创建逻辑分散到子类中，符合开闭原则。

### 代码

```go
package test

// Operator 接口
type Operator interface {
	SetA(int)
	SetB(int)
	Result() int
}

// 工厂接口
type OperatorFactory interface {
	Create() Operator
}

// OperatorBase 提供公共字段与方法
type OperatorBase struct {
	a, b int
}

func (o *OperatorBase) SetA(a int) { o.a = a }
func (o *OperatorBase) SetB(b int) { o.b = b }

// PlusOperator
type PlusOperator struct{ *OperatorBase }

func (p *PlusOperator) Result() int { return p.a + p.b }

// PlusOperatorFactory
type PlusOperatorFactory struct{}

func (PlusOperatorFactory) Create() Operator {
	return &PlusOperator{&OperatorBase{}}
}

// MinusOperator
type MinusOperator struct{ *OperatorBase }

func (m *MinusOperator) Result() int { return m.a - m.b }

// MinusOperatorFactory
type MinusOperatorFactory struct{}

func (MinusOperatorFactory) Create() Operator {
	return &MinusOperator{&OperatorBase{}}
}

```

```go
package test

import "testing"

func TestOperatorFactory(t *testing.T) {
	tests := []struct {
		name     string
		factory  OperatorFactory
		a, b     int
		expected int
	}{
		{"Plus", &PlusOperatorFactory{}, 1001, 10, 1011},
		{"Minus", &MinusOperatorFactory{}, 1001, 10, 991},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			op := tt.factory.Create()
			op.SetA(tt.a)
			op.SetB(tt.b)
			if got := op.Result(); got != tt.expected {
				t.Fatalf("expected %v, got %v", tt.expected, got)
			}
		})
	}
}

```

## 3. abstract_factory 抽象工厂

### 定义
提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

### 使用场景
- 系统需要独立于产品的创建、组合和表示
- 系统要由多个产品系列中的一个来配置
- 强调一系列相关的产品对象一起使用

### 经典案例
- **Go 标准库**：`database/sql` 的 `Open()` + `Driver` 机制，不同数据库提供不同的 Connection/Statement 产品族
- **跨平台 GUI 工具包**：Windows 和 Mac 系统分别提供 Button、TextField 的产品族
- **主题系统**：亮色主题和暗色主题分别提供不同的颜色、字体、图标系列
- **DAO 层**：RDB 和 XML 两种持久化方案，各自提供 MainDAO 和 DetailDAO

### 核心概念
与工厂方法的区别：工厂方法生产单一产品；抽象工厂生产一系列相关产品（产品族）。

### 代码

```go
package test

// OrderMainDAO 为订单主记录
type OrderMainDAO interface {
	SaveOrderMain() string
}

// OrderDetailDAO 为订单详情纪录
type OrderDetailDAO interface {
	SaveOrderDetail() string
}

// DAOFactory DAO 抽象工厂接口
type DAOFactory interface {
	CreateOrderMainDAO() OrderMainDAO
	CreateOrderDetailDAO() OrderDetailDAO
}

// ------------------ RDB 实现 ------------------

type RDBMainDAO struct{}

func (*RDBMainDAO) SaveOrderMain() string {
	return "rdb main save"
}

type RDBDetailDAO struct{}

func (*RDBDetailDAO) SaveOrderDetail() string {
	return "rdb detail save"
}

type RDBDAOFactory struct{}

func (*RDBDAOFactory) CreateOrderMainDAO() OrderMainDAO {
	return &RDBMainDAO{}
}

func (*RDBDAOFactory) CreateOrderDetailDAO() OrderDetailDAO {
	return &RDBDetailDAO{}
}

// ------------------ XML 实现 ------------------

type XMLMainDAO struct{}

func (*XMLMainDAO) SaveOrderMain() string {
	return "xml main save"
}

type XMLDetailDAO struct{}

func (*XMLDetailDAO) SaveOrderDetail() string {
	return "xml detail save"
}

type XMLDAOFactory struct{}

func (*XMLDAOFactory) CreateOrderMainDAO() OrderMainDAO {
	return &XMLMainDAO{}
}

func (*XMLDAOFactory) CreateOrderDetailDAO() OrderDetailDAO {
	return &XMLDetailDAO{}
}

```

```go
package test

import "testing"

// GetMainAndDetail 调用工厂生成 DAO 并返回结果
func GetMainAndDetail(factory DAOFactory) (mainResult, detailResult string) {
	mainResult = factory.CreateOrderMainDAO().SaveOrderMain()
	detailResult = factory.CreateOrderDetailDAO().SaveOrderDetail()
	return
}

func TestAbstractFactory(t *testing.T) {
	tests := []struct {
		name           string
		factory        DAOFactory
		expectedMain   string
		expectedDetail string
	}{
		{"RDBFactory", &RDBDAOFactory{}, "rdb main save", "rdb detail save"},
		{"XMLFactory", &XMLDAOFactory{}, "xml main save", "xml detail save"},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			mainResult, detailResult := GetMainAndDetail(tt.factory)
			if mainResult != tt.expectedMain {
				t.Errorf("mainResult = %q, want %q", mainResult, tt.expectedMain)
			}
			if detailResult != tt.expectedDetail {
				t.Errorf("detailResult = %q, want %q", detailResult, tt.expectedDetail)
			}
		})
	}
}

```

## 4. singleton 单例模式

### 定义
保证一个类仅有一个实例，并提供一个访问它的全局访问点。

### 使用场景
- 系统的某个类只需要一个全局实例
- 对象创建成本较高（配置管理、连接池、线程池）
- 需要控制实例数量以节省系统资源

### 经典案例
- **Go `sync.Once`**：标准库提供的线程安全一次性初始化原语，是 Go 实现单例的推荐方式
- **配置管理器**：整个应用共享一份配置实例
- **数据库连接池**：全局唯一的连接池管理所有数据库连接
- **日志记录器**：统一的日志输出实例，避免重复创建文件句柄
- **Spring IoC 容器**：默认以单例方式管理 Bean

### 代码

```go
package test

import "sync"

type Singleton interface {
	Foo()
}

type singleton struct{}

func (s *singleton) Foo() {}

var (
	once     sync.Once
	instance Singleton
)

func GetSingleton() Singleton {
	once.Do(func() {
		instance = &singleton{}
	})
	return instance
}

```

```go
package test

import (
	"sync"
	"testing"
)

const goroutineCount = 1000

// 单线程测试
func TestSingletonSingleThread(t *testing.T) {
	a := GetSingleton()
	b := GetSingleton()
	if a != b {
		t.Fatalf("Singleton failed: a != b")
	}
}

// 并发测试
func TestSingletonConcurrent(t *testing.T) {
	start := make(chan struct{})
	instances := [goroutineCount]Singleton{}
	wg := sync.WaitGroup{}
	wg.Add(goroutineCount)

	for i := 0; i < goroutineCount; i++ {
		go func(idx int) {
			<-start
			instances[idx] = GetSingleton()
			wg.Done()
		}(i)
	}

	// 同时启动所有 goroutine
	close(start)
	wg.Wait()

	// 验证所有实例都相等
	for i := 0; i < goroutineCount-1; i++ {
		if instances[i] != instances[i+1] {
			t.Fatalf("Singleton failed: instances[%d] != instances[%d]", i, i+1)
		}
	}
}

```

## 5. builder 建造者

### 定义
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

### 使用场景
- 创建对象的步骤固定，但具体实现不同
- 需要创建的产品有复杂的内部结构
- 希望将构建过程和产品本身解耦

### 经典案例
- **Go `strings.Builder`**：通过 Write 系列方法逐步构建字符串，最后调用 String() 获取结果
- **Lombok `@Builder`**：自动生成 Builder 模式的代码
- **Java `StringBuilder`/`StringBuffer`**：逐步构建字符串
- **Kubernetes 客户端**：使用 Builder 模式构建复杂的 API 请求对象

### 核心概念
- **Builder**：定义建造步骤的接口
- **Director**：控制建造过程（步骤顺序）
- **Product**：最终构建出的产品

### 代码

```go
package test

// Builder 是生成器接口
type Builder interface {
	Part1()
	Part2()
	Part3()
}

// Director 构建指挥者
type Director struct {
	builder Builder
}

// NewDirector ...
func NewDirector(builder Builder) *Director {
	return &Director{builder: builder}
}

// Construct 构建产品
func (d *Director) Construct() {
	d.builder.Part1()
	d.builder.Part2()
	d.builder.Part3()
}

// ---------------- Builder1 ----------------

type Builder1 struct {
	result string
}

func (b *Builder1) Part1()            { b.result += "1" }
func (b *Builder1) Part2()            { b.result += "2" }
func (b *Builder1) Part3()            { b.result += "3" }
func (b *Builder1) GetResult() string { return b.result }

// ---------------- Builder2 ----------------

type Builder2 struct {
	result int
}

func (b *Builder2) Part1()         { b.result += 1 }
func (b *Builder2) Part2()         { b.result += 2 }
func (b *Builder2) Part3()         { b.result += 3 }
func (b *Builder2) GetResult() int { return b.result }

```

```go
package test

import "testing"

func TestBuilder(t *testing.T) {
	tests := []struct {
		name     string
		builder  Builder
		expected interface{}
		getRes   func() interface{}
	}{
		{
			name:     "Builder1",
			builder:  &Builder1{},
			expected: "123",
			getRes: func() interface{} {
				return (&Builder1{}).GetResult()
			},
		},
		{
			name:     "Builder2",
			builder:  &Builder2{},
			expected: 6,
			getRes: func() interface{} {
				return (&Builder2{}).GetResult()
			},
		},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			d := NewDirector(tt.builder)
			d.Construct()

			var actual interface{}
			switch b := tt.builder.(type) {
			case *Builder1:
				actual = b.GetResult()
			case *Builder2:
				actual = b.GetResult()
			default:
				t.Fatalf("unsupported builder type")
			}

			if actual != tt.expected {
				t.Fatalf("expected %v, got %v", tt.expected, actual)
			}
		})
	}
}

```

## 6. prototype 原型模式

### 定义
用原型实例指定创建对象的种类，并通过拷贝这些原型创建新的对象。

### 使用场景
- 创建对象的成本较大（如数据库查询、网络请求），而复制成本较低
- 需要避免使用工厂模式创建子类
- 需要在运行时动态指定对象的类型

### 经典案例
- **Go 的切片操作**：`copy()` 函数、`append()` 返回新切片本质是原型复制
- **Java `Object.clone()`**：所有 Java 对象都可以通过 clone() 方法克隆
- **Spring 原型作用域**：`@Scope("prototype")` 每次返回新实例
- **游戏开发**：创建大量相似怪物或子弹时，通过原型复制而非新建

### 核心概念
深拷贝 vs 浅拷贝：深拷贝复制所有引用类型的内容；浅拷贝只复制引用地址。本示例使用值拷贝实现深克隆。

### 代码

```go
package test

// Cloneable 是原型对象需要实现的接口
type Cloneable interface {
	Clone() Cloneable
}

type PrototypeManager struct {
	prototypes map[string]Cloneable
}

func NewPrototypeManager() *PrototypeManager {
	return &PrototypeManager{
		prototypes: make(map[string]Cloneable),
	}
}

// Get 返回指定名称的原型克隆对象
func (p *PrototypeManager) Get(name string) Cloneable {
	proto, ok := p.prototypes[name]
	if !ok {
		return nil
	}
	return proto.Clone()
}

// Set 注册原型对象
func (p *PrototypeManager) Set(name string, prototype Cloneable) {
	p.prototypes[name] = prototype
}

```

```go
package test

import "testing"

// Type1 原型类型1
type Type1 struct {
	Name string
}

func (t *Type1) Clone() Cloneable {
	tc := *t
	return &tc
}

// Type2 原型类型2
type Type2 struct {
	Name string
}

func (t *Type2) Clone() Cloneable {
	tc := *t
	return &tc
}

func setupManager() *PrototypeManager {
	manager := NewPrototypeManager()
	manager.Set("t1", &Type1{Name: "type1"})
	manager.Set("t2", &Type2{Name: "type2"})
	return manager
}

func TestPrototypeClone(t *testing.T) {
	manager := setupManager()

	tests := []struct {
		name       string
		prototype  string
		expected   string
		typeAssert string // 用于类型断言检查
	}{
		{"Type1 Clone", "t1", "type1", "Type1"},
		{"Type2 Clone", "t2", "type2", "Type2"},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			obj := manager.Get(tt.prototype)
			if obj == nil {
				t.Fatalf("Get(%s) returned nil", tt.prototype)
			}

			// 验证克隆对象不是原对象
			original := manager.prototypes[tt.prototype]
			if obj == original {
				t.Fatalf("Clone returned the same object as original")
			}

			// 类型断言并检查字段
			switch tt.typeAssert {
			case "Type1":
				casted := obj.(*Type1)
				if casted.Name != tt.expected {
					t.Fatalf("expected %s, got %s", tt.expected, casted.Name)
				}
			case "Type2":
				casted := obj.(*Type2)
				if casted.Name != tt.expected {
					t.Fatalf("expected %s, got %s", tt.expected, casted.Name)
				}
			}
		})
	}
}
```

## 7. facade 门面模式

### 定义
为子系统中的一组接口提供一个统一的接口。门面模式定义了一个高层接口，使得子系统更容易使用。

### 使用场景
- 为复杂子系统提供一个简单入口
- 客户端与多个子系统存在强依赖，希望解耦
- 构建分层系统时，作为每层的入口点

### 经典案例
- **Go `net/http`**：`http.HandleFunc()` 和 `http.ListenAndServe()` 封装了底层的 ServeMux、Listener、Server 等
- **SLF4J**：Java 的日志门面，统一了 log4j、logback、java.util.logging 等日志框架
- **RESTful API**：Controller 层作为 Service 层的门面
- **复杂库的 API**：提供简洁的 API 隐藏复杂的内部实现

### 代码

```go
package test

type API interface {
	Test() string
}

func NewAPI() API {
	return &apiImpl{
		a: NewAImpl(),
		b: NewBImpl(),
	}
}

type apiImpl struct {
	a AModule
	b BModule
}

func (api *apiImpl) Test() string {
	// 把两部分结果拼接返回
	return api.a.TestA() + api.b.TestB()
}

// --- AModule ---

type AModule interface {
	TestA() string
}

type AImpl struct{}

func NewAImpl() AModule {
	return &AImpl{}
}

func (a *AImpl) TestA() string {
	return "testA ... "
}

// --- BModule ---

type BModule interface {
	TestB() string
}

type BImpl struct{}

func NewBImpl() BModule {
	return &BImpl{}
}

func (b *BImpl) TestB() string {
	return "testB ... "
}

```

```go
package test

import "testing"

func TestFacade(t *testing.T) {
	api := NewAPI()
	out := api.Test()
	expected := "testA ... testB ... "

	if out != expected {
		t.Errorf("API Test() = %q, want %q", out, expected)
	}
}

```

## 8. adapter 适配器

### 定义
将一个类的接口转换成客户希望的另一个接口。适配器模式让那些接口不兼容的类可以一起工作。

### 使用场景
- 想使用一个已存在的类，但其接口不符合需求
- 想创建一个可复用的类，与不兼容接口的类协同工作
- 需要适配多个版本或多种实现

### 经典案例
- **Go `io` 包**：`io.Copy()` 接受 `io.Reader` 和 `io.Writer`，通过适配器模式将不同类型的数据源统一
- **JDBC 驱动**：每个数据库提供 JDBC 适配器，应用程序统一通过 JDBC 接口操作不同数据库
- **充电器转换头**：不同国家的电源插座标准不同，通过转换头适配
- **AB 兼容**：新接口和旧接口之间的适配转换

### 代码

```go
package test

type Adapter interface {
	Request() string
}

func NewAdapter(adaptee Adaptee) Adapter {
	return &adapter{
		adaptee: adaptee,
	}
}

type adapter struct {
	adaptee Adaptee
}

func (a *adapter) Request() string {
	return a.adaptee.SpecificRequest()
}

// --- Adaptee 接口与实现 ---

type Adaptee interface {
	SpecificRequest() string
}

type adaptee struct{}

func NewAdaptee() Adaptee {
	return &adaptee{}
}

func (a *adaptee) SpecificRequest() string {
	return "specific request called"
}

```

```go
package test

import "testing"

func TestAdapter(t *testing.T) {
	adaptee := NewAdaptee()
	adapter := NewAdapter(adaptee)

	out := adapter.Request()
	expected := "specific request called"

	if out != expected {
		t.Errorf("Adapter Request() = %q, want %q", out, expected)
	}
}
```

## 9. proxy 代理

### 定义
为其他对象提供一种代理以控制对这个对象的访问。

### 使用场景
- 远程代理：为一个位于不同地址空间的对象提供本地代表
- 虚拟代理：延迟加载大对象，直到真正需要时才创建
- 保护代理：控制对敏感对象的访问权限
- 智能引用：在访问对象时添加额外操作（如引用计数、日志）

### 经典案例
- **Go `net/http`**：`httputil.ReverseProxy` 反向代理服务器
- **Nginx**：作为反向代理服务器，负载均衡到多个后端
- **Spring AOP**：通过动态代理实现切面编程
- **RPC 框架**：客户端通过存根（Stub）代理远程服务调用
- **延迟加载**：Hibernate 的懒加载代理

### 代码

```go
package test

type Subject interface {
	Do() string
}

type RealSubject struct{}

func (RealSubject) Do() string {
	return "real"
}

type Proxy struct {
	real Subject
}

func NewProxy(real Subject) Subject {
	return &Proxy{real: real}
}

func (p *Proxy) Do() string {
	res := "pre:"
	res += p.real.Do()
	res += ":after"
	return res
}

```

```go
package test

import "testing"

func TestProxy(t *testing.T) {
	real := &RealSubject{}
	sub := NewProxy(real)

	res := sub.Do()

	if res != "pre:real:after" {
		t.Fatalf("expect pre:real:after, got %s", res)
	}
}

```

## 10. decorator 装饰器模式

### 定义
动态地给一个对象添加一些额外的职责。就增加功能来说，装饰模式比生成子类更为灵活。

### 使用场景
- 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责
- 需要给一个对象扩展功能，但又不希望通过继承来增加子类数量
- Java I/O 流的设计（`BufferedInputStream` 装饰 `FileInputStream`）

### 经典案例
- **Java I/O 流**：`BufferedReader` 装饰 `FileReader` 以提供缓冲功能，`DataInputStream` 装饰 `InputStream`
- **Python 装饰器语法**：`@staticmethod`、`@classmethod` 等语法糖本质就是装饰器模式
- **中间件**：Web 框架的中间件机制（如 Gin 的 `Use()`），每个中间件装饰请求处理链
- **Go `io` 包**：`io.MultiWriter`、`io.TeeReader` 等包装器

### 核心概念
- **Component（抽象组件）**：定义对象的接口
- **ConcreteComponent（具体组件）**：被装饰的原始对象
- **Decorator（装饰器基类）**：持有一个 Component 引用，实现 Component 接口
- **ConcreteDecorator（具体装饰器）**：在调用父类方法前后添加额外职责

### 个人想法
注意装饰器内部维护一个对象，所有装饰器的子类在操作时，必须调用父类的函数，一直从下到上再到下。相比于继承，装饰模式可以在运行时组合不同的行为。

### 代码

本示例模拟穿衣打扮：人（person）可以被 T恤、大裤衩、破球鞋层层装饰，每层装饰在展示时依次叠加。

```go
package decorator

import (
	"fmt"
)

type person struct {
	Name string
}

func (p *person) show() {
	if p == nil {
		return
	}
	fmt.Println("姓名：", p.Name)
}

type AbstractPerson interface {
	show()
}

type Decorator struct {
	AbstractPerson
}

func (d *Decorator) SetDecorator(component AbstractPerson) {
	if d == nil {
		return
	}
	d.AbstractPerson = component
}

func (d *Decorator) show() {
	if d == nil {
		return
	}
	if d.AbstractPerson != nil {
		d.AbstractPerson.show()
	}
}

type TShirts struct {
	Decorator
}

func (t *TShirts) show() {
	if t == nil {
		return
	}
	t.Decorator.show()
	fmt.Println("T恤")
}

type BigTrouser struct {
	Decorator
}

func (b *BigTrouser) show() {
	if b == nil {
		return
	}
	b.Decorator.show()
	fmt.Println("大裤衩")
}

type Sneakers struct {
	Decorator
}

func (b *Sneakers) show() {
	if b == nil {
		return
	}
	b.Decorator.show()
	fmt.Println("破球鞋")
}
```

```go
package decorator

import "testing"

func TestDecorator(t *testing.T) {
	p := &person{"Alice"}

	ts := new(TShirts)
	ts.SetDecorator(p)

	bt := new(BigTrouser)
	bt.SetDecorator(ts)

	sk := new(Sneakers)
	sk.SetDecorator(bt)

	// sneakers -> bigtrouser -> tshirts -> person
	sk.show()
}

func TestDecoratorChain(t *testing.T) {
	// 验证多层装饰不会 panic
	p := &person{"Bob"}
	ts := new(TShirts)
	ts.SetDecorator(p)

	bt := new(BigTrouser)
	bt.SetDecorator(ts)

	sk := new(Sneakers)
	sk.SetDecorator(bt)

	sk.show()
}

func TestDecoratorWithoutInner(t *testing.T) {
	d := new(Decorator)
	// 没有设置内部对象时，show 不应 panic
	d.show()
}
```

## 11. bridge 桥接模式

### 定义
将抽象部分与它的实现部分分离，使它们都可以独立地变化。

### 使用场景
- 不希望在抽象和实现之间形成固定的绑定关系
- 抽象和实现都应当能通过子类化独立扩展
- 对具体实现的修改不影响客户端代码

### 经典案例
- **Go `io.Reader`/`io.Writer` 接口**：具体的读取实现（文件、网络、内存缓冲）与使用方式分离
- **JDBC 驱动**：`DriverManager` 是抽象，具体的数据库驱动是实现，两者独立变化
- **GUI 工具包**：窗口抽象（Button、TextField）与具体平台实现（Windows、Mac、Linux）分离
- **Logger 框架**：日志记录器（抽象）和日志输出目标（文件、控制台、远程服务器）分离

### 个人想法
组合/聚合复用原则。桥接模式本质上是"优先使用组合而非继承"的体现。

### 代码

本示例模拟手机和软件的搭配：手机（PhoneA、PhoneB）是抽象，软件（SoftwareA、SoftwareB）是实现，两者可以任意组合。

```go
package bridge

import (
	"fmt"
)

type Phone struct {
	soft ISoftware
	name string
}

func (p *Phone) setSoft(soft ISoftware) {
	if p == nil {
		return
	}
	p.soft = soft
}

func (p *Phone) Run() {
	if p == nil {
		return
	}
	fmt.Println(p.name)
	p.soft.Run()
}

type PhoneA struct {
	Phone
}

func NewPhoneA(name string) *PhoneA {
	return &PhoneA{Phone{name: name}}
}

type PhoneB struct {
	Phone
}

func NewPhoneB(name string) *PhoneB {
	return &PhoneB{Phone{name: name}}
}

type ISoftware interface {
	Run()
}

type Software struct {
	name string
}

type SoftwareA struct {
	Software
}

func (s *Software) Run() {
	if s == nil {
		return
	}
	fmt.Println(s.name)
}

type SoftwareB struct {
	Software
}
```

```go
package bridge

import "testing"

func TestBridge(t *testing.T) {
	sa := &SoftwareA{Software{"软件A"}}
	sb := &SoftwareB{Software{"软件B"}}

	pa := NewPhoneA("手机A")
	pb := NewPhoneB("手机B")

	pa.setSoft(sa)
	pb.setSoft(sb)

	// 验证手机和软件的组合可以正常执行
	pa.Run()
	pb.Run()
}

func TestBridgeSetSoft(t *testing.T) {
	pa := NewPhoneA("测试手机")
	sa := &SoftwareA{Software{"测试软件"}}
	pa.setSoft(sa)
	// 验证设置后可以运行，不会 panic
	pa.Run()
}

func TestBridgePhoneBWithSoftwareA(t *testing.T) {
	// 验证任意手机可以搭配任意软件（桥接模式的核心）
	pb := NewPhoneB("手机B")
	sa := &SoftwareA{Software{"软件A"}}
	pb.setSoft(sa)
	pb.Run()
}
```

## 12. composite 组合模式

### 定义
将对象组合成树形结构以表示”部分-整体”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

### 使用场景
- 需要表示对象的部分-整体层次结构（树形结构）
- 希望统一对待单个对象和组合对象
- 树形菜单、文件系统、组织架构

### 经典案例
- **Java AWT/Swing**：`Container` 和 `Component`，Container 可以包含其他 Component
- **Go 的 `html/template`**：模板可以嵌套其他模板
- **文件系统**：目录（Composite）包含文件（Leaf），统一通过 `Read()`/`Write()` 操作
- **XML/HTML DOM 树**：节点可以包含子节点，叶节点没有子节点

### 核心概念
- **Component（组件接口）**：声明了叶子和容器的公共接口
- **Leaf（叶子节点）**：没有子节点的对象
- **Composite（容器节点）**：有子节点，实现子节点管理接口

### 个人想法
组合模式的关键是透明性——客户端不需要区分操作的是单个对象还是组合对象。在实现时，Leaf 节点可以空实现 add/remove 方法（透明方式），也可以通过抛出不支持操作异常来提示客户端。

### 代码

本示例模拟公司组织架构：总公司包含分公司和部门，分公司又可以包含子公司和部门，统一通过 display() 和 lineOfDuty() 操作。

```go
package composite

import (
	“fmt”
	“strings”
)

// 公司管理接口
type Company interface {
	Name() string
	add(Company)
	remove(Company)
	display(int)
	lineOfDuty()
}

type RealCompany struct {
	name string
}

func (r *RealCompany) Name() string {
	if r == nil {
		return “”
	}
	return r.name
}

// 具体公司 -- Composite 节点，可以包含子节点
type ConcreteCompany struct {
	RealCompany
	list []Company
}

func NewConcreteCompany(name string) *ConcreteCompany {
	return &ConcreteCompany{RealCompany{name}, []Company{}}
}

func (c *ConcreteCompany) add(newc Company) {
	if c == nil {
		return
	}
	c.list = append(c.list, newc)
}

// 按名称移除（避免直接比较 interface 导致只能移除同一指针的问题）
func (c *ConcreteCompany) remove(delc Company) {
	if c == nil {
		return
	}
	for i, val := range c.list {
		if val.Name() == delc.Name() {
			c.list = append(c.list[:i], c.list[i+1:]...)
			return
		}
	}
}

func (c *ConcreteCompany) display(depth int) {
	if c == nil {
		return
	}
	fmt.Println(strings.Repeat(“-”, depth), “ “, c.name)
	for _, val := range c.list {
		val.display(depth + 2)
	}
}

func (c *ConcreteCompany) lineOfDuty() {
	if c == nil {
		return
	}
	for _, val := range c.list {
		val.lineOfDuty()
	}
}

// 人力资源部门 -- Leaf 节点
type HRDepartment struct {
	RealCompany
}

func NewHRDepartment(name string) *HRDepartment {
	return &HRDepartment{RealCompany{name}}
}

func (h *HRDepartment) add(c Company)    {}
func (h *HRDepartment) remove(c Company) {}

func (h *HRDepartment) display(depth int) {
	if h == nil {
		return
	}
	fmt.Println(strings.Repeat(“-”, depth), “ “, h.name)
}

func (h *HRDepartment) lineOfDuty() {
	if h == nil {
		return
	}
	fmt.Println(h.name, “员工招聘培训管理”)
}

// 财务部门 -- Leaf 节点
type FinanceDepartment struct {
	RealCompany
}

func NewFinanceDepartment(name string) *FinanceDepartment {
	return &FinanceDepartment{RealCompany{name}}
}

func (h *FinanceDepartment) add(c Company)    {}
func (h *FinanceDepartment) remove(c Company) {}

func (h *FinanceDepartment) display(depth int) {
	if h == nil {
		return
	}
	fmt.Println(strings.Repeat(“-”, depth), “ “, h.name)
}

func (h *FinanceDepartment) lineOfDuty() {
	if h == nil {
		return
	}
	fmt.Println(h.name, “公司财务收支管理”)
}
```

```go
package composite

import (
	“strings”
	“testing”
)

func TestCompositeBuildAndDisplay(t *testing.T) {
	root := NewConcreteCompany(“北京总公司”)
	root.add(NewHRDepartment(“总公司人力资源部”))
	root.add(NewFinanceDepartment(“总公司财务部”))

	compA := NewConcreteCompany(“上海华东分公司”)
	compA.add(NewHRDepartment(“上海华东分公司人力资源部”))
	compA.add(NewFinanceDepartment(“上海华东分公司财务部”))
	root.add(compA)

	// 验证根节点有3个子节点（人力资源部、财务部、上海华东分公司）
	if len(root.list) != 3 {
		t.Errorf(“root should have 3 children, got %d”, len(root.list))
	}

	// 验证上海华东分公司有2个子节点
	if len(compA.list) != 2 {
		t.Errorf(“compA should have 2 children, got %d”, len(compA.list))
	}
}

func TestCompositeRemove(t *testing.T) {
	root := NewConcreteCompany(“总公司”)
	hr := NewHRDepartment(“人力资源部”)
	fin := NewFinanceDepartment(“财务部”)
	root.add(hr)
	root.add(fin)

	if len(root.list) != 2 {
		t.Fatalf(“expected 2 children after add, got %d”, len(root.list))
	}

	// 按名称移除（使用新创建的相同名称对象也可以移除）
	root.remove(NewHRDepartment(“人力资源部”))
	if len(root.list) != 1 {
		t.Errorf(“expected 1 child after remove, got %d”, len(root.list))
	}

	// 验证剩下的是财务部
	if root.list[0].Name() != “财务部” {
		t.Errorf(“expected remaining child to be '财务部', got %q”, root.list[0].Name())
	}
}

func TestCompositeLeafOperations(t *testing.T) {
	hr := NewHRDepartment(“HR”)
	// 叶子节点 add/remove 不应 panic
	hr.add(nil)
	hr.remove(nil)

	if hr.Name() != “HR” {
		t.Errorf(“expected name 'HR', got %q”, hr.Name())
	}
}

func TestCompositeLineOfDuty(t *testing.T) {
	// 通过名称关键词验证职责描述
	hr := NewHRDepartment(“HR”)
	fin := NewFinanceDepartment(“FIN”)

	name := hr.Name()
	if !strings.Contains(name, “HR”) {
		t.Errorf(“expected HR department, got %q”, name)
	}
	if !strings.Contains(fin.Name(), “FIN”) {
		t.Errorf(“expected FIN department, got %q”, fin.Name())
	}
}
```

## 13. flyweight 享元模式

### 定义
运用共享技术有效地支持大量细粒度对象的复用。

### 使用场景
- 一个应用使用了大量的对象，造成很大的存储开销
- 对象的大多数状态可以变为外部状态
- 需要维护一组共享对象，并希望复用它们

### 经典案例
- **Go `sync.Pool`**：对象池复用临时对象，减少 GC 压力
- **Java String 常量池**：相同的字符串字面量共享同一个 String 对象
- **数据库连接池**：复用数据库连接，避免频繁创建和销毁
- **线程池**：复用线程，减少线程创建和销毁的开销

### 核心概念
- **内蕴状态（Intrinsic State）**：可共享的状态，存储在享元对象内部
- **外蕴状态（Extrinsic State）**：不可共享的状态，由客户端传入

### 代码

```go
/*
 Flyweight 享元模式：
        运用共享技术有效地支持大量细粒度的对象
 个人想法：主要思想是共享，将可以共享的部分放在对象内部，
         不可以共享的部分放在外边，享元工厂创建几个享元对象就可以了，
		这样不同的外部状态，可以针对同一个对象，给人感觉是操作多个对象，
		通过参数的形式对同一个对象的操作，像是对多个对象的操作
 日期：   20170311
*/

package test

import (
	"fmt"
)

// 享元对象接口
type IFlyweight interface {
	Operation(int) //来自外部的状态
}

// 共享对象
type ConcreteFlyweight struct {
	name string
}

func (c *ConcreteFlyweight) Operation(outState int) {
	if c == nil {
		return
	}
	fmt.Println("共享对象响应外部状态", outState)
}

// 不共享对象
type UnsharedConcreteFlyweight struct {
	name string
}

func (c *UnsharedConcreteFlyweight) Operation(outState int) {
	if c == nil {
		return
	}
	fmt.Println("不共享对象响应外部状态", outState)
}

// 享元工厂对象
type FlyweightFactory struct {
	flyweights map[string]IFlyweight
}

func (f *FlyweightFactory) Flyweight(name string) IFlyweight {
	if f == nil {
		return nil
	}
	if name == "u" {
		return &UnsharedConcreteFlyweight{"u"}
	} else if _, ok := f.flyweights[name]; !ok {
		f.flyweights[name] = &ConcreteFlyweight{name}
	}
	return f.flyweights[name]
}

func NewFlyweightFactory() *FlyweightFactory {
	ff := FlyweightFactory{make(map[string]IFlyweight)}
	ff.flyweights["a"] = &ConcreteFlyweight{"a"}
	ff.flyweights["b"] = &ConcreteFlyweight{"b"}
	return &ff
}
```

```go
package test

import (
	"testing"
)

func TestFlyweight(t *testing.T) {
	ff := NewFlyweightFactory()

	fya := ff.Flyweight("a")
	fya.Operation(1)

	fyb := ff.Flyweight("b")
	fyb.Operation(2)

	fyc := ff.Flyweight("c")
	fyc.Operation(3)

	fyd := ff.Flyweight("d")
	fyd.Operation(4)

	fyu := ff.Flyweight("u")
	fyu.Operation(5)
}
```

## 14. observer 观察者

### 定义
定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。

### 使用场景
- 一个对象的改变需要同时改变其他对象，但不知道有多少对象需要改变
- 事件驱动的系统（UI 事件、消息推送）
- 广播通信场景

### 经典案例
- **Go `context` 包**：`context.Context` 的 `Done()` 通道通知所有监听者取消事件
- **Go `sync.Cond`**：条件变量的 Broadcast() 方法通知所有等待的 goroutine
- **事件驱动架构**：消息队列中的发布-订阅模式
- **Kubernetes Informer**：监听资源变化并通过事件通知处理器

### 核心概念
- **Subject（主题）**：维护观察者列表，提供添加/删除/通知方法
- **Observer（观察者）**：定义更新接口
- **推模型 vs 拉模型**：推模型在通知时主动推送数据；拉模型让观察者按需拉取

### 代码

```go
package test

type Subject struct {
	observers []Observer
	context   string
}

func NewSubject() *Subject {
	return &Subject{
		observers: make([]Observer, 0),
	}
}

type Observer interface {
	Update(*Subject)
}

func (s *Subject) Attach(o Observer) {
	s.observers = append(s.observers, o)
}

func (s *Subject) notify() {
	for _, o := range s.observers {
		o.Update(s)
	}
}

func (s *Subject) UpdateContext(context string) {
	s.context = context
	s.notify()
}

func (s *Subject) Context() string {
	return s.context
}

type Reader struct {
	name     string
	received []string
}

func NewReader(name string) *Reader {
	return &Reader{
		name:     name,
		received: make([]string, 0),
	}
}

func (r *Reader) Update(s *Subject) {
	r.received = append(r.received, s.Context())
}

func (r *Reader) Received() []string {
	return r.received
}

func (r *Reader) Name() string {
	return r.name
}

```

```go
package test

import "testing"

func TestObserver(t *testing.T) {
	subject := NewSubject()

	r1 := NewReader("reader1")
	r2 := NewReader("reader2")
	r3 := NewReader("reader3")

	subject.Attach(r1)
	subject.Attach(r2)
	subject.Attach(r3)

	subject.UpdateContext("observer mode")

	readers := []*Reader{r1, r2, r3}
	for _, r := range readers {
		received := r.Received()
		if len(received) != 1 {
			t.Fatalf("%s expect 1 update got %d", r.Name(), len(received))
		}
		if received[0] != "observer mode" {
			t.Fatalf("%s expect observer mode got %s", r.Name(), received[0])
		}
	}
}

```

## 15. interpreter 解释器模式

### 定义
给定一个语言，定义其文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。

### 使用场景
- 需要解释执行的简单语言或表达式
- 编译器、计算器、正则表达式引擎
- 业务规则引擎中解析 DSL（领域特定语言）

### 经典案例
- **Go template 包**：`text/template` 和 `html/template` 解析模板语法
- **SQL 解析器**：将 SQL 语句解析为 AST（抽象语法树）再执行
- **数学表达式计算器**：将 `"1+2*3"` 解析为 AST 再求值
- **Lua/Python 解释器**：将源码解析为 AST 后解释执行

### 核心概念
- **终结符表达式（TerminalExpression）**：文法中的最小单元，不再包含子表达式（如数字、变量）
- **非终结符表达式（NonterminalExpression）**：由子表达式组合而成（如加减运算）
- **抽象表达式（Node）**：定义统一的 Interpret 接口

### 个人想法
解释器模式适合构建简单的 DSL 解析器。对于复杂语言，通常会先经过词法分析 → 语法分析生成 AST，而解释器模式相当于简化版的 AST 解释执行过程。

### 代码

本示例实现一个简单的整数加减法表达式计算器。

```go
package interpreter

import "strconv"

// Node 抽象表达式接口
type Node interface {
	Interpret() int
}

// NumNode 终结符表达式 — 数字
type NumNode struct {
	num int
}

func NewNumNode(s string) *NumNode {
	n, _ := strconv.Atoi(s)
	return &NumNode{num: n}
}

func (n *NumNode) Interpret() int {
	return n.num
}

// AddNode 非终结符表达式 — 加法
type AddNode struct {
	left, right Node
}

func NewAddNode(left, right Node) *AddNode {
	return &AddNode{left: left, right: right}
}

func (n *AddNode) Interpret() int {
	return n.left.Interpret() + n.right.Interpret()
}

// SubNode 非终结符表达式 — 减法
type SubNode struct {
	left, right Node
}

func NewSubNode(left, right Node) *SubNode {
	return &SubNode{left: left, right: right}
}

func (n *SubNode) Interpret() int {
	return n.left.Interpret() - n.right.Interpret()
}

// Parser 简单的解析器：将 "1+2-3" 格式的表达式解析为 AST
func Parser(input string) Node {
	tokens := tokenize(input)
	if len(tokens) == 0 {
		return &NumNode{0}
	}

	var root Node = NewNumNode(tokens[0])
	for i := 1; i < len(tokens); i += 2 {
		op := tokens[i]
		right := NewNumNode(tokens[i+1])
		switch op {
		case "+":
			root = NewAddNode(root, right)
		case "-":
			root = NewSubNode(root, right)
		}
	}
	return root
}

func tokenize(input string) []string {
	var tokens []string
	var buf []byte
	for i := 0; i < len(input); i++ {
		c := input[i]
		if c == '+' || c == '-' {
			if len(buf) > 0 {
				tokens = append(tokens, string(buf))
				buf = buf[:0]
			}
			tokens = append(tokens, string(c))
		} else {
			buf = append(buf, c)
		}
	}
	if len(buf) > 0 {
		tokens = append(tokens, string(buf))
	}
	return tokens
}
```

```go
package interpreter

import "testing"

func TestInterpreter(t *testing.T) {
	tests := []struct {
		input    string
		expected int
	}{
		{"1+2", 3},
		{"1+2+3", 6},
		{"10-3", 7},
		{"10-3-2", 5},
		{"1+2-3", 0},
		{"5-3+2", 4},
		{"0", 0},
		{"100", 100},
	}

	for _, tt := range tests {
		t.Run(tt.input, func(t *testing.T) {
			node := Parser(tt.input)
			result := node.Interpret()
			if result != tt.expected {
				t.Errorf("Parser(%q).Interpret() = %d, want %d", tt.input, result, tt.expected)
			}
		})
	}
}
```

## 16. template_method 模板方法模式

### 定义
定义一个操作中的算法的骨架，而将一些具体步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

### 使用场景
- 多个子类有公共的行为逻辑，但部分步骤的实现不同
- 需要控制子类扩展的"挂钩"操作（在模板方法的关键点提供可选的扩展点）
- 框架设计中的"好莱坞原则"——"Don't call us, we'll call you"（别打给我们，我们会打给你）

### 经典案例
- **Go `sort` 包**：`sort.Slice()` 定义了排序的算法骨架，使用者只需提供 `less` 函数
- **Spring 框架**：`JdbcTemplate`、`RestTemplate`、`TransactionTemplate` 等模板类
- **Java Servlet**：`HttpServlet` 的 `doGet()`/`doPost()` 方法，由容器调用
- **Go `database/sql`**：`sql.DB` 的 `Query()`/`Exec()` 定义连接池管理的骨架，具体 SQL 由使用者提供

### 核心概念
- **AbstractClass（抽象类）**：定义模板方法（算法骨架）和抽象步骤方法
- **ConcreteClass（具体类）**：实现抽象步骤方法，不改变算法结构

### 个人想法
模板方法模式与建造者模式的区别：模板方法是行为型模式，关注算法步骤的复用；建造者是创建型模式，关注对象的构建过程。

### 代码

本示例模拟"拿食物"的过程：打开容器 → 拿出东西 → 关闭容器。冰箱和锅具的具体操作不同，但步骤顺序相同。

```go
package template_method

import (
	"fmt"
)

type getfood interface {
	first()
	second()
	third()
}

type template struct {
	g getfood
}

func (b *template) getsomefood() {
	if b == nil {
		return
	}
	b.g.first()
	b.g.second()
	b.g.third()
}

type bingA struct {
	template
}

func NewBingA() *bingA {
	b := bingA{}
	return &b
}

func (b *bingA) first() {
	if b == nil {
		return
	}
	fmt.Println("打开冰箱")
}

func (b *bingA) second() {
	if b == nil {
		return
	}
	fmt.Println("拿出东西")
}

func (b *bingA) third() {
	if b == nil {
		return
	}
	fmt.Println("关闭冰箱")
}

type Guo struct {
	template
}

func NewGuo() *Guo {
	b := Guo{}
	return &b
}

func (b *Guo) first() {
	if b == nil {
		return
	}
	fmt.Println("打开锅")
}

func (b *Guo) second() {
	if b == nil {
		return
	}
	fmt.Println("拿出东西锅")
}

func (b *Guo) third() {
	if b == nil {
		return
	}
	fmt.Println("关闭锅")
}
```

```go
package template_method

import "testing"

func TestTemplate(t *testing.T) {
	b := NewBingA()
	b.g = b
	b.getsomefood()

	g := NewGuo()
	g.g = g
	g.getsomefood()
}

func TestTemplateBingA(t *testing.T) {
	b := NewBingA()
	b.g = b
	// 验证模板方法不会 panic
	b.getsomefood()
}

func TestTemplateGuo(t *testing.T) {
	g := NewGuo()
	g.g = g
	// 验证模板方法不会 panic
	g.getsomefood()
}
```

---

## 17. chain_of_responsibility 责任链模式

### 定义
使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。

### 使用场景
- 有多个对象可以处理一个请求，哪个对象处理由运行时决定
- 在不明确指定接收者的情况下，向多个对象中的一个提交请求
- 可以动态指定处理请求的对象集合

### 经典案例
- **Go `net/http` 中间件**：`Handle` 链中每个中间件可以决定处理请求或传递给下一个
- **Java Servlet Filter**：FilterChain 将请求沿过滤器链传递
- **审批流程**：组长 → 经理 → 总监 → CEO，逐级审批
- **日志级别**：DEBUG → INFO → WARN → ERROR，级别不够就传递给下一个

### 代码

```go
/*
 Chain Of Responsibility 职责链模式：
        使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。
        将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止

 个人想法：MFC的消息队列机制
*/
package test

import (
	"fmt"
)

const (
	constHandler = iota
	constHandlerA
	constHandlerB
)

// 处理请求接口
type IHandler interface {
	SetSuccessor(IHandler)
	HandleRequest(int) int
}

// 实现处理请求的接口的基本结构体类型
type Handler struct {
	successor IHandler // 继承者
}

func (h *Handler) SetSuccessor(i IHandler) {
	if h == nil {
		return
	}
	h.successor = i
}

// 具体处理结构体，这里简单处理int类型的请求，判断是否在[1-10]之间，是：处理，否：交给successor处理
type ConcreteHandlerA struct {
	Handler
}

func (c *ConcreteHandlerA) HandleRequest(req int) int {
	if c == nil {
		return constHandler
	}
	if req > 0 && req < 11 {
		fmt.Println("ConcreteHandlerA可以处理这个请求")
		return constHandlerA
	} else if c.successor != nil {
		return c.successor.HandleRequest(req)
	}
	return constHandler
}

func NewConcreteHandlerA() *ConcreteHandlerA {
	return &ConcreteHandlerA{}
}

// 具体处理结构体，这里简单处理int类型的请求，判断是否在[11-20]之间，是：处理，否：交给successor处理
type ConcreteHandlerB struct {
	Handler
}

func (c *ConcreteHandlerB) HandleRequest(req int) int {
	if c == nil {
		return constHandler
	}
	if req > 10 && req < 21 {
		fmt.Println("ConcreteHandlerB可以处理这个请求")
		return constHandlerB
	} else if c.successor != nil {
		return c.successor.HandleRequest(req)
	}
	return constHandler
}

func NewConcreteHandlerB() *ConcreteHandlerB {
	return &ConcreteHandlerB{}
}
```

```go
package test

import (
	"testing"
)

func TestChainOfResponsibility(t *testing.T) {
	ca := NewConcreteHandlerA()
	cb := NewConcreteHandlerB()
	ca.SetSuccessor(cb)

	var req = [][]int{{1, constHandlerA}, {4, constHandlerA}, {11, constHandlerB}, {0, constHandler}}
	for _, val := range req {
		if val[1] != ca.HandleRequest(val[0]) {
			t.Error("错误")
		}
	}
}
```

## 18. state 状态模式

### 定义
当一个对象的内在状态改变时，允许改变其行为，这个对象看起来像是改变了其类。

### 使用场景
- 对象的行为依赖于其状态，并且必须在运行时根据状态改变行为
- 代码中包含大量与状态有关的条件语句（if-else / switch）
- 工作流引擎、订单状态机、游戏角色状态

### 经典案例
- **TCP 连接状态**：ESTABLISHED、LISTEN、CLOSE_WAIT 等状态对应不同的处理行为
- **订单状态机**：待支付 → 已支付 → 已发货 → 已收货 → 已完成，各状态下的操作不同
- **游戏角色状态**：正常状态、中毒状态、无敌状态等，影响角色的行为逻辑
- **Go `net/http`**：连接的读写超时、TLS 握手等状态处理

### 核心概念
- **Context（上下文）**：持有当前状态对象，将请求委托给状态对象处理
- **State（状态接口）**：定义状态相关行为的统一接口
- **ConcreteState（具体状态）**：实现具体的状态相关行为，并决定状态转换

### 个人想法
UML 图与策略模式很相似，区别在于：
- 策略模式：客户端主动选择策略，策略之间相互独立
- 状态模式：状态之间可以自动转换，每个状态决定下一个状态

### 代码

本示例模拟工作时间段状态：不同时间（上午、中午、下午、晚上）有不同的行为，状态会自动转换。

```go
package state

import (
	"fmt"
)

// Work 工作类 -- Context
type Work struct {
	hour  int
	state State
}

func (w *Work) Hour() int {
	if w == nil {
		return -1
	}
	return w.hour
}

func (w *Work) State() State {
	if w == nil {
		return nil
	}
	return w.state
}

func (w *Work) SetHour(h int) {
	if w == nil {
		return
	}
	w.hour = h
}

func (w *Work) SetState(s State) {
	if w == nil {
		return
	}
	w.state = s
}

func (w *Work) writeProgram() {
	if w == nil {
		return
	}
	w.state.writeProgram(w)
}

func NewWork() *Work {
	state := new(morningState)
	return &Work{state: state}
}

type State interface {
	writeProgram(w *Work)
}

// morningState 上午时分状态
type morningState struct{}

func (m *morningState) writeProgram(w *Work) {
	if w.Hour() < 12 {
		fmt.Println("现在是上午时分", w.Hour())
	} else {
		w.SetState(new(NoonState))
		w.writeProgram()
	}
}

// NoonState 中午时分状态
type NoonState struct{}

func (m *NoonState) writeProgram(w *Work) {
	if w.Hour() < 13 {
		fmt.Println("现在是中午时分", w.Hour())
	} else {
		w.SetState(new(AfternoonState))
		w.writeProgram()
	}
}

// AfternoonState 下午时分状态
type AfternoonState struct{}

func (m *AfternoonState) writeProgram(w *Work) {
	if w.Hour() < 17 {
		fmt.Println("现在是下午时分", w.Hour())
	} else {
		w.SetState(new(EveningState))
		w.writeProgram()
	}
}

// EveningState 晚上时分状态
type EveningState struct{}

func (m *EveningState) writeProgram(w *Work) {
	if w.Hour() < 21 {
		fmt.Println("现在是晚上时分", w.Hour())
	} else {
		fmt.Println("现在开始睡觉", w.Hour())
	}
}
```

```go
package state

import (
	"testing"
)

func TestStateMorning(t *testing.T) {
	w := NewWork()
	w.SetHour(9)
	// 9点应该是上午状态
	if w.State() == nil {
		t.Fatal("state should not be nil")
	}
	w.writeProgram()
}

func TestStateTransition(t *testing.T) {
	tests := []struct {
		name string
		hour int
	}{
		{"morning", 9},
		{"noon", 12},
		{"afternoon", 14},
		{"evening", 18},
		{"sleep", 21},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			w := NewWork()
			w.SetHour(tt.hour)
			w.writeProgram()
		})
	}
}

func TestStateHourGetter(t *testing.T) {
	w := NewWork()
	w.SetHour(15)
	if w.Hour() != 15 {
		t.Errorf("expected hour 15, got %d", w.Hour())
	}
}

func TestNewWork(t *testing.T) {
	w := NewWork()
	if w == nil {
		t.Fatal("NewWork() returned nil")
	}
	if w.State() == nil {
		t.Error("initial state should not be nil")
	}
}
```

## 19. memento 备忘录模式

### 定义
在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可以将该对象恢复到原先保存的状态。

### 使用场景
- 需要保存和恢复对象的历史状态
- 实现撤销/重做操作
- 需要保存快照以应对故障恢复

### 经典案例
- **版本控制系统**：Git 的 commit 机制保存文件快照，可以恢复到任意提交
- **游戏存档**：保存游戏角色的状态（生命值、装备、位置）
- **IDE 撤销**：Ctrl+Z 撤销操作恢复文档到之前的状态
- **数据库事务**：回滚日志用于事务回滚

### 核心概念
- **Originator（发起人）**：需要保存状态的对象
- **Memento（备忘录）**：存储 Originator 内部状态的对象
- **Caretaker（管理者）**：负责保存和提供备忘录

### 代码

```go
/*
 Memento 备忘录模式：
        在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。
        这样以后就可以将该对象恢复到原先保存的状态
 个人想法：将某个类的状态（某些状态，具体有该类决定）保存在另外一个类中
         （代码级别：提供一个函数能够将状态保存起来，返回出去），保存好状态的类对象是管理类的成员，
		 原来的类需要恢复时，再从管理类中获取原来的状态
*/

package test

import (
	"fmt"
)

type GameRole struct {
	vit int
	atk int
	def int
}

func (g *GameRole) StateDisplay() {
	if g == nil {
		return
	}
	fmt.Println("角色当前状态：")
	fmt.Println("体力：", g.vit)
	fmt.Println("攻击：", g.atk)
	fmt.Println("防御：", g.def)
	fmt.Println("============")
}

func (g *GameRole) GetInitState() {
	if g == nil {
		return
	}
	g.vit = 100
	g.atk = 100
	g.def = 100
}

func (g *GameRole) Fight() {
	if g == nil {
		return
	}
	g.vit = 0
	g.atk = 0
	g.def = 0
}
func (g *GameRole) SaveState() RoleStateMemento {
	if g == nil {
		return RoleStateMemento{}
	}
	return RoleStateMemento{*g}
}

func (g *GameRole) RecoveryState(r RoleStateMemento) {
	if g == nil {
		return
	}
	g.vit = r.vit
	g.atk = r.atk
	g.def = r.def
}

type RoleStateMemento struct {
	GameRole
}

type RoleStateCaretaker struct {
	memento RoleStateMemento
}
```

```go
package test

import (
	"testing"
)

func TestMemento(t *testing.T) {
	gr := GameRole{}
	gr.GetInitState()
	gr.StateDisplay()

	caretaker := RoleStateCaretaker{}
	caretaker.memento = gr.SaveState()
	gr.Fight()
	gr.StateDisplay()
	gr.RecoveryState(caretaker.memento)
	gr.StateDisplay()
}
```

## 20. iterator 迭代器模式

### 定义
提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露该对象的内部表示。

### 使用场景
- 需要遍历聚合对象而不暴露其内部结构
- 需要为聚合对象提供多种遍历方式
- 为遍历不同聚合结构提供统一的接口

### 经典案例
- **Go `range` 关键字**：遍历 slice、map、channel 的语法糖
- **Go `database/sql.Rows`**：通过 `Next()`/`Scan()` 迭代查询结果
- **Java `Iterator`/`Iterable`**：所有集合类的统一遍历接口
- **Python `__iter__`/`__next__`**：通过迭代器协议实现 for-in 遍历

### 代码

```go
/*
  Iterator 迭代器模式：
        提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露该对象的内部表示
 个人想法：
 日期：   20170310
*/
package test

//"fmt"

type Book struct {
	name string
}

type Iterator interface {
	first() interface{}
	next() interface{}
}

type BookGroup struct {
	books []Book
}

func (b *BookGroup) add(newb Book) {
	if b == nil {
		return
	}
	b.books = append(b.books, newb)
}

func (b *BookGroup) createIterator() *BookIterator {
	if b == nil {
		return nil
	}
	return &BookIterator{b, 0}
}

type BookIterator struct {
	g     *BookGroup
	index int
}

func (b *BookIterator) first() interface{} {
	if b == nil {
		return nil
	}
	if len(b.g.books) > 0 {
		b.index = 0
		return b.g.books[b.index]
	}
	return nil
}

func (b *BookIterator) next() interface{} {
	if b == nil {
		return nil
	}
	if len(b.g.books) > b.index+1 {
		b.index++
		return b.g.books[b.index]
	}
	return nil
}
```

```go
package test

import (
	"fmt"
	"testing"
)

func TestIterator(t *testing.T) {
	bg := BookGroup{}
	nb := Book{"a"}
	bg.add(nb)
	nbb := Book{"b"}
	bg.add(nbb)

	it := bg.createIterator()

	for b := it.first(); b != nil; b = it.next() {
		fmt.Println(b)
	}
}
```

## 21. visitor 访问者模式

### 定义
表示一个作用于某对象结构中的各元素的操作。它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作。

### 使用场景
- 对象结构比较稳定，但经常需要在此结构上定义新的操作
- 需要对一个对象结构中的对象进行很多不同且不相关的操作
- 编译器设计中的 AST 处理

### 经典案例
- **Go `go/ast` 包**：`ast.Inspect()` 遍历 AST 节点，`ast.Visitor` 接口用于访问
- **Java `javax.lang.model.element`**：注解处理器中的 ElementVisitor
- **编译器**：对同一 AST 进行类型检查、代码生成、优化等不同操作
- **报表系统**：对同一数据结构生成 HTML、PDF、Excel 等不同格式

### 核心概念
- **Visitor（访问者）**：为每个 ConcreteElement 定义一个 Visit 方法
- **Element（元素）**：接受访问者的 Accept 方法
- **ObjectStructure（对象结构）**：管理元素集合

### 代码

```go
/*
 Visitor 访问者模式：
        表示一个作用于某对象结构中的各元素的操作，
		它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作
 个人想法：
*/

package test

import (
	"fmt"
)

// 访问接口
type IVisitor interface {
	VisitConcreteElementA(ConcreteElementA)
	VisitConcreteElementB(ConcreteElementB)
}

// 具体访问者A
type ConcreteVisitorA struct {
	name string
}

func (c *ConcreteVisitorA) VisitConcreteElementA(ce ConcreteElementA) {
	if c == nil {
		return
	}
	fmt.Println(ce.name, c.name)
	ce.OperatorA()
}

func (c *ConcreteVisitorA) VisitConcreteElementB(ce ConcreteElementB) {
	if c == nil {
		return
	}
	fmt.Println(ce.name, c.name)
	ce.OperatorB()
}

// 具体访问者B
type ConcreteVisitorB struct {
	name string
}

func (c *ConcreteVisitorB) VisitConcreteElementA(ce ConcreteElementA) {
	if c == nil {
		return
	}
	fmt.Println(ce.name, c.name)
	ce.OperatorA()
}

func (c *ConcreteVisitorB) VisitConcreteElementB(ce ConcreteElementB) {
	if c == nil {
		return
	}
	fmt.Println(ce.name, c.name)
	ce.OperatorB()
}

// 元素接口
type IElement interface {
	Accept(IVisitor)
}

// 具体元素A
type ConcreteElementA struct {
	name string
}

func (c *ConcreteElementA) Accept(visitor IVisitor) {
	if c == nil {
		return
	}
	visitor.VisitConcreteElementA(*c)
}
func (c *ConcreteElementA) OperatorA() {
	if c == nil {
		return
	}
	fmt.Println("OperatorA")
}

// 具体元素B
type ConcreteElementB struct {
	name string
}

func (c *ConcreteElementB) Accept(visitor IVisitor) {
	if c == nil {
		return
	}
	visitor.VisitConcreteElementB(*c)
}
func (c *ConcreteElementB) OperatorB() {
	if c == nil {
		return
	}
	fmt.Println("OperatorB")
}

// 维护元素集合
type ObjectStructure struct {
	list []IElement
}

func (o *ObjectStructure) Attach(e IElement) {
	if o == nil || e == nil {
		return
	}
	o.list = append(o.list, e)
}

func (o *ObjectStructure) Detach(e IElement) {
	if o == nil || e == nil {
		return
	}
	for i, val := range o.list {
		if val == e {
			o.list = append(o.list[:i], o.list[i+1:]...)
			break
		}
	}
}

func (o *ObjectStructure) Accept(v IVisitor) {
	if o == nil {
		return
	}
	for _, val := range o.list {
		val.Accept(v)
	}
}
```

```go
package test

import (
	"testing"
)

func TestVisitor(t *testing.T) {
	object := ObjectStructure{}
	object.Attach(&ConcreteElementA{"A"})
	object.Attach(&ConcreteElementB{"B"})

	cva := ConcreteVisitorA{"vA"}
	cvb := ConcreteVisitorB{"vB"}

	object.Accept(&cva)
	object.Accept(&cvb)
}
```

## 22. command 命令模式

### 定义
将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化，对请求排队或记录请求日志，以及支持可撤销的操作。

### 使用场景
- 需要将请求发送者和接收者解耦
- 需要支持请求的排队、记录、撤销操作
- 需要将多个操作组合成宏命令

### 经典案例
- **Go `net/http`**：`http.Handler` 接口封装了 HTTP 请求处理
- **Java `Runnable`**：将任务封装为 Runnable 对象，线程池执行
- **IDE 操作**：撤销/重做功能将每个操作封装为 Command
- **事务**：数据库事务将多个 SQL 封装为命令，支持批量提交或回滚
- **任务队列**：将任务封装为命令对象，放入队列异步执行

### 代码

```go
/*
 Command 命令模式：
    将一个请求封装为一个对象，
    从而使你可用不同的请求对客户进行参数化；
    对请求排队或者记录请求日志，以及支持可撤销的操作

 个人想法：Invoker维护请求队列（Command接口队列），通过一些函数可以添加、修改、执行请求队列，
         在每一种ConcreteCommand中有对该命令的执行体（Receiver），最终响应请求队列的命令
 日期：   20170310
*/

package test

import (
	"fmt"
)

// 命令接口 -- 可以保存在请求队形中，方便请求队形处理命令，具体对命令的执行体在实现这个接口的类型结构体中保存着
type Command interface {
	Run()
}

// 请求队形，保存命令列表，在ExecuteCommand函数中遍历执行命令
type Invoker struct {
	comlist []Command
}

// 添加命令
func (i *Invoker) AddCommand(c Command) {
	if i == nil {
		return
	}
	i.comlist = append(i.comlist, c)
}

// 执行命令
func (i *Invoker) ExecuteCommand() {
	if i == nil {
		return
	}
	for _, val := range i.comlist {
		val.Run()
	}
}

func NewInvoker() *Invoker {
	return &Invoker{[]Command{}}
}

// 具体命令,实现Command接口，保存一个对该命令如何处理的执行体
type ConcreteCommandA struct {
	receiver ReceiverA
}

func (c *ConcreteCommandA) SetReceiver(r ReceiverA) {
	if c == nil {
		return
	}
	c.receiver = r
}

// 具体命令的执行体
func (c *ConcreteCommandA) Run() {
	if c == nil {
		return
	}
	c.receiver.Execute()
}

func NewConcreteCommandA() *ConcreteCommandA {
	return &ConcreteCommandA{}
}

// 针对ConcreteCommand，如何处理该命令
type ReceiverA struct {
}

func (r *ReceiverA) Execute() {
	if r == nil {
		return
	}
	fmt.Println("针对ConcreteCommandA，如何处理该命令")
}

func NewReceiverA() *ReceiverA {
	return &ReceiverA{}
}

//////////////////////////////////////////////////////////

// 具体命令,实现Command接口，保存一个对该命令如何处理的执行体
type ConcreteCommandB struct {
	receiver ReceiverB
}

func (c *ConcreteCommandB) SetReceiver(r ReceiverB) {
	if c == nil {
		return
	}
	c.receiver = r
}

// 具体命令的执行体
func (c *ConcreteCommandB) Run() {
	if c == nil {
		return
	}
	c.receiver.Execute()
}

func NewConcreteCommandB() *ConcreteCommandB {
	return &ConcreteCommandB{}
}

// 针对ConcreteCommandB，如何处理该命令
type ReceiverB struct {
}

func (r *ReceiverB) Execute() {
	if r == nil {
		return
	}
	fmt.Println("针对ConcreteCommandB，如何处理该命令")
}

func NewReceiverB() *ReceiverB {
	return &ReceiverB{}
}
```

```go
package test

import (
	"testing"
)

func TestCommand(t *testing.T) {
	invoker := NewInvoker()
	concomA := NewConcreteCommandA()
	receA := NewReceiverA()

	concomA.SetReceiver(*receA)
	invoker.AddCommand(concomA)

	concomB := NewConcreteCommandB()
	receB := NewReceiverB()

	concomB.SetReceiver(*receB)
	invoker.AddCommand(concomB)

	invoker.ExecuteCommand()
}
```

## 23. mediator 中介者模式

### 定义
用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，并且可以独立地改变它们之间的交互。

### 使用场景
- 多个对象之间存在复杂的网状交互关系
- 希望将多对多的交互简化为一对多的交互（中介者一对多）
- 聊天室、机场调度、MVC 中的 Controller

### 经典案例
- **MVC 架构**：Controller 作为中介者，协调 Model 和 View 之间的交互
- **聊天室程序**：聊天服务器作为中介者，转发消息给所有客户端
- **消息队列**：消息中间件作为生产者和消费者之间的中介
- **Go channel 模式**：通过 channel 协调多个 goroutine 之间的通信

### 核心概念
- **Mediator（中介者）**：定义通信接口，协调 Colleague 之间的交互
- **ConcreteMediator（具体中介者）**：维护 Colleague 列表，实现转发逻辑
- **Colleague（同事类）**：每个同事持有中介者引用，通过中介者与其他同事通信

### 个人想法
每个对象都有一个中介者对象，发生变化时，通知中介者，由中介者判断通知其他的对象。

### 代码

本示例模拟聊天室场景：多个用户通过中介者发送消息，中介者负责将消息广播给其他用户。

```go
package mediator

import (
	"fmt"
)

// IMediator 中介者接口
type IMediator interface {
	Send(string, IColleague)
}

// Mediator 中介者基类
type Mediator struct {
}

// ConcreteMediator 具体中介者 — 维护同事列表，广播消息
type ConcreteMediator struct {
	Mediator
	colleagues []IColleague
}

func NewConcreteMediator() *ConcreteMediator {
	return &ConcreteMediator{}
}

func (m *ConcreteMediator) AddColleague(c IColleague) {
	if m == nil {
		return
	}
	m.colleagues = append(m.colleagues, c)
}

// Send 广播消息给除发送者外的所有同事
func (m *ConcreteMediator) Send(message string, c IColleague) {
	if m == nil {
		return
	}
	for _, val := range m.colleagues {
		if c == val {
			continue
		}
		val.Notify(message)
	}
}

// IColleague 同事接口
type IColleague interface {
	Send(string)
	Notify(string)
}

// Colleague 同事基类
type Colleague struct {
	mediator IMediator
}

// ConcreteColleagueA 具体同事 A
type ConcreteColleagueA struct {
	Colleague
}

func NewConcreteColleagueA(mediator IMediator) *ConcreteColleagueA {
	return &ConcreteColleagueA{Colleague{mediator}}
}

func (c *ConcreteColleagueA) Send(message string) {
	if c == nil {
		return
	}
	c.mediator.Send(message, c)
}

func (c *ConcreteColleagueA) Notify(message string) {
	if c == nil {
		return
	}
	fmt.Println("ConcreteColleagueA get message:", message)
}

// ConcreteColleagueB 具体同事 B
type ConcreteColleagueB struct {
	Colleague
}

func NewConcreteColleagueB(mediator IMediator) *ConcreteColleagueB {
	return &ConcreteColleagueB{Colleague{mediator}}
}

func (c *ConcreteColleagueB) Send(message string) {
	if c == nil {
		return
	}
	c.mediator.Send(message, c)
}

func (c *ConcreteColleagueB) Notify(message string) {
	if c == nil {
		return
	}
	fmt.Println("ConcreteColleagueB get message:", message)
}
```

```go
package mediator

import (
	"testing"
)

func TestMediator(t *testing.T) {
	m := NewConcreteMediator()
	ca := NewConcreteColleagueA(m)
	cb := NewConcreteColleagueB(m)

	m.AddColleague(ca)
	m.AddColleague(cb)

	// 验证中介者有2个同事
	if len(m.colleagues) != 2 {
		t.Errorf("expected 2 colleagues, got %d", len(m.colleagues))
	}

	// 验证发送消息不会 panic
	ca.Send("test from A")
	cb.Send("test from B")
}

func TestMediatorSendOnlyToOthers(t *testing.T) {
	m := NewConcreteMediator()
	ca := NewConcreteColleagueA(m)
	cb := NewConcreteColleagueB(m)

	m.AddColleague(ca)
	m.AddColleague(cb)

	// 验证发送者不会收到自己的消息，中介者会排除发送者自己
	m.Send("hello", ca)
	m.Send("world", cb)
}

func TestMediatorNoColleagues(t *testing.T) {
	m := NewConcreteMediator()
	// 没有同事时发送消息不应 panic
	m.Send("test", nil)
}

func TestNewConcreteMediator(t *testing.T) {
	m := NewConcreteMediator()
	if m == nil {
		t.Fatal("NewConcreteMediator() returned nil")
	}
	if len(m.colleagues) != 0 {
		t.Errorf("expected 0 colleagues, got %d", len(m.colleagues))
	}
}
```

## 24. strategy 策略模式

### 定义
定义一系列算法，将每个算法封装起来，并使它们可以互相替换。策略模式让算法的变化独立于使用算法的客户。

### 使用场景
- 一个系统需要动态地在几种算法中选择一种
- 避免使用多重的条件判断（if-else 或 switch）
- 一个类定义了多种行为，这些行为以多个条件语句的形式出现

### 经典案例
- **Java 的 Comparator 接口**：`Collections.sort()` 接受不同的 Comparator 策略来排序
- **Spring 框架**：ResourceLoader 根据资源路径前缀（classpath:/, file:/, http://）使用不同的策略加载资源
- **Go 标准库**：`crypto/tls` 中的证书验证策略、`net/http` 中的路由匹配策略
- **电商系统**：促销活动期间，根据不同的优惠规则（打折、满减、返现）计算最终价格

### 个人想法
UML图很相似，策略模式是用在对多个做同样事情（统一接口）的类对象的选择上，
而状态模式是：将对某个事情的处理过程抽象成接口和实现类的形式，
由context保存一份state，在state实现类处理事情时，修改状态传递给context，
由context继续传递到下一个状态处理中。

### 代码

本示例模拟商场收银系统，根据不同的收费策略（正常收费、打8折、满300返100）计算最终金额。

```go
package strategy

import (
	"errors"
)

// CashSuper 策略接口 -- 定义所有算法的统一接口
type CashSuper interface {
	acceptCash(money float32) float32
}

// CashNormal 正常收费策略
type CashNormal struct {
}

func (this *CashNormal) acceptCash(money float32) float32 {
	return money
}

// CashRebate 打折策略
type CashRebate struct {
	moneyRebate float32
}

func (this *CashRebate) acceptCash(money float32) float32 {
	return this.moneyRebate * money
}

// CashReturn 满减策略
type CashReturn struct {
	moneyCondition float32 // 满多少
	moneyReturn    float32 // 返多少
}

func (this *CashReturn) acceptCash(money float32) float32 {
	if money >= this.moneyCondition {
		// 注意：int(money/condition) 需要先计算满足条件的次数，再乘以返还金额
		return money - float32(int(money/this.moneyCondition))*this.moneyReturn
	}
	return money
}

// Context 上下文类，持有一个策略引用
type Context struct {
	CashSuper
}

// NewCashContext 根据策略名称创建上下文
func NewCashContext(str string) (cash *Context, err error) {
	cash = new(Context)
	switch str {
	case "正常收费":
		cash.CashSuper = &CashNormal{}
	case "满300返100":
		cash.CashSuper = &CashReturn{300, 100}
	case "打8折":
		cash.CashSuper = &CashRebate{0.8}
	default:
		err = errors.New("no match")
	}
	return cash, err
}
```

```go
package strategy

import "testing"

func TestStrategy(t *testing.T) {
	tests := []struct {
		name     string
		strategy string
		money    float32
		expected float32
	}{
		{"正常收费", "正常收费", 10000, 10000},
		{"满300返100-350", "满300返100", 350, 250},
		{"满300返100-600", "满300返100", 600, 400},
		{"满300返100-300", "满300返100", 300, 200},
		{"满300返100-299", "满300返100", 299, 299},
		{"打8折-300", "打8折", 300, 240},
		{"打8折-0", "打8折", 0, 0},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			context, err := NewCashContext(tt.strategy)
			if err != nil {
				t.Fatalf("NewCashContext(%q) unexpected error: %v", tt.strategy, err)
			}

			result := context.acceptCash(tt.money)
			if result != tt.expected {
				t.Errorf("acceptCash(%.2f) = %.2f, want %.2f", tt.money, result, tt.expected)
			}
		})
	}
}

func TestStrategyInvalid(t *testing.T) {
	_, err := NewCashContext("不存在的策略")
	if err == nil {
		t.Error("expected error for invalid strategy, got nil")
	}
}
```

