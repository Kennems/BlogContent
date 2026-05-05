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
	}{
		{
			name:     "Builder1",
			builder:  &Builder1{},
			expected: "123",
		},
		{
			name:     "Builder2",
			builder:  &Builder2{},
			expected: 6,
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


## bridge Bridge

```go
/*
  Bridge 桥接模式：
        将抽象部分与它的实现部分分离，使它们都可以独立地变化
 个人想法：组合/聚合复用原则

## bridge

```go
package bridge

type Software interface {
	Run() string
}

type SoftwareA struct{}
func (s *SoftwareA) Run() string { return "run soft a" }

type SoftwareB struct{}
func (s *SoftwareB) Run() string { return "run soft b" }

type Phone interface {
	SetSoft(software Software)
	Run() string
}

type PhoneA struct {
	soft Software
	name string
}
func NewPhoneA(name string) *PhoneA { return &PhoneA{name: name} }
func (p *PhoneA) SetSoft(software Software) { p.soft = software }
func (p *PhoneA) Run() string { return "PhoneA " + p.name + " run: " + p.soft.Run() }

type PhoneB struct {
	soft Software
	name string
}
func NewPhoneB(name string) *PhoneB { return &PhoneB{name: name} }
func (p *PhoneB) SetSoft(software Software) { p.soft = software }
func (p *PhoneB) Run() string { return "PhoneB " + p.name + " run: " + p.soft.Run() }
```

```go
package bridge
import "testing"
func TestBridge(t *testing.T) {
	sa := &SoftwareA{}
	pa := NewPhoneA("pa")
	pa.SetSoft(sa)
	if res := pa.Run(); res != "PhoneA pa run: run soft a" {
		t.Errorf("Unexpected result: %s", res)
	}
}
```


## chain_of_responsibility

```go
package chain_of_responsibility
import "strconv"

type Request struct {
	RequestType string
	Number      int
}

type Manager interface {
	SetSuperior(manager Manager)
	RequestApplications(request Request) string
}

type CommonManager struct {
	name     string
	superior Manager
}
func NewCommonManager(name string) *CommonManager { return &CommonManager{name: name} }
func (c *CommonManager) SetSuperior(manager Manager) { c.superior = manager }
func (c *CommonManager) RequestApplications(request Request) string {
	if request.RequestType == "请假" && request.Number <= 2 {
		return c.name + ":" + request.RequestType + " 数量" + strconv.Itoa(request.Number) + " 被批准"
	} else if c.superior != nil {
		return c.superior.RequestApplications(request)
	}
	return ""
}

type Majordomo struct {
	name     string
	superior Manager
}
func NewMajordomo(name string) *Majordomo { return &Majordomo{name: name} }
func (m *Majordomo) SetSuperior(manager Manager) { m.superior = manager }
func (m *Majordomo) RequestApplications(request Request) string {
	if request.RequestType == "请假" && request.Number <= 5 {
		return m.name + ":" + request.RequestType + " 数量" + strconv.Itoa(request.Number) + " 被批准"
	} else if m.superior != nil {
		return m.superior.RequestApplications(request)
	}
	return ""
}

type GeneralManager struct {
	name     string
	superior Manager
}
func NewGeneralManager(name string) *GeneralManager { return &GeneralManager{name: name} }
func (g *GeneralManager) SetSuperior(manager Manager) { g.superior = manager }
func (g *GeneralManager) RequestApplications(request Request) string {
	if request.RequestType == "请假" {
		return g.name + ":" + request.RequestType + " 数量" + strconv.Itoa(request.Number) + " 被批准"
	} else if request.RequestType == "加薪" && request.Number <= 500 {
		return g.name + ":" + request.RequestType + " 数量" + strconv.Itoa(request.Number) + " 被批准"
	} else if request.RequestType == "加薪" && request.Number > 500 {
		return g.name + ":" + request.RequestType + " 数量" + strconv.Itoa(request.Number) + " 再说吧"
	}
	return ""
}
```

```go
package chain_of_responsibility
import "testing"
func TestChain(t *testing.T) {
	jinli := NewCommonManager("jinli")
	zongjian := NewMajordomo("zongjian")
	zongjingli := NewGeneralManager("zongjingli")
	jinli.SetSuperior(zongjian)
	zongjian.SetSuperior(zongjingli)

	req := Request{RequestType: "请假", Number: 1}
	res := jinli.RequestApplications(req)
	if res != "jinli:请假 数量1 被批准" { t.Errorf("Unexpected res: %s", res) }
	
	req2 := Request{RequestType: "加薪", Number: 1000}
	res2 := jinli.RequestApplications(req2)
	if res2 != "zongjingli:加薪 数量1000 再说吧" { t.Errorf("Unexpected res: %s", res2) }
}
```


## command

```go
package command

type Command interface {
	Execute() string
}

type Receiver struct{}
func (r *Receiver) Action() string { return "执行请求！" }

type ConcreteCommand struct {
	receiver *Receiver
}
func NewConcreteCommand(receiver *Receiver) *ConcreteCommand {
	return &ConcreteCommand{receiver: receiver}
}
func (c *ConcreteCommand) Execute() string {
	return c.receiver.Action()
}

type Invoker struct {
	command Command
}
func (i *Invoker) SetCommand(command Command) { i.command = command }
func (i *Invoker) ExecuteCommand() string {
	if i.command != nil { return i.command.Execute() }
	return ""
}
```

```go
package command
import "testing"
func TestCommand(t *testing.T) {
	receiver := &Receiver{}
	cmd := NewConcreteCommand(receiver)
	invoker := &Invoker{}
	invoker.SetCommand(cmd)
	if res := invoker.ExecuteCommand(); res != "执行请求！" {
		t.Errorf("Unexpected result: %s", res)
	}
}
```


## composite

```go
package composite
import "strings"

type Component interface {
	Add(c Component)
	Remove(c Component)
	Display(depth int) string
}

type Leaf struct {
	name string
}
func NewLeaf(name string) *Leaf { return &Leaf{name: name} }
func (l *Leaf) Add(c Component) {}
func (l *Leaf) Remove(c Component) {}
func (l *Leaf) Display(depth int) string {
	return strings.Repeat("-", depth) + l.name
}

type Composite struct {
	name     string
	children []Component
}
func NewComposite(name string) *Composite { return &Composite{name: name, children: make([]Component, 0)} }
func (c *Composite) Add(comp Component) { c.children = append(c.children, comp) }
func (c *Composite) Remove(comp Component) {}
func (c *Composite) Display(depth int) string {
	res := strings.Repeat("-", depth) + c.name
	for _, child := range c.children {
		res += "\n" + child.Display(depth+2)
	}
	return res
}
```

```go
package composite
import "testing"
func TestComposite(t *testing.T) {
	root := NewComposite("root")
	root.Add(NewLeaf("Leaf A"))
	comp := NewComposite("Composite X")
	comp.Add(NewLeaf("Leaf XA"))
	root.Add(comp)

	res := root.Display(1)
	expected := "-root\n---Leaf A\n---Composite X\n-----Leaf XA"
	if res != expected {
		t.Errorf("Expected:\n%s\nGot:\n%s", expected, res)
	}
}
```


## decorator

```go
package decorator

type Component interface {
	Operation() string
}

type ConcreteComponent struct{}
func (c *ConcreteComponent) Operation() string { return "具体对象的操作" }

type Decorator struct {
	component Component
}
func (d *Decorator) SetComponent(c Component) { d.component = c }
func (d *Decorator) Operation() string {
	if d.component != nil {
		return d.component.Operation()
	}
	return ""
}

type ConcreteDecoratorA struct {
	Decorator
	addedState string
}
func (c *ConcreteDecoratorA) Operation() string {
	c.addedState = "New State"
	return c.Decorator.Operation() + " 具体装饰对象A的操作"
}

type ConcreteDecoratorB struct {
	Decorator
}
func (c *ConcreteDecoratorB) Operation() string {
	return c.Decorator.Operation() + " 具体装饰对象B的操作"
}
```

```go
package decorator
import "testing"
func TestDecorator(t *testing.T) {
	c := &ConcreteComponent{}
	d1 := &ConcreteDecoratorA{}
	d2 := &ConcreteDecoratorB{}

	d1.SetComponent(c)
	d2.SetComponent(d1)

	res := d2.Operation()
	expected := "具体对象的操作 具体装饰对象A的操作 具体装饰对象B的操作"
	if res != expected {
		t.Errorf("Expected: %s, Got: %s", expected, res)
	}
}
```


## flyweight

```go
package flyweight
import "strconv"

type Flyweight interface {
	Operation(extrinsicstate int) string
}

type ConcreteFlyweight struct{}
func (c *ConcreteFlyweight) Operation(extrinsicstate int) string {
	return "具体Flyweight:" + strconv.Itoa(extrinsicstate)
}

type UnsharedConcreteFlyweight struct{}
func (u *UnsharedConcreteFlyweight) Operation(extrinsicstate int) string {
	return "不共享的具体Flyweight:" + strconv.Itoa(extrinsicstate)
}

type FlyweightFactory struct {
	flyweights map[string]Flyweight
}
func NewFlyweightFactory() *FlyweightFactory {
	f := &FlyweightFactory{flyweights: make(map[string]Flyweight)}
	f.flyweights["X"] = &ConcreteFlyweight{}
	f.flyweights["Y"] = &ConcreteFlyweight{}
	f.flyweights["Z"] = &ConcreteFlyweight{}
	return f
}
func (f *FlyweightFactory) GetFlyweight(key string) Flyweight {
	return f.flyweights[key]
}
```

```go
package flyweight
import "testing"
func TestFlyweight(t *testing.T) {
	extrinsicstate := 22
	f := NewFlyweightFactory()
	fx := f.GetFlyweight("X")
	res := fx.Operation(extrinsicstate)
	if res != "具体Flyweight:22" {
		t.Errorf("Unexpected result: %s", res)
	}
}
```


## interpreter

```go
package interpreter

type Context struct {
	Input  string
	Output string
}

type AbstractExpression interface {
	Interpret(context *Context)
}

type TerminalExpression struct{}
func (t *TerminalExpression) Interpret(context *Context) {
	context.Output += "终端解释器"
}

type NonterminalExpression struct{}
func (n *NonterminalExpression) Interpret(context *Context) {
	context.Output += "非终端解释器"
}
```

```go
package interpreter
import "testing"
func TestInterpreter(t *testing.T) {
	context := &Context{}
	list := []AbstractExpression{
		&TerminalExpression{},
		&NonterminalExpression{},
		&TerminalExpression{},
	}
	for _, exp := range list {
		exp.Interpret(context)
	}
	if context.Output != "终端解释器非终端解释器终端解释器" {
		t.Errorf("Unexpected output: %s", context.Output)
	}
}
```


## iterator

```go
package iterator

type Iterator interface {
	First() interface{}
	Next() interface{}
	IsDone() bool
	CurrentItem() interface{}
}

type Aggregate interface {
	CreateIterator() Iterator
}

type ConcreteAggregate struct {
	items []interface{}
}
func NewConcreteAggregate() *ConcreteAggregate {
	return &ConcreteAggregate{items: make([]interface{}, 0)}
}
func (c *ConcreteAggregate) CreateIterator() Iterator {
	return NewConcreteIterator(c)
}
func (c *ConcreteAggregate) Count() int { return len(c.items) }
func (c *ConcreteAggregate) Get(index int) interface{} { return c.items[index] }
func (c *ConcreteAggregate) Set(index int, value interface{}) {
	if index >= len(c.items) {
		c.items = append(c.items, value)
	} else {
		c.items[index] = value
	}
}

type ConcreteIterator struct {
	aggregate *ConcreteAggregate
	current   int
}
func NewConcreteIterator(aggregate *ConcreteAggregate) *ConcreteIterator {
	return &ConcreteIterator{aggregate: aggregate, current: 0}
}
func (c *ConcreteIterator) First() interface{} { return c.aggregate.Get(0) }
func (c *ConcreteIterator) Next() interface{} {
	c.current++
	if c.current < c.aggregate.Count() {
		return c.aggregate.Get(c.current)
	}
	return nil
}
func (c *ConcreteIterator) IsDone() bool { return c.current >= c.aggregate.Count() }
func (c *ConcreteIterator) CurrentItem() interface{} { return c.aggregate.Get(c.current) }
```

```go
package iterator
import "testing"
func TestIterator(t *testing.T) {
	a := NewConcreteAggregate()
	a.Set(0, "A")
	a.Set(1, "B")
	a.Set(2, "C")

	i := a.CreateIterator()
	res := ""
	for !i.IsDone() {
		res += i.CurrentItem().(string)
		i.Next()
	}
	if res != "ABC" {
		t.Errorf("Unexpected result: %s", res)
	}
}
```


## mediator

```go
package mediator

type Mediator interface {
	Send(message string, colleague Colleague) string
}

type Colleague interface {
	Send(message string) string
	Notify(message string) string
}

type ConcreteMediator struct {
	Colleague1 Colleague
	Colleague2 Colleague
}
func (m *ConcreteMediator) Send(message string, colleague Colleague) string {
	if colleague == m.Colleague1 {
		return m.Colleague2.Notify(message)
	}
	return m.Colleague1.Notify(message)
}

type ConcreteColleague1 struct {
	mediator Mediator
}
func NewConcreteColleague1(mediator Mediator) *ConcreteColleague1 { return &ConcreteColleague1{mediator: mediator} }
func (c *ConcreteColleague1) Send(message string) string { return c.mediator.Send(message, c) }
func (c *ConcreteColleague1) Notify(message string) string { return "同事1得到信息:" + message }

type ConcreteColleague2 struct {
	mediator Mediator
}
func NewConcreteColleague2(mediator Mediator) *ConcreteColleague2 { return &ConcreteColleague2{mediator: mediator} }
func (c *ConcreteColleague2) Send(message string) string { return c.mediator.Send(message, c) }
func (c *ConcreteColleague2) Notify(message string) string { return "同事2得到信息:" + message }
```

```go
package mediator
import "testing"
func TestMediator(t *testing.T) {
	m := &ConcreteMediator{}
	c1 := NewConcreteColleague1(m)
	c2 := NewConcreteColleague2(m)
	m.Colleague1 = c1
	m.Colleague2 = c2

	res1 := c1.Send("吃饭了吗？")
	if res1 != "同事2得到信息:吃饭了吗？" { t.Errorf("Error: %s", res1) }
	res2 := c2.Send("没有呢，你打算请客？")
	if res2 != "同事1得到信息:没有呢，你打算请客？" { t.Errorf("Error: %s", res2) }
}
```


## memento

```go
package memento

type Memento struct {
	state string
}
func NewMemento(state string) *Memento { return &Memento{state: state} }
func (m *Memento) State() string { return m.state }

type Originator struct {
	state string
}
func (o *Originator) State() string { return o.state }
func (o *Originator) SetState(state string) { o.state = state }
func (o *Originator) CreateMemento() *Memento { return NewMemento(o.state) }
func (o *Originator) SetMemento(memento *Memento) { o.state = memento.State() }

type Caretaker struct {
	memento *Memento
}
func (c *Caretaker) Memento() *Memento { return c.memento }
func (c *Caretaker) SetMemento(memento *Memento) { c.memento = memento }
```

```go
package memento
import "testing"
func TestMemento(t *testing.T) {
	o := &Originator{}
	o.SetState("On")
	c := &Caretaker{}
	c.SetMemento(o.CreateMemento())

	o.SetState("Off")
	if o.State() != "Off" { t.Errorf("State should be Off") }

	o.SetMemento(c.Memento())
	if o.State() != "On" { t.Errorf("State should be On") }
}
```


## state

```go
package state
import "strconv"

type Work struct {
	hour  int
	state State
}
func NewWork() *Work { return &Work{state: &MoringState{}} }
func (w *Work) Hour() int { return w.hour }
func (w *Work) SetHour(h int) { w.hour = h }
func (w *Work) SetState(s State) { w.state = s }
func (w *Work) WriteProgram() string { return w.state.WriteProgram(w) }

type State interface {
	WriteProgram(w *Work) string
}

type MoringState struct{}
func (m *MoringState) WriteProgram(w *Work) string {
	if w.Hour() < 12 { return "上午工作，精神百倍:" + strconv.Itoa(w.Hour()) }
	w.SetState(&NoonState{})
	return w.WriteProgram()
}

type NoonState struct{}
func (n *NoonState) WriteProgram(w *Work) string {
	if w.Hour() < 13 { return "饿了，午饭；犯困，午休:" + strconv.Itoa(w.Hour()) }
	w.SetState(&AfternoonState{})
	return w.WriteProgram()
}

type AfternoonState struct{}
func (a *AfternoonState) WriteProgram(w *Work) string {
	if w.Hour() < 17 { return "下午状态还不错，继续努力:" + strconv.Itoa(w.Hour()) }
	w.SetState(&EveningState{})
	return w.WriteProgram()
}

type EveningState struct{}
func (e *EveningState) WriteProgram(w *Work) string {
	if w.Hour() < 21 { return "加班哦，疲累之极:" + strconv.Itoa(w.Hour()) }
	return "不行了，睡着了:" + strconv.Itoa(w.Hour())
}
```

```go
package state
import "testing"
func TestState(t *testing.T) {
	w := NewWork()
	w.SetHour(9)
	if res := w.WriteProgram(); res != "上午工作，精神百倍:9" { t.Errorf("Error: %s", res) }
	
	w.SetHour(13)
	if res := w.WriteProgram(); res != "下午状态还不错，继续努力:13" { t.Errorf("Error: %s", res) }
	
	w.SetHour(22)
	if res := w.WriteProgram(); res != "不行了，睡着了:22" { t.Errorf("Error: %s", res) }
}
```


## strategy

```go
package strategy

type Strategy interface {
	AlgorithmInterface() string
}

type ConcreteStrategyA struct{}
func (c *ConcreteStrategyA) AlgorithmInterface() string { return "算法A实现" }

type ConcreteStrategyB struct{}
func (c *ConcreteStrategyB) AlgorithmInterface() string { return "算法B实现" }

type ConcreteStrategyC struct{}
func (c *ConcreteStrategyC) AlgorithmInterface() string { return "算法C实现" }

type Context struct {
	strategy Strategy
}
func NewContext(strategy Strategy) *Context { return &Context{strategy: strategy} }
func (c *Context) ContextInterface() string { return c.strategy.AlgorithmInterface() }
```

```go
package strategy
import "testing"
func TestStrategy(t *testing.T) {
	context := NewContext(&ConcreteStrategyA{})
	if res := context.ContextInterface(); res != "算法A实现" { t.Errorf("Error: %s", res) }

	context = NewContext(&ConcreteStrategyB{})
	if res := context.ContextInterface(); res != "算法B实现" { t.Errorf("Error: %s", res) }
}
```


## template_method

```go
package template_method

type AbstractClass interface {
	PrimitiveOperation1() string
	PrimitiveOperation2() string
}

type Template struct {
	impl AbstractClass
}
func NewTemplate(impl AbstractClass) *Template { return &Template{impl: impl} }
func (t *Template) TemplateMethod() string {
	return t.impl.PrimitiveOperation1() + " " + t.impl.PrimitiveOperation2()
}

type ConcreteClassA struct{}
func (c *ConcreteClassA) PrimitiveOperation1() string { return "具体类A方法1实现" }
func (c *ConcreteClassA) PrimitiveOperation2() string { return "具体类A方法2实现" }

type ConcreteClassB struct{}
func (c *ConcreteClassB) PrimitiveOperation1() string { return "具体类B方法1实现" }
func (c *ConcreteClassB) PrimitiveOperation2() string { return "具体类B方法2实现" }
```

```go
package template_method
import "testing"
func TestTemplateMethod(t *testing.T) {
	c := &ConcreteClassA{}
	tmpl := NewTemplate(c)
	if res := tmpl.TemplateMethod(); res != "具体类A方法1实现 具体类A方法2实现" {
		t.Errorf("Error: %s", res)
	}

	c2 := &ConcreteClassB{}
	tmpl2 := NewTemplate(c2)
	if res := tmpl2.TemplateMethod(); res != "具体类B方法1实现 具体类B方法2实现" {
		t.Errorf("Error: %s", res)
	}
}
```


## visitor

```go
package visitor

type Visitor interface {
	VisitConcreteElementA(a *ConcreteElementA) string
	VisitConcreteElementB(b *ConcreteElementB) string
}

type Element interface {
	Accept(visitor Visitor) string
}

type ConcreteElementA struct{}
func (c *ConcreteElementA) Accept(visitor Visitor) string { return visitor.VisitConcreteElementA(c) }
func (c *ConcreteElementA) OperationA() string { return "具体元素A的操作" }

type ConcreteElementB struct{}
func (c *ConcreteElementB) Accept(visitor Visitor) string { return visitor.VisitConcreteElementB(c) }
func (c *ConcreteElementB) OperationB() string { return "具体元素B的操作" }

type ConcreteVisitor1 struct{}
func (c *ConcreteVisitor1) VisitConcreteElementA(a *ConcreteElementA) string {
	return "访问者1 访问了 " + a.OperationA()
}
func (c *ConcreteVisitor1) VisitConcreteElementB(b *ConcreteElementB) string {
	return "访问者1 访问了 " + b.OperationB()
}

type ObjectStructure struct {
	elements []Element
}
func (o *ObjectStructure) Attach(element Element) { o.elements = append(o.elements, element) }
func (o *ObjectStructure) Accept(visitor Visitor) string {
	res := ""
	for _, val := range o.elements {
		res += val.Accept(visitor) + "\n"
	}
	return res
}
```

```go
package visitor
import "testing"
func TestVisitor(t *testing.T) {
	o := &ObjectStructure{}
	o.Attach(&ConcreteElementA{})
	o.Attach(&ConcreteElementB{})

	v := &ConcreteVisitor1{}
	res := o.Accept(v)
	expected := "访问者1 访问了 具体元素A的操作\n访问者1 访问了 具体元素B的操作\n"
	if res != expected {
		t.Errorf("Expected:\n%s\nGot:\n%s", expected, res)
	}
}
```

