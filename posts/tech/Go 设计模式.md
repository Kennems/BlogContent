---
title : 'Go ——设计模式'
date : 2026-01-02T10:00:01+08:00
lastmod: 2026-01-02T10:00:01+08:00
description : "Go ——设计模式" 
image : img/cat.jpg
draft : false    
categories : ["Go"]
tags : ["GoLang"]
---

# Go ——设计模式

## simple_factory 简单工厂

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

## facade  门面模式

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

## adapter 适配器

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

## singleton 单例设计模式

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

## factory_method 工厂方法

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

## abstract_factory 抽象工厂

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

## builder 建造者

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

## prototype 原型模式

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

## proxy 代理

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

## observer 观察者

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

