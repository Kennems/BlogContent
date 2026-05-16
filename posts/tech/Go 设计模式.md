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



## interpreter 解释器模式

```go
/*
 Interpreter 解释器模式：
        给定一个语言，定义它的文法的一种表示，
		并定义一个解释器，这个解释器使用该表示来解释语言中的句子
 个人想法：
 日期：   20170310
*/

package test

import (
	"fmt"
)

type Context struct {
	text string
}

// 抽象表达式
type IAbstractExpression interface {
	Interpret(*Context)
}

// 终结符表达式
type TerminalExpression struct {
}

func (t *TerminalExpression) Interpret(context *Context) {
	if t == nil {
		return
	}
	context.text = context.text[:len(context.text)-1]
	fmt.Println(context)
}

// 非终结符表达式
type NonterminalExpression struct {
}

func (t *NonterminalExpression) Interpret(context *Context) {
	if t == nil {
		return
	}
	context.text = context.text[:len(context.text)-1]
	fmt.Println(context)
}
```

```go
package test

import (
	"testing"
)

func TestInterpreter(t *testing.T) {
	context := Context{"abc"}

	list := []IAbstractExpression{}

	list = append(list, new(TerminalExpression))
	list = append(list, new(TerminalExpression))
	list = append(list, new(NonterminalExpression))

	for _, val := range list {
		val.Interpret(&context)
	}
}
```

## template_method 模板方法模式

```go
/*
   Template Methed模板方法：
        定义一个操作中的算法的骨架，而将一些具体步骤延迟到子类中。
		模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。
 个人想法：与建造者：一个是行为型模式，一个是创建型模式
*/
package test

import (
	"fmt"
)

type getfood interface {
	first()
	secend()
	three()
}

type template struct {
	g getfood
}

func (b *template) getsomefood() {
	if b == nil {
		return
	}
	b.g.first()
	b.g.secend()
	b.g.three()
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

func (b *bingA) secend() {
	if b == nil {
		return
	}
	fmt.Println("拿出东西")
}

func (b *bingA) three() {
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

func (b *Guo) secend() {
	if b == nil {
		return
	}
	fmt.Println("拿出东西锅")
}

func (b *Guo) three() {
	if b == nil {
		return
	}
	fmt.Println("关闭锅")
}
```

```go
package test

import (
	"testing"
)

func TestTemplate(t *testing.T) {
	b := NewBingA()
	b.g = b
	b.getsomefood()

	g := NewGuo()
	g.g = g
	g.getsomefood()
}
```

## decorator 装饰器模式

```go
/*
  Decorator 装饰模式：
        动态地给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生成子类更为灵活。
 个人想法：注意装饰器内部维护一个对象，所有装饰器的子类在操作时，必须调用父类的函数，一直从下到上再到下的感觉
 日期：   20170308
*/
package test

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

type AbsstractPerson interface {
	show()
}
type Decorator struct {
	AbsstractPerson
}

func (d *Decorator) SetDecorator(component AbsstractPerson) {
	if d == nil {
		return
	}
	d.AbsstractPerson = component
}

func (d *Decorator) show() {
	if d == nil {
		return
	}
	if d.AbsstractPerson != nil {
		d.AbsstractPerson.show()
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
package test

import (
	"fmt"
	"testing"
)

func TestDecorator(t *testing.T) {
	person := &person{"hcl"}
	person.show()
	fmt.Println()
	ts := new(TShirts)
	ts.SetDecorator(person)
	ts.show()
	fmt.Println()
	bt := new(BigTrouser)
	bt.SetDecorator(ts)
	bt.show()
	fmt.Println()
	sk := new(Sneakers)
	sk.SetDecorator(bt)
	sk.show()
}
```

## chain_of_responsibility 责任链模式

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

## bridge 桥接模式

```go
/*
  Bridge 桥接模式：
        将抽象部分与它的实现部分分离，使它们都可以独立地变化
 个人想法：组合/聚合复用原则
 日期：   20170306
*/

package test

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

type TSoftware struct {
	ISoftware
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

/*func (s *SoftwareB) Run() {
	if s == nil {
		return
	}
	fmt.Println(s.name)
}*/
```

```go
package test

import (
	"fmt"
	"testing"
)

func TestBridge(t *testing.T) {
	sa := SoftwareA{Software{"a"}}
	sb := SoftwareB{Software{"b"}}

	pa := NewPhoneA("pa")
	pb := NewPhoneB("pb")

	pa.setSoft(&sa)
	pa.Run()

	pb.setSoft(&sb)
	pb.Run()

	fmt.Println()
	p := TSoftware{&sb}
	p.Run()
}
```

## memento 备忘录模式

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

## state 状态模式

