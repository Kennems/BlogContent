---
title : 'Java20天速成——进阶课程(4)'
date : 2024-04-20T22:30:13+08:00
lastmod: 2024-09-11T22:20:13+08:00
description : "Java学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Java学习笔记"]
tags : ["Java"]
# password : leetcode
---

# Java20天速成——进阶课程(4)

## 特殊文件

`properties`, `xml`

**为什么要用这些特殊文件？**

存储多个用户的：用户名， 密码， 家乡， 性别

**日志技术**

把程序运行的信息，记录到文件中，方便程序员定位bug， 并了解程序的执行情况等。

## Properties

- 是一个Map集合（键值对集合）， 但是一般不会当集合使用
- 核心作用：Properties是用来代表属性文件的，通过Properties可以读写属性文件里面的内容

```java
    public static void main(String[] args) throws IOException {
        // 创建一个Properties对象
        Properties properties = new Properties();
        
        // 输出初始的Properties对象，此时为空
        System.out.println(properties);

        // 从文件加载Properties数据
        properties.load(new FileReader("file-io-app\\properties-xml-log-app\\src\\users.properties"));
        
        // 输出加载后的Properties对象，包含从文件中读取的键值对
        System.out.println(properties);
        
        // 根据指定键取得对应的值
        System.out.println(properties.get("ken"));

        // 获取Properties对象中所有的键，并遍历输出每个键值对
        Set<String> keys = properties.stringPropertyNames();
        for (String key : keys) {
            String val = properties.getProperty(key);
            System.out.println(key + "---->" + val);
        }
    }
```

## XML

- 本质是一种数据的格式，可以用来存储复杂的数据结构，和数据关系

**XML特点**

- XML中的`<标签名>`称为一个标签或一个元素，一般是成对出现的

### xml解析(dom4j)

```java
    public static void main(String[] args) throws DocumentException {
        // 创建SAXReader对象
        SAXReader saxReader = new SAXReader();

        // 使用SAXReader解析XML文件并生成Document对象
        Document document = saxReader.read("file-io-app\\properties-xml-log-app\\src\\helloworld.xml");
        
        // 获取XML文档的根元素并输出其名称
        Element root = document.getRootElement();
        System.out.println(root.getName());

        // 获取根元素下名为"user"的所有子元素并输出它们的名称
        List<Element> elements = root.elements("user");
        for (Element element : elements) {
            System.out.println(element.getName());
        }

        // 获取名为"people"的元素的文本内容并输出
        Element people = root.element("people");
        System.out.println(people.getText());

        // 获取名为"user"的元素下名为"user"的子元素的文本内容并输出
        Element user = root.element("user");
        System.out.println(user.elementText("user"));

        // 获取名为"user"的元素的名为"id"的属性值并输出
        System.out.println(user.attributeValue("id"));

        // 获取名为"user"的元素的名为"id"的属性，并输出属性名和属性值
        Attribute id = user.attribute("id");
        System.out.println(id.getName());
        System.out.println(id.getValue());

        // 获取名为"user"的元素的所有属性，并输出它们的名和值
        List<Attribute> attributes = user.attributes();
        for (Attribute attribute : attributes) {
            System.out.println(attribute.getName() + attribute.getValue());
        }

        // 输出名为"user"的元素下名为"name"、"地址"和"password"的子元素的文本内容
        System.out.println(user.elementText("name"));
        System.out.println(user.elementText("地址"));
        System.out.println(user.elementText("password"));

        // 获取名为"user"的元素下名为"data"的子元素的文本内容，并输出包括前后空格的文本和去除前后空格的文本
        Element data = user.element("data");
        System.out.println(data.getText());
        System.out.println(data.getTextTrim()); //去除前后空格
    }
```

### 写入XML文件

使用程序把数据写出到xml中

不建议使用dom4j， 推荐直接把程序里的数据拼接成xml格式