```go
/*
 State 状态模式：
        当一个对象的内在状态改变时，允许改变其行为，这个对象看起来像是改变了其类
 个人想法：UML图很相似，策略模式是用在对多个做同样事情（统一接口）的类对象的选择上，
         而状态模式是：将对某个事情的处理过程抽象成接口和实现类的形式，
		由context保存一份state，在state实现类处理事情时，修改状态传递给context，
		由context继续传递到下一个状态处理中，
*/
package test

import (
	"fmt"
)

// 工作类 --context
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
	state := new(moringState)
	return &Work{state: state}
}

type State interface {
	writeProgram(w *Work)
}

// 上午时分状态类
type moringState struct {
}

func (m *moringState) writeProgram(w *Work) {
	if w.Hour() < 12 {
		fmt.Println("现在是上午时分", w.Hour())
	} else {
		w.SetState(new(NoonState))
		w.writeProgram()
	}
}

// 中午时分状态类
type NoonState struct {
}

func (m *NoonState) writeProgram(w *Work) {
	if w.Hour() < 13 {
		fmt.Println("现在是中午时分", w.Hour())
	} else {
		w.SetState(new(AfternoonState))
		w.writeProgram()
	}
}

// 下午时分状态类
type AfternoonState struct {
}

func (m *AfternoonState) writeProgram(w *Work) {
	if w.Hour() < 17 {
		fmt.Println("现在是下午时分", w.Hour())
	} else {
		w.SetState(new(EveningState))
		w.writeProgram()
	}
}

// 晚上时分状态类
type EveningState struct {
}

func (m *EveningState) writeProgram(w *Work) {
	if w.Hour() < 21 {
		fmt.Println("现在是晚上时分", w.Hour())
	} else {
		fmt.Println("现在开始睡觉", w.Hour())
	}
}
```

```go
package test

import (
	"fmt"
	"testing"
)

func TestState(t *testing.T) {
	w := NewWork()

	fmt.Println("========================")
	w.writeProgram()
	fmt.Println("========================")
	w.SetHour(12)
	w.writeProgram()
	fmt.Println("========================")
	w.SetHour(14)
	w.writeProgram()
	fmt.Println("========================")
	w.SetHour(21)
	w.writeProgram()
}
```

## composite 组合模式

```go
/*
  Composite 组合模式：
        将对象组合成树形结构，以表示“部分-整体”的层次结构。
		组合模式使得用户对单个对象和组合对象的使用具有一致性
 个人想法：
 日期：   20170308
*/

package test

import (
	"fmt"
	"strings"
)

// 公司管理接口
type Company interface {
	add(Company)
	remove(Company)
	display(int)
	lineOfDuty()
}

type RealCompany struct {
	name string
}

// 具体公司
type ConcreateCompany struct {
	RealCompany
	list []Company
}

func NewConcreateCompany(name string) *ConcreateCompany {
	return &ConcreateCompany{RealCompany{name}, []Company{}}
}

func (c *ConcreateCompany) add(newc Company) {
	if c == nil {
		return
	}
	c.list = append(c.list, newc)
}

func (c *ConcreateCompany) remove(delc Company) {
	if c == nil {
		return
	}
	for i, val := range c.list {
		if val == delc {
			c.list = append(c.list[:i], c.list[i+1:]...)
			return
		}
	}
	return
}

func (c *ConcreateCompany) display(depth int) {
	if c == nil {
		return
	}
	fmt.Println(strings.Repeat("-", depth), " ", c.name)
	for _, val := range c.list {
		val.display(depth + 2)
	}
}

func (c *ConcreateCompany) lineOfDuty() {
	if c == nil {
		return
	}

	for _, val := range c.list {
		val.lineOfDuty()
	}
}

// 人力资源部门
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
	fmt.Println(strings.Repeat("-", depth), " ", h.name)
}

func (h *HRDepartment) lineOfDuty() {
	if h == nil {
		return
	}
	fmt.Println(h.name, "员工招聘培训管理")
}

// 财务部门
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
	fmt.Println(strings.Repeat("-", depth), " ", h.name)
}

func (h *FinanceDepartment) lineOfDuty() {
	if h == nil {
		return
	}
	fmt.Println(h.name, "公司财务收支管理")
}
```

```go
package test

import (
	"fmt"
	"testing"
)

func TestComponent(t *testing.T) {
	root := NewConcreateCompany("北京总公司")
	root.add(NewHRDepartment("总公司人力资源部"))
	root.add(NewFinanceDepartment("总公司财务部"))

	compA := NewConcreateCompany("上海华东分公司")
	compA.add(NewHRDepartment("上海华东分公司人力资源部"))
	compA.add(NewFinanceDepartment("上海华东分公司财务部"))
	root.add(compA)

	compB := NewConcreateCompany("南京办事处")
	compB.add(NewHRDepartment("南京办事处人力资源部"))
	compB.add(NewFinanceDepartment("南京办事处财务部"))
	compA.add(compB)

	compC := NewConcreateCompany("杭州办事处")
	compC.add(NewHRDepartment("杭州办事处人力资源部"))
	compC.add(NewFinanceDepartment("杭州办事处财务部"))
	compA.add(compC)

	fmt.Println("结构体：")
	root.display(1)

	fmt.Println("职责：")
	root.lineOfDuty()
}
```