```java
    public static void main(String[] args) throws IOException {
        // 创建一个StringBuilder对象，用于构建XML内容
        StringBuilder sb = new StringBuilder();
        
        // 构建XML头部信息和根元素
        sb.append("<?xml version=\"1.0\" encoding=\"utf-8\" ?>");
        sb.append("<book>\r\n");
        
        // 添加书名、作者和价格信息作为子元素
        sb.append("<name>").append("从入门到删库").append("</name>\r\n");
        sb.append("<author>").append("ai").append("</author>\r\n");
        sb.append("<price>").append(999.999).append("</price>\r\n");
        
        // 关闭根元素
        sb.append("</book>\r\n");

        try (
                // 使用try-with-resources语法创建BufferedWriter对象，将内容写入到文件中
                BufferedWriter bw = new BufferedWriter(new FileWriter("file-io-app\\properties-xml-log-app\\src\\test.xml"));
        ){
            bw.write(sb.toString()); // 将StringBuilder对象中的内容写入到文件中

        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

### 约束XML文件的书写

- 就是限制XML文件只能按照某种格式进行书写

约束文档：

- 专门用来显示xml书写格式的文档，比如：限制标签，属性该怎么写

约束文档的分类

- DTD文档
- Schema文档

#### XML文档结束-DTD的使用

需求：利用DTD约束文档，约束一个XML文件的编写。

**DTD**(约束文档)

```dtd
<!ELEMENT 书架 (书+)>
<!ELEMENT 书 (书名,作者,售价)>
<!ELEMENT 书名 (#PCDATA)>
<!ELEMENT 作者 (#PCDATA)>
<!ELEMENT 售价 (#PCDATA)>
```

**XML**文档

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE 书架 SYSTEM "data.dtd">
<书架>
    <书>
        <书名>从删库到跑路</书名>
        <作者>Kenn</作者>
        <售价>0.3</售价>
    </书>
</书架>
```

#### XML文档结束-schema的使用

- 可以约束XML文件的编写、和数据类型

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
        targetNamespace="http://www.itcast.cn"
        elementFormDefault="qualified" >
    <!-- targetNamespace:申明约束文档的地址（命名空间）-->
    <element name='书架'>
        <!-- 写子元素 -->
        <complexType>
            <!-- maxOccurs='unbounded': 书架下的子元素可以有任意多个！-->
            <sequence maxOccurs='unbounded'>
                <element name='书'>
                    <!-- 写子元素 -->
                    <complexType>
                        <sequence>
                            <element name='书名' type='string'/>
                            <element name='作者' type='string'/>
                            <element name='售价' type='double'/>
                        </sequence>
                    </complexType>
                </element>
            </sequence>
        </complexType>
    </element>
</schema>
```

XML

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<书架 xmlns="http://www.itcast.cn"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.itcast.cn data.xsd">
    <!-- xmlns="http://www.itcast.cn"  基本位置
         xsi:schemaLocation="http://www.itcast.cn books02.xsd" 具体的位置 -->
   <书>
       <书名>从入门到删除</书名>
       <作者>dlei</作者>
       <售价>0.03</售价>
   </书>
    <书>
        <书名>从入门到删除</书名>
        <作者>dlei</作者>
        <售价>0.9</售价>
    </书>
</书架>
```

### `Logback`快速入门

需求：

- 使用`Logback`日志框架，记录系统的运行信息

实现步骤

1. 导入`Logback`框架到项目中去

   - `slf4j-api`：日志接口
   - `logback-core`
   - `logback-classic`

2. 将`Logback`框架的核心配置文件`logback.xml`直接拷贝到`src`目录下（必须是`src`下）

3. 创建`Logback`框架提供的`Logger`对象，然后用Logger对象调用其提供的方法就可记录系统的日志信息。

   ```java
       public static final Logger LOGGER = LoggerFactory.getLogger("LogBackTest");
   ```

核心配置文件`logback.xml`

- 对`Logback`日志框架进行控制

`Logback`设置日志级别

- 日志级别指的是日志信息的类型，日志都会分级别，常见的日志级别如下（优先级依次升高）

| 日志级别 |                             说明                             |
| :------: | :----------------------------------------------------------: |
|  trace   |                    追踪，指明程序运行轨迹                    |
|  debug   |             调试，实际应用中一般将其作为最低级别             |
|   info   | 输出重要的运行信息，数据连接，网络连接，IO操作等等，使用较多 |
|   warn   |              警告信息，可能会发生问题，使用较多              |
|  error   |                      错误信息，使用较多                      |

**配置文件配置如下：**

```xml
    <root level="debug">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE" />
    </root>
```

- 只有日志的级别是大于或等于核心配置文件配值的日志级别，才会被记录，否则不记录。

## 多线程

- 线程（`Thread`）是一个程序内部的一条执行流程
- 程序中如果只有一条执行流程，那这个程序就是单线程的程序

**多线程**

- 多线程是指从软硬件上实现的多条执行流程的技术（多条线程由CPU负责调度执行）

### 方式一：继承Thread类

```java
public class MyThread extends Thread{
    @Override
    public void run() {

        for (int i = 0; i < 100; i++) {
            System.out.println("子线程MyThread" + i);
        }

    }
}
```

```java
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();

        for (int i = 0; i < 100; i++) {
            System.out.println("主线程main输出：" + i);
        }
    }
```

**优缺点：**

- 优点：编码简单
- 缺点：线程类已经继承Thread, 无法继承其他类，不利于功能的扩展

**注意事项**：

1. 调用的是`start`而不是`run`
2. 不要把主线程任务放在启动子线程之前

### 方式二：实现Runnable接口

```java
public class Demo2 {
    public static void main(String[] args) {
        MyRunnable target = new MyRunnable();
        new Thread(target).start();

        for (int i = 0; i < 1000; i++) {
            System.out.println("===主线程输出" + i);
        }
    }
}
```

```java
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 1000; i++) {
            System.out.println("子线程输出===" + i);
        }
    }
}
```

1. 定义一个线程任务类`MyRunnable`实现`Runnable`接口， 重写`run()`方法
2. 创建`MyRunnable`任务对象
3. 把`MyRunnbale`任务对象交给`Thread`处理

优缺点：

- 优点：实现`Runnable`接口后，可以继续继承其他类，实现其他接口，扩展性强
- 缺点：需要多一个`Runnable`对象

```java
    public static void main(String[] args) {
        // 创建并启动第一个子线程
        Runnable target1 = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    System.out.println("子线程输出:" + i);
                }
            }
        };
        new Thread(target1).start();

        // 主线程执行
        for (int i = 0; i < 100; i++) {
            System.out.println("主线程:" + i);
        }

        // 创建并启动第二个子线程，使用Lambda表达式
        new Thread(() -> {
            for (int i = 0; i < 100; i++) {
                System.out.println("子线程2:" + i);
            }
        }).start();

        // 主线程继续执行
        for (int i = 0; i < 100; i++) {
            System.out.println("主线程:" + i);
        }
    }
```

### 方式三：利用`Callable`接口，`FutureTask`类来实现

`MyCallable`

```java
package com.showguan.ThreadDemo;

import java.util.concurrent.Callable;

public class MyCallable implements Callable<String> {
    private int n;

    public MyCallable() {
    }

    public MyCallable(int n) {
        this.n = n;
    }

    @Override
    public String call() throws Exception {
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += i;
        }
        return "线程求和结果为" + sum;
    }
}

```

```java
package com.showguan.ThreadDemo;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class Demo4 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Callable<String> call = new MyCallable(100);
        FutureTask<String> ft = new FutureTask<>(call);
        new Thread(ft).start();
        Callable<String> call2 = new MyCallable(200);
        FutureTask<String> ft2 = new FutureTask<>(call2);
        new Thread(ft2).start();


        String rs = ft.get();
        System.out.println(rs);
        //线程求和结果为4950
        String rs2 = ft2.get();
        System.out.println(rs2);
        //线程求和结果为19900

    }
}
```

**优点：**

- 线程任务类只是实现接口，可以继续继承类和实现接口，扩展性强；可以在线程执行完毕后去获取线程执行的结果。

### 线程常用方法

- 为线程设置名称

```java
public class Main {
    public static void main(String[] args) {
        // 创建并启动线程 t1
        Thread t1 = new MyThread("线程一号");
        t1.start();
        System.out.println(t1.getName()); // 打印线程 t1 的名称

        // 创建并启动线程 t2
        Thread t2 = new MyThread("线程二号");
        t2.start();
        System.out.println(t2.getName()); // 打印线程 t2 的名称

        // 获取当前线程，并将其名称设置为 "max线程"
        Thread m = Thread.currentThread();
        m.setName("max线程");
        System.out.println(m.getName()); // 打印当前线程的名称

        // 在当前线程中执行一个简单的 for 循环，打印输出当前线程的名称和循环变量 i
        for (int i = 0; i < 100; i++) {
            System.out.println(m.getName() + "线程输出" + i);
        }
    }
}

// 自定义线程类 MyThread
package com.showguan.ThreadDemo;

public class MyThread extends Thread {
    public MyThread() {
    }

    public MyThread(String name) {
        super(name);
    }

    @Override
    public void run() {

        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + "开始执行。");
            System.out.println("子线程MyThread" + i);
        }

    }
}

```

- 使用线程的 `join()` 方法来实现线程的**顺序执行**，在循环中使用 `sleep()` 方法来控制线程的执行

```java
public static void main(String[] args) throws InterruptedException {
    // 循环遍历整数 0 到 5
    for (int i = 0; i <= 5; i++) {
        System.out.println(i); // 打印当前整数值

        // 如果当前整数值等于 3，则让当前线程休眠 5 秒钟
        if (i == 3) {
            Thread.sleep(5000);
        }
    }

    // 创建并启动线程 t1，并在主线程中等待 t1 线程执行完成
    Thread t1 = new MyThread("1号线程");
    t1.start();
    t1.join();

    // 创建并启动线程 t2，并在主线程中等待 t2 线程执行完成
    Thread t2 = new MyThread("2号线程");
    t2.start();
    t2.join();

    // 创建并启动线程 t3，并在主线程中等待 t3 线程执行完成
    Thread t3 = new MyThread("3号线程");
    t3.start();
    t3.join();
}
```

`yield`, `interrupt`, `守护线程`，`线程优先级`等...

## 线程安全

什么是线程安全问题？

- 多个线程，同时操作同一个共享资源的时候，可能会出现业务安全问题

```java
package com.showguan.ThreadSafeDemo;

public class Account {
    private String cardId;
    private double money;

    public Account() {
    }

    public Account(String cardId, double money) {
        this.cardId = cardId;
        this.money = money;
    }

    public String getCardId() {
        return cardId;
    }

    public void setCardId(String cardId) {
        this.cardId = cardId;
    }

    public double getMoney() {
        return money;
    }

    public void setMoney(double money) {
        this.money = money;
    }

    // 使用 synchronized 关键字确保线程安全
    public synchronized void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        if(this.money>=money){
            System.out.println(name + "来取钱" + money + "成功！");
            this.money -= money;
            System.out.println(name + "取钱之后，余额剩余: " + this.money);
        }else{
            System.out.println(name + "来取钱， 余额不足！");
        }
    }
}


package com.showguan.ThreadSafeDemo;

public class DrawThread extends Thread{

    private Account acc;
    public DrawThread(Account acc, String name) {
        super(name);
        this.acc = acc;
    }

    @Override
    public void run() {
        acc.drawMoney(10000.0);
    }
}



public class Demo1 {
    public static void main(String[] args) {
        Account acc = new Account("ICBC-10101", 10000);
        new DrawThread(acc, "小红").start();
        new DrawThread(acc, "小明").start();
        /**输出结果
         * 小红来取钱10000.0成功！
         * 小明来取钱， 余额不足！
         * 小红取钱之后，余额剩余: 0.0
         */
    }
}
```

### 线程同步的常见方案

- **加锁**：每次只允许一个线程加锁，加锁后才能进入访问，访问完毕后自动解锁，然后其他线程才能再加锁进来

#### (1) 同步代码块

- **作用**：把访问共享资源的核心代码给上锁，依次保证线程安全。

- **原理**：每次只允许一个线程加锁后进入，执行完毕后自动结果，其他线程才可以进来执行。

**注意事项**：

- 对于当前同时执行的线程来说，同步锁必须同一把（同一个对象），否则会出bug

**锁对象的使用规范**：

- 建议使用**共享资源**作为锁对象，对于**实例方法**建议使用`this`作为锁对象
- 对于**静态方法**建议使用字节码（`类名.class`）对象作为锁对象

```java
    public void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        synchronized (this) {
            if(this.money>=money){
                System.out.println(name + "来取钱" + money + "成功！");
                this.money -= money;
                System.out.println(name + "取钱之后，余额剩余: " + this.money);
            }else{
                System.out.println(name + "来取钱， 余额不足！");
            }
        }
    }
    public static void test(){
        synchronized(Account.class){

        }
    }
```

#### (2) 同步方法

使用`synchronized`修饰方法

```java
    public synchronized void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        if(this.money>=money){
            System.out.println(name + "来取钱" + money + "成功！");
            this.money -= money;
            System.out.println(name + "取钱之后，余额剩余: " + this.money);
        }else{
            System.out.println(name + "来取钱， 余额不足！");
        }
    }
```

**同步方法底层原理**

- 同步方法其实底层也是有隐式对象的， 只是锁的范围是整个方法代码
- 同样，如果是实例方法：同步方法默认用`this`作为锁对象；如果是静态方法：同步方法默认用类名`.class`作为锁对象

#### (3) Lock锁

```java
    private final Lock lk = new ReentrantLock();
    public void drawMoney(double money) {
        String name = Thread.currentThread().getName();
        try {
            lk.lock();
            if(this.money>=money){
                System.out.println(name + "来取钱" + money + "成功！");
                this.money -= money;
                System.out.println(name + "取钱之后，余额剩余: " + this.money);
            }else{
                System.out.println(name + "来取钱， 余额不足！");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lk.unlock();
        }
    }
```

## 线程间通信

```java
/**
 * 这个程序演示了使用 wait() 和 notify() 方法来实现多线程间的消息传递和同步。
 * 
 * 主要知识点：
 * 1. wait() 方法：使当前线程等待并释放所占锁，直到另一个线程调用 notify() 方法或 notifyAll() 方法来唤醒它。
 * 2. notify() 方法：唤醒正在等待的单个线程。
 * 3. notifyAll() 方法：唤醒正在等待的所有线程。
 * 4. 生产者-消费者模型：多个生产者线程向共享资源中生产物品，多个消费者线程从共享资源中消费物品。
 * 5. 使用 synchronized 关键字来确保线程安全，避免多个线程同时访问共享资源引起的数据不一致性问题。
 * 
 */
package com.showguan.ThreadMessageDemo;

import java.util.ArrayList;
import java.util.List;

public class Desk {
    private List<String> list = new ArrayList<>();

    /**
     * 生产食物的方法
     */
    public synchronized void put() {
        try {
            String name = Thread.currentThread().getName();
            // 如果桌子上没有食物，则生产一个食物
            if (list.size() == 0) {
                list.add(name + "做的肉包子");
                System.out.println(name + "做了一个肉包子");
                Thread.sleep(2000); // 模拟制作时间
                this.notifyAll(); // 唤醒所有等待线程
                this.wait(); // 等待被唤醒
            } else {
                this.notifyAll(); // 唤醒所有等待线程
                this.wait(); // 等待被唤醒
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 消费食物的方法
     */
    public synchronized void get() {
        try {
            String name = Thread.currentThread().getName();
            // 如果桌子上有食物，则消费一个食物
            if (list.size() == 1) {
                System.out.println(name + "吃了" + list.get(0));
                list.clear(); // 清空桌子上的食物
                Thread.sleep(1000); // 模拟吃的时间
                this.notifyAll(); // 唤醒所有等待线程
                this.wait(); // 等待被唤醒
            } else {
                this.notifyAll(); // 唤醒所有等待线程
                this.wait(); // 等待被唤醒
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 线程池

- 线程池就是一个可以复用线程的技术

`ThreadPoolExecutor`构造器

```java
public ThreadPoolExecutor(int corePoolSize,
 int maximumPoolSize,
 long keepAliveTime,
 TimeUnit unit,
 BlockingQueue<Runnable> workQueue)
```

- 参数一：`corePoolSize`：指定线程池的核心线程的数量
- 参数二：`maximumPoolSize`：指定线程池的最大线程数量
- 参数三：`keepAliveTime`：指定临时线程的存活时间
- 参数四：`unit`:指定临时线程存活的时间单位（秒，分，时，天）
- 参数五：`workQueue`：指定线程池的任务队列
- 参数六：`threadFactory`：指定线程池的线程工厂
- 参数七：`handler`：指定线程池的任务拒绝策略（线程都在忙，任务队列也满了的时候，新任务来了该怎么处理）

**注意事项**

1. **临时线程什么时候创建？**
   - 新任务提交时发现核心线程都在忙，任务队列也满了，并且还可以创建临时线程，此时才会创建临时线程
2. **什么时候会开始拒绝新任务？**
   - 核心线程和临时线程都在忙，任务队列也满了，新的任务过来的时候才会开始拒绝新任务

### 新任务拒绝策略

|                  `策略`                  |                            `解释`                            |
| :--------------------------------------: | :----------------------------------------------------------: |
|     `ThreadPoolExecutor.AbortPolicy`     | 当任务添加到线程池中被拒绝时，抛出`RejectedExecutionException`异常。（默认策略） |
|    `ThreadPoolExecutor.DiscardPolicy`    |               丢弃任务，但是不抛出异常，不推荐               |
| `ThreadPoolExecutor.DiscardOldestPolicy` |    抛弃队列中**等待最久**的任务，然后把当前任务加入队列中    |
|  `ThreadPoolExecutor.CallerRunsPolicy`   |    由主线程负责调用任务的run()方法从而绕过线程池直接执行     |

### `ExecutorService`的常用方法

|         **方法、描述**         |                           **说明**                           |
| :----------------------------: | :----------------------------------------------------------: |
|  `execute(Runnable command)`   |                       执行Runnable任务                       |
|   `submit(Callable<T> task)`   |  执行Callable任务，返回未来任务对象，用于获取线程返回的结果  |
|          `shutdown()`          |             等全部任务执行完毕后，再关闭线程池！             |
| `List<Runnable> shutdownNow()` | 立刻关闭线程池，停止正在执行的任务，并返回队列中未执行的任务 |

#### 执行Runnable任务

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        // 创建线程池
        // 使用 ThreadPoolExecutor 构造方法自定义线程池
        ExecutorService pool = new ThreadPoolExecutor(
                3, // 核心线程数
                5, // 最大线程数
                8, // 线程空闲时间
                TimeUnit.SECONDS, // 时间单位
                new ArrayBlockingQueue<>(4), // 工作队列
                Executors.defaultThreadFactory(), // 线程工厂， 用于创建新线程的实例。
                new ThreadPoolExecutor.CallerRunsPolicy() // 拒绝策略
        );

        // 创建任务
        Runnable target = new MyRunnable();

        // 提交任务给线程池执行
        pool.execute(target); // 使用核心线程执行任务
        pool.execute(target); // 使用核心线程执行任务
        pool.execute(target); // 使用核心线程执行任务

        pool.execute(target); // 复用前面的核心线程执行任务
        pool.execute(target); // 复用前面的核心线程执行任务
        pool.execute(target); // 复用前面的核心线程执行任务
        pool.execute(target); // 复用前面的核心线程执行任务

        pool.execute(target); // 使用临时线程执行任务
        pool.execute(target); // 使用临时线程执行任务

        pool.execute(target); // 执行任务被拒绝（拒绝策略决定）
        pool.execute(target); // 执行任务被拒绝（拒绝策略决定）

        // 关闭线程池
//        pool.shutdown();
//        pool.shutdownNow();
    }
}

// 自定义任务
class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 任务逻辑
    }
}
```

#### 执行Callable任务

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 创建线程池
        ExecutorService pool = new ThreadPoolExecutor(
                3, // 核心线程数
                5, // 最大线程数
                8, // 线程空闲时间
                TimeUnit.SECONDS, // 时间单位
                new ArrayBlockingQueue<>(4), // 工作队列
                Executors.defaultThreadFactory(), // 线程工厂
                new ThreadPoolExecutor.CallerRunsPolicy() // 拒绝策略
        );

        // 提交可调用任务给线程池执行
        Future<String> f1 = pool.submit(new MyCallable(100)); // 提交任务并获取 Future 对象
        Future<String> f2 = pool.submit(new MyCallable(200)); // 提交任务并获取 Future 对象
        Future<String> f3 = pool.submit(new MyCallable(300)); // 提交任务并获取 Future 对象
        Future<String> f4 = pool.submit(new MyCallable(400)); // 提交任务并获取 Future 对象

        // 获取任务执行结果并输出
        System.out.println(f1.get()); // 阻塞等待任务执行完成，并获取结果
        System.out.println(f2.get()); // 阻塞等待任务执行完成，并获取结果
        System.out.println(f3.get()); // 阻塞等待任务执行完成，并获取结果
        System.out.println(f4.get()); // 阻塞等待任务执行完成，并获取结果

        // 关闭线程池
        pool.shutdown();
    }
}

// 自定义可调用任务
class MyCallable implements Callable<String> {
    private int value;

    public MyCallable(int value) {
        this.value = value;
    }

    @Override
    public String call() throws Exception {
        // 任务逻辑
        return "Result: " + value;
    }
}
```

### `Executors`

- 是一个线程池的工具类，提供里很多静态方法用于返回不同特点的线程池对象

`Executors`使用可能存在的陷进

- 大型并发系统环境中使用`Executors`如果不注意可能会出现系统风险

> 4. 【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这
> 样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
> 说明：Executors 返回的线程池对象的弊端如下：
> 1） FixedThreadPool 和 SingleThreadPool：
> 允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM(OutOfMemoryError)。
> 2） CachedThreadPool：
> 允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。

### `volatile` 关键字

#### 作用:

1. **可见性保证** ： 当多个线程访问同一个变量时，如果使用不同的同步机制，线程可能会缓存变量的值，并不会立即从主内存中读取或写回，使用`volatile`修饰的变量，能够保证对该变量的所有写操作都会**立即**被刷新到主内存中，所有对它的读取操作也会**直接从主内存中读取**，而不是从线程的本地缓存中读取。即一个线程对volatile变量所做的写操作，对其他线程来说是立即可见的。
2. **防止指令重排**：Java编译器，JVM，和CPU在执行代码时，可能会为了提高性能而对指令进行重排序，这回导致某些操作的执行顺序和代码中的顺序不一样。volatile保证在读取或写入变量时，之前的操作一定已经完成，之后的操作一定不会被重排序到它之前。

#### 注意点：

1. **不能保证原子性**：`volatile`仅仅保证了可见性和禁止指令重排，但它并不能保证**操作的原子性**。例如，对于`volatile int`变量进行自增操作 `count ++` , 并不是线程安全的，因为自增操作实际上分为三步（读取，加一，写回），这些步骤可能被多个线程同时执行而互相干扰。
2. **适用场景**：适用于哪些需要**简单读写操作的共享变量**，比如标志位，状态变量等。如果需要更复杂的操作，最好使用`synchronized`或其他锁机制。

## 并发、并行

**进程**

- 正在运行的程序（软件）就是一个独立的进程
- 线程是属于进程的，一个进程中可以同时运行多个线程
- 进程中的多个线程其实是并发和并行执行的

**并发的含义**

- 进程中的线程是由CPU负责调度执行的，但CPU能同时处理线程的数量有限，为了保证全部线程都能往前执行， CPU会轮询为系统的每个线程服务，由于CPU切换的速度很快，给我们的感觉这些线程在同时执行，这就是并发

**并行的理解**

- 在同一个时刻上，同时有多个线程在被CPU调度执行

### 线程的生命周期

![image-20240421160224752](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240421160224752.png)

#### 线程的6种状态总结

| **状态名称**             | **说明**                                                     |
| :----------------------- | :----------------------------------------------------------- |
| NEW(新建)                | 线程刚被创建，但是并未启动                                   |
| Runnable(可运行)         | 线程已经调用了start()， 等待CPU调度                          |
| Blocked(被阻塞)          | 线程在执行的时候未竞争到锁对象，则该线程进入Blocked状态      |
| Waiting(等待)            | 一个线程进入Waiting状态，另一个线程需要Notify或者notifyAll才能够唤醒。 |
| Timed Waiting (计时等待) | 同Waiting状态类似，有几个方法（sleep， wait）有超时参数，调用他们将进入Timed Waiting状态 |
| Terminated (被终止)      | 因为run方法正常退出而死亡，或者因为没有捕获的异常终止了run方法而死亡. |

## 悲观锁、乐观锁原理

- 悲观锁：一上来就加锁，没有安全感，每次只能一个线程进入访问完毕后，再解锁，线程安全，性能较差
- 乐观锁：一开始不上锁，认为是没有问题的，大家一起跑，等要出现线程安全问题的时候才开始控制。线程安全，性能较好
  - CAS算法

```java
package com.showguan.ExtendDemo;

import java.util.concurrent.atomic.AtomicInteger;

/**
 * 这个程序演示了乐观锁的使用。
 * 
 * 乐观锁是一种并发控制机制，它假设对数据的读取操作不会造成数据冲突，因此不需要对数据进行加锁操作。
 * 相反，它在更新数据时检查是否有其他线程已经修改了数据，如果没有则进行更新，如果有则进行相应的处理，比如重试或者放弃更新。
 * 
 * 在这个程序中，我们比较了两种实现方式：
 * 1. 使用 synchronized 关键字实现的悲观锁（MyRunnable 类）
 * 2. 使用 AtomicInteger 实现的乐观锁（MyRunnable2 类）
 * 
 * MyRunnable 类中使用了 synchronized 关键字来保证多线程环境下对 count 变量的安全访问，它是一种悲观锁的实现方式，
 * 每个线程在访问 count 变量时都需要获得对象锁，因此只有一个线程能够执行临界区代码，其他线程需要等待释放锁才能执行。
 * 
 * MyRunnable2 类中使用了 AtomicInteger 来实现乐观锁。AtomicInteger 提供了一种原子性的更新操作，保证了在多线程环境下的安全访问。
 * 在这种实现方式中，线程不需要获取锁，而是直接通过原子性的操作来更新 count 变量，因此可以实现更高的并发性能。
 * 
 * 在实际应用中，选择合适的锁机制取决于具体的场景和性能需求。如果对数据的更新操作比较频繁，且竞争不是很激烈，可以选择乐观锁来提升性能；
 * 如果竞争比较激烈，或者对数据的一致性要求较高，则可以选择悲观锁来保证数据的正确性。
 */
public class Demo1 {
    public static void main(String[] args) {
//        Runnable target = new MyRunnable();
//        for (int i = 1; i <= 100; i++) {
//            new Thread(target).start();
//        }
        Runnable target = new MyRunnable2();
        for (int i = 1; i <= 100; i++) {
            new Thread(target).start();
        }
    }
}

package com.showguan.ExtendDemo;

/**
 * 实现了 Runnable 接口的类，使用 synchronized 关键字实现的悲观锁。
 */
public class MyRunnable implements Runnable{
    private int count;
    @Override
    public void run() {
        synchronized (this) {
            for (int i = 1; i <= 100; i++) {
//                System.out.println(this);
                System.out.println(Thread.currentThread().getName() + "  Count:  " + (++count));
            }
        }
    }
}

package com.showguan.ExtendDemo;

import java.util.concurrent.atomic.AtomicInteger;

/**
 * 实现了 Runnable 接口的类，使用 AtomicInteger 实现的乐观锁。
 */
public class MyRunnable2 implements Runnable{
    private AtomicInteger count = new AtomicInteger();
    @Override
    public void run() {
        for (int i = 1; i <= 100; i++) {
//                System.out.println(this);
            System.out.println(Thread.currentThread().getName() + "  Count:  " + (count.incrementAndGet()));
        }
    }
}

```

### 案例

```java
package com.showguan.Test;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class Demo1 {
    public static void main(String[] args) throws InterruptedException {
        // 创建礼物列表
        List<String> gift = new ArrayList<>();
        // 定义礼物名称数组
        String[] names = {"口红", "爱马仕", "iphone", "手表", "mac"};
        // 随机数生成器
        Random r = new Random();
        // 循环添加礼物到列表中
        for (int i = 1; i <= 1000; i++) {
            gift.add(names[r.nextInt(names.length)] + " " + (i+1)); // 在礼物列表中添加随机选取的礼物
        }
        System.out.println(gift); // 打印礼物列表

        System.out.println("-------------");

        // 创建两个发送线程，分别代表小红和小明
        SendThread xh = new SendThread(gift, "小红");
        xh.start();
        SendThread xm = new SendThread(gift, "小明");
        xm.start();

        // 等待两个发送线程结束
        xm.join();
        xh.join();

        // 打印两个发送线程发送的礼物数量
        System.out.println("小明发出了：" + xm.getCount());
        System.out.println("小红发出了：" + xh.getCount());

    }
}

```

```java
package com.showguan.Test;

import java.util.List;
import java.util.Random;

public class SendThread extends Thread {
    private List<String> gift; // 礼物列表
    private int count; // 发送的礼物数量

    // 构造方法
    public SendThread(List<String> gift, String name) {
        super(name); // 调用父类构造方法，设置线程名
        this.gift = gift; // 初始化礼物列表
    }

    @Override
    public void run() {
        Random r = new Random(); // 随机数生成器
        String name = Thread.currentThread().getName(); // 获取当前线程名
        while (true) {
            synchronized (gift){ // 同步块，确保礼物列表的线程安全
                if(gift.size()<10){ // 当礼物列表数量小于10时，结束循环
                    break;
                }
                String rs = gift.remove(r.nextInt(gift.size())); // 从礼物列表中随机选择一个礼物，并移除
                System.out.println(name + " 发出了礼品： " + rs); // 打印发送的礼物信息
                count++; // 记录发送的礼物数量
            }
        }
    }

    // 获取发送的礼物数量
    public int getCount() {
        return count;
    }

    // 设置发送的礼物数量
    public void setCount(int count) {
        this.count = count;
    }
}

```