## iterator 迭代器模式

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

## visitor 访问者模式

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

## command 命令模式

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

## flyweight 享元模式

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

## mediator 中介者模式

```go
/*
 Mediator 中介者模式：
        用一个中介对象来封装一系列的对象交互。
        中介这使各对象不需要显式地相互引用，从而使其耦合松散，
        而且可以独立地改变它们之间的交互。
 个人想法：每个对象都有一个中介者对象，发生变化时，通知中介者，由中介者判断通知其他的对象。
*/

package test

import (
	"fmt"
)

// 中介者接口
type IMediator interface {
	Send(string, IColleague)
}

// 实现中介者接口的基本类型
type Mediator struct {
}

// 具体的中介者
type ConcreteMediator struct {
	Mediator
	colleagues []IColleague
}

func (m *ConcreteMediator) AddColleague(c IColleague) {
	if m == nil {
		return
	}
	m.colleagues = append(m.colleagues, c)
}

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

func NewConcreteMediator() *ConcreteMediator {
	return &ConcreteMediator{}
}

// 合作者接口
type IColleague interface {
	Send(string)
	Notify(string)
}

// 实现合作者接口的基本类型
type Colleague struct {
	mediator IMediator
}

// 具体合作者对象A
type ConcreteColleageA struct {
	Colleague
}

func (c *ConcreteColleageA) Notify(message string) {
	if c == nil {
		return
	}
	fmt.Println("ConcreteColleageA get message:", message)
}

func (c *ConcreteColleageA) Send(message string) {
	if c == nil {
		return
	}
	c.mediator.Send(message, c)

}
func NewConcreteColleageA(mediator IMediator) *ConcreteColleageA {
	return &ConcreteColleageA{Colleague{mediator}}
}

// 具体合作者对象B
type ConcreteColleageB struct {
	Colleague
}

func (c *ConcreteColleageB) Notify(message string) {
	if c == nil {
		return
	}
	fmt.Println("ConcreteColleageB get message:", message)
}
func (c *ConcreteColleageB) Send(message string) {
	if c == nil {
		return
	}
	c.mediator.Send(message, c)

}
func NewConcreteColleageB(mediator IMediator) *ConcreteColleageB {
	return &ConcreteColleageB{Colleague{mediator}}
}
```

```go
package test

import (
	"testing"
)

func TestMediator(t *testing.T) {
	m := NewConcreteMediator()
	ca := NewConcreteColleageA(m)
	cb := NewConcreteColleageB(m)

	m.AddColleague(ca)
	m.AddColleague(cb)

	ca.Send("ca")
	cb.Send("cb")
}
```

## strategy 策略模式

```go
/*
 Strategy 策略模式：
        它定义了算法家族，分别封装起来，让它们可以相互替换，
		此模式让算法的变化，不会影响到使用算法的客户。
 个人想法：UML图很相似，策略模式是用在对多个做同样事情（统一接口）的类对象的选择上，
         而状态模式是：将对某个事情的处理过程抽象成接口和实现类的形式，
		由context保存一份state，在state实现类处理事情时，修改状态传递给context，
		由context继续传递到下一个状态处理中，
*/
package test

import (
	"errors"
)

type CashSuper interface {
	acceptCash(memory float32) float32
}

type CashNomal struct {
}

func (this *CashNomal) acceptCash(money float32) float32 {
	return money
}

type CashRebate struct {
	meneyRebate float32
}

func (this *CashRebate) acceptCash(money float32) float32 {
	return this.meneyRebate * money
}

type CashReturn struct {
	meneyCondition float32
	meneyReturn    float32
}

func (this *CashReturn) acceptCash(money float32) float32 {
	if money >= this.meneyCondition {
		return money - float32(int(money/this.meneyCondition*this.meneyReturn))
	} else {
		return money
	}
}

type Context struct {
	CashSuper
}

func NewCashContext(str string) (cash *Context, err error) {
	cash = new(Context)
	switch str {
	case "正常收费":
		cash.CashSuper = &CashNomal{}
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
package test

import (
	"fmt"
	"testing"
)

func TestStrategy(t *testing.T) {
	context, err := NewCashContext("正常费")
	if err != nil {
		//t.Error(err)
	} else {
		result := context.acceptCash(10000)
		fmt.Println(result)
	}

	context, err = NewCashContext("满300返100")
	if err != nil {
		t.Error(err)
	} else {
		result := context.acceptCash(350)
		fmt.Println(result)
	}

	context, err = NewCashContext("打8折")
	if err != nil {
		t.Error(err)
	} else {
		result := context.acceptCash(300)
		fmt.Println(result)
	}
}
```
