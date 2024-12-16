---
title : 'Java20天速成——进阶课程(5)'
date : 2024-04-21T22:30:13+08:00
lastmod: 2022-01-08T23:33:37+08:00
description : "Java学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Java学习笔记"]
tags : ["Java"]
# password : leetcode
---

# Java20天速成——进阶课程(5)

## 网络编程

- 可以让设备中的程序与网络上其他设备中的程序进行数据交互（实现网络通信）

基本的通信架构

- 基本的通信架构有2中形式：**CS架构**（Client客户端/Server服务端）、**BS架构**（Browser浏览器/Server服务器）

无论是`CS`架构，还是`BS`架构的软件都必须要依赖网络编程

### 网络通信三要素：

#### **IP**（Internet Protocol）：

设备在网络中的地址，是唯一的标识， 全称 “互联网协议地址”， 是分配给上网设备的唯一标志。

- 两种形式：`IPv4(32bit)`, `IPv6(128bit)`
- `IPv6`分成8段表示，每段每四位编码成一个十六进制位标识，数之间用冒号(:)分开。

公网IP， 内网IP

- **公网IP**：是可以连接互联网的IP地址；
- **内网IP**：也叫局域网IP， 只能组织机构内部使用
- `192.168.` 开头的就是常见的局域网地址，范围即为`192.168.0.0--192.168.255.255`，专门位组织机构内部使用
- `127.0.0.1` 、`localhost` : 代表本机IP，只会寻找当前所在的主机

#### **端口**：

应用程序在设备中唯一的标识

- 标记正在计算机设备上运行的应用程序的，被规定为一个16位的二进制，范围是`0~65535`

##### 分类：

- **周知端口**：0~1023： 被预先定义的知名应用占用（如：HTTP占用80， FTP占用21）
- **注册端口**：1024~49151，分配给用户进程或某些应用程序
- **动态端口**：49152到65535，之所以被称为动态端口，是因为它一般不固定分配某种进程，而是动态分配

#### **协议**：

连接和数据在网络中传输的规则

### `InetAddress`

- 代表IP地址

```java
    public static void main(String[] args) throws Exception {
        // 获取本地主机的 InetAddress
        InetAddress ip1 = InetAddress.getLocalHost();
        System.out.println(ip1.getHostName()); // 输出本地主机的主机名
        System.out.println(ip1.getHostAddress()); // 输出本地主机的 IP 地址

        // 根据域名获取 InetAddress
        InetAddress ip2 = InetAddress.getByName("www.baidu.com");
        System.out.println(ip2.getHostName()); // 输出百度主机的主机名
        System.out.println(ip2.getHostAddress()); // 输出百度主机的 IP 地址

        // 检查主机是否可达（ping）
        System.out.println(ip2.isReachable(6000)); // 输出百度主机是否可达
    }
```

开放式网络互联标准：OSI网络参考模型

- OSI网络参考模型：全球网络互联标准

### 传输层

- **UDP（User Datagram Protocol）**：用户数据报协议；**TCP（Transmission Control Protocol）**：传输控制协议

#### **UDP**协议

- **特点**：无连接，不可靠通信
- 不事先建立连接，数据按照包发，一包数据包含：自己的IP，程序端口，目的地IP，程序端口和数据（限制在`64KB`内）等, 发出去就不管了。
- 发送方不管对方是否在线，数据在中间丢失也不管，如果接受方收到数据也不返回确认，所以不可靠的

##### UDP通信

```java
package com.showguan.InternetDemo;

import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketException;
import java.util.Scanner;

public class Client {
    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket(); // 创建客户端 DatagramSocket 实例

        Scanner sc = new Scanner(System.in); // 创建 Scanner 对象用于输入消息
        while (true) {
            System.out.println("请输入你需要发送的消息(exit退出)");
            String msg = sc.nextLine(); // 读取用户输入的消息
            if (msg.equals("exit")) { // 如果输入 exit，则退出循环
                System.out.println("退出成功");
                socket.close(); // 关闭客户端 Socket
                break;
            }
            byte[] bytes = msg.getBytes(); // 将消息转换为字节数组
            DatagramPacket packet = new DatagramPacket(bytes, bytes.length
                    , InetAddress.getLocalHost(), 6666); // 创建要发送的 DatagramPacket
            socket.send(packet); // 发送数据报文到服务器
        }
    }
}

```

```java
package com.showguan.InternetDemo;

import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

public class Server {
    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket(6666); // 创建服务器端 DatagramSocket 实例，监听端口 6666

        while (true) {
            byte[] buffer = new byte[1024*64]; // 创建字节数组用于存储接收的数据
            DatagramPacket packet = new DatagramPacket(buffer, buffer.length); // 创建 DatagramPacket 用于接收数据

            socket.receive(packet); // 接收客户端发送的数据报文
            System.out.println(packet.getAddress()); // 打印客户端的 IP 地址
            System.out.println(packet.getPort()); // 打印客户端的端口号

            int len = packet.getLength(); // 获取接收到的数据长度
            String s = new String(buffer, 0, len); // 将接收到的字节数组转换为字符串
            System.out.println(s); // 打印接收到的消息
            System.out.println("--------------------");
        }
    }
}

```

#### **TCP**协议

- **特点**：面向连接，可靠通信
- **TCP的最终目的**：要保证在不可靠的信道上实现可靠的传输
- TCP主要有三个步骤实现可靠传输：三次握手建立连接，传输数据进行确认，四次挥手断开连接

可靠连接：确定通信双方，收发消息都是正常无问题的（全双工）

三次握手才能确定彼此收发消息都是没问题的。

![image-20240421204937900](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240421204937900.png)

传输数据会进行确认，以保证数据传输的可靠性

![image-20240421205029391](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240421205029391.png)

##### TCP通信-支持与多个客户端同时通信

```java
package com.showguan.CaseDemo;

import java.io.DataOutputStream;
import java.io.OutputStream;
import java.net.Socket;
import java.util.Scanner;

public class Client {
    public static void main(String[] args) throws Exception {
        Socket socket = new Socket("127.0.0.1", 8888); // 创建客户端 Socket 并连接服务器
        OutputStream os = socket.getOutputStream(); // 获取 Socket 的输出流

        DataOutputStream dos = new DataOutputStream(os); // 包装输出流为 DataOutputStream
        Scanner sc = new Scanner(System.in);
        new ClientReaderThread(socket).start(); // 启动客户端读线程，用于接收服务端消息
        while (true) {
            System.out.println("请输入你需要发送的消息");
            String msg = sc.nextLine(); // 读取用户输入的消息
            if(msg.equals("exit")){ // 如果输入 exit，则退出循环
                socket.close(); // 关闭客户端 Socket
                System.out.println("成功退出通信");
                dos.close(); // 关闭输出流
                break;
            }
            dos.writeUTF(msg); // 向服务器发送消息
            dos.flush(); // 刷新输出流，确保消息发送
        }
    }
}

```

```java
package com.showguan.CaseDemo;

import java.io.DataInputStream;
import java.io.InputStream;
import java.net.Socket;

public class ClientReaderThread extends Thread {
    private Socket socket;

    public ClientReaderThread(Socket socket) {
        this.socket = socket; // 初始化 Socket
    }

    @Override
    public void run() {
        try {
            InputStream is = socket.getInputStream(); // 获取输入流
            DataInputStream dis = new DataInputStream(is); // 包装输入流为 DataInputStream
            while (true) {
                try {
                    String msg = dis.readUTF(); // 读取服务端发送的消息
                    System.out.println(msg); // 打印接收到的消息
                } catch (Exception e) {
                    System.out.println("您成功下线了！"); // 打印客户端下线信息
                    socket.close(); // 关闭 Socket 连接
                    dis.close(); // 关闭输入流
                    break; // 退出循环
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

```java
package com.showguan.CaseDemo;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
import java.util.List;

public class Server {
    public static List<Socket> onLineSockets = new ArrayList<>(); // 存储在线的客户端 Socket
    public static void main(String[] args) throws Exception {
        System.out.println("服务端启动成功！");
        ServerSocket serverSocket = new ServerSocket(8888); // 创建服务器端 ServerSocket，监听端口 8888

        while (true) {
            Socket socket = serverSocket.accept(); // 等待客户端连接
            onLineSockets.add(socket); // 添加客户端 Socket 到在线列表
            System.out.println("有人上线了！" + socket.getRemoteSocketAddress()); // 打印客户端地址信息
            ServerReaderThread serverReaderThread = new ServerReaderThread(socket); // 创建服务器端读线程
            serverReaderThread.start(); // 启动服务器端读线程处理客户端消息
        }
    }
}

```

```java
package com.showguan.CaseDemo;

import java.io.*;
import java.net.Socket;

public class ServerReaderThread extends Thread {
    private Socket socket;

    public ServerReaderThread(Socket socket) {
        this.socket = socket; // 初始化 Socket
    }

    @Override
    public void run() {
        try {
            InputStream is = socket.getInputStream(); // 获取输入流
            DataInputStream dis = new DataInputStream(is); // 包装输入流为 DataInputStream
            while (true) {
                String msg = null;
                try {
                    msg = dis.readUTF(); // 读取客户端发送的消息
                    System.out.println(msg); // 打印接收到的消息
                    sendMsgToAll(msg); // 将消息发送给所有在线客户端
                } catch (Exception e) {
                    System.out.println("用户： " + socket.getRemoteSocketAddress() + "下线了"); // 打印客户端下线信息
                    Server.onLineSockets.remove(socket); // 从在线列表中移除客户端 Socket
                    socket.close(); // 关闭 Socket 连接
                    dis.close(); // 关闭输入流
                    break; // 退出循环
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 将消息发送给所有在线客户端
    private void sendMsgToAll(String msg) throws Exception {
        for (Socket onLineSocket : Server.onLineSockets) { // 遍历在线客户端列表
            if(onLineSocket.equals(socket)){ // 排除当前客户端
                continue;
            }
            OutputStream os = onLineSocket.getOutputStream(); // 获取客户端的输出流
            DataOutputStream dos = new DataOutputStream(os); // 包装输出流为 DataOutputStream
            dos.writeUTF(msg); // 发送消息给客户端
            dos.flush(); // 刷新输出流
        }
    }
}

```

#### BS 架构

```java
import java.io.*;
import java.net.*;
import java.util.concurrent.*;

/**
 * 一个简单的基于Socket的HTTP服务器示例
 */
public class SimpleHttpServer {

    public static void main(String[] args) throws Exception {
        // 显示服务端启动成功信息
        System.out.println("服务端启动成功！");
        
        // 创建ServerSocket并监听指定端口
        ServerSocket serverSocket = new ServerSocket(8081);
        
        // 创建线程池，用于处理客户端请求
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
                12 * 2,                                      // 核心线程数
                12 * 2,                                      // 最大线程数
                0,                                           // 线程空闲超时时间
                TimeUnit.SECONDS,                            // 时间单位
                new ArrayBlockingQueue<>(8),                 // 任务队列
                Executors.defaultThreadFactory(),            // 线程工厂
                new ThreadPoolExecutor.AbortPolicy());       // 拒绝策略

        // 服务端循环监听客户端请求
        while (true) {
            Socket socket = serverSocket.accept();  // 等待客户端连接
            System.out.println("有人访问网页！" + socket.getRemoteSocketAddress());  // 显示客户端连接信息
            pool.execute(new ServerReaderThread(socket));  // 将连接交给线程池处理
        }
    }
}

/**
 * 服务器读取线程，用于处理客户端请求
 */
class ServerReaderThread extends Thread {
    private Socket socket;  // 与客户端通信的Socket对象

    /**
     * 构造方法，初始化Socket对象
     * @param socket 与客户端通信的Socket对象
     */
    public ServerReaderThread(Socket socket) {
        this.socket = socket;
    }

    /**
     * 线程运行方法，处理客户端请求
     */
    @Override
    public void run() {
        try {
            OutputStream os = socket.getOutputStream();  // 获取输出流
            PrintStream ps = new PrintStream(os);  // 使用PrintStream包装输出流，方便写入响应数据

            // HTTP响应头部信息
            ps.println("HTTP/1.1 200 OK");  // 响应状态码及状态信息
            ps.println("Content-Type:text/html;charset=UTF-8");  // 内容类型及编码
            ps.println();  // 空行，表示头部信息结束

            // HTTP响应体，返回一个简单的HTML页面
            ps.println("<div style='color:red;font-size:120px;text-align:center'>Kennem<div>");  // 红色文本
            ps.println("<html>");
            ps.println("<head>");
            ps.println("<title>Welcome</title>");
            ps.println("</head>");
            ps.println("<body>");
            ps.println("<h1 style='color:red;text-align:center;'>Welcome to my server!</h1>");
            ps.println("<p style='text-align:center;'>Here are some interesting links:</p>");
            ps.println("<ul>");
            ps.println("<li><a href='https://www.example.com'>Example Website</a></li>");
            ps.println("<li><a href='https://www.example2.com'>Another Example Website</a></li>");
            ps.println("</ul>");
            ps.println("<p style='text-align:center;'>And here's an image:</p>");
            ps.println("<div style='text-align:center;'><img src='https://th.bing.com/th/id/OIG3.ryWiFmseYX.jWcO7pmoz?pid=ImgGn' alt='Example Image'></div>");
            ps.println("</body>");
            ps.println("</html>");

            ps.close();  // 关闭输出流
        } catch (Exception e) {
            e.printStackTrace();  // 输出异常信息
        }
    }
}

```

## 单元测试

- 就是针对最小的功能单元（方法），编写测试代码对其进行正确性测试。

`Junit`单元测试框架

- 可以用来对方法进行测试，是第三方公司开源出来的

**优点**

- 可以灵活的编写测试代码，可以针对某个方法执行测试，也支持一键完成对全部方法的自动化测试，且各自独立
- 不需要程序员去分析测试的结果，会自动生成测试报告

```java
/**
 * StringUtilTest 类是用于测试 StringUtil 类中各个方法的测试类。
 * 主要测试了 StringUtil 类中 printNumber() 和 getMaxIndex() 方法的功能和正确性。
 * 
 * 测试方法说明：
 * - testprintNumber(): 测试 printNumber() 方法，验证其对输入字符串的处理是否正确。
 * - testGetMaxIndex(): 测试 getMaxIndex() 方法，验证其返回字符串中最大字母的索引是否正确。
 * 
 * 测试生命周期注解说明：
 * - @BeforeClass: 在该测试类的所有测试方法执行之前执行，用于初始化静态资源或执行一次性设置。
 *   - test11(): 测试 BeforeClass 生命周期方法，用于打印 BeforeClass 执行信息。
 * 
 * - @Before: 在每个测试方法执行之前执行，用于准备测试环境或执行某些预处理操作。
 *   - test1(): 测试 Before 生命周期方法，用于打印 Before 执行信息。
 * 
 * - @Test: 表示该方法是一个测试方法。
 *   - testprintNumber(): 测试 printNumber() 方法，验证其功能是否正确。
 *   - testGetMaxIndex(): 测试 getMaxIndex() 方法，验证其功能和返回值是否正确。
 * 
 * - @After: 在每个测试方法执行之后执行，用于清理测试环境或执行某些收尾操作。
 *   - test2(): 测试 After 生命周期方法，用于打印 After 执行信息。
 * 
 * - @AfterClass: 在该测试类的所有测试方法执行之后执行，用于执行清理操作或释放资源。
 *   - test22(): 测试 AfterClass 生命周期方法，用于打印 AfterClass 执行信息。
 * 
 * 其他说明：
 * - 使用断言机制验证 getMaxIndex() 方法的正确性。
 * - 在 testGetMaxIndex() 方法中，使用 Assert.assertEquals() 断言方法，对比预期返回值和实际返回值是否相等。
 */
public class StringUtilTest {
    /**
     * 在所有测试方法执行之前执行的静态方法，用于初始化静态资源或执行一次性设置。
     */
    @BeforeClass
    public static void test11() {
        System.out.println("---test11 beforeClass 执行了---");
    }

    /**
     * 在每个测试方法执行之前执行的方法，用于准备测试环境或执行某些预处理操作。
     */
    @Before
    public void test1() {
        System.out.println("---test1 before 执行了---");
    }

    /**
     * 测试 printNumber() 方法，验证其对输入字符串的处理是否正确。
     */
    @Test
    public void testprintNumber() {
        StringUtil.printNumber("abcdefg");
        StringUtil.printNumber(null);
    }

    /**
     * 测试 getMaxIndex() 方法，验证其返回字符串中最大字母的索引是否正确。
     */
    @Test
    public void testGetMaxIndex() {
        int idx1 = StringUtil.getMaxIndex("abcdefg");
        System.out.println(idx1);
        int idx2 = StringUtil.getMaxIndex(null);
        System.out.println(idx2);

        // 断言机制
        Assert.assertEquals("方法内部有bug", 6, idx1);
    }

    /**
     * 在每个测试方法执行之后执行的方法，用于清理测试环境或执行某些收尾操作。
     */
    @After
    public void test2() {
        System.out.println("---test2 after 执行了---");
    }

    /**
     * 在所有测试方法执行之后执行的静态方法，用于执行清理操作或释放资源。
     */
    @AfterClass
    public static void test22() {
        System.out.println("---test22 afterClass 执行了---");
    }
}

```

- 在测试方法执行前执行的方法：常用于：初始化资源
- 在测试方法执行完后再执行的方法，常用于：释放资源

在`Junit 5.xxxx`版本

```java
@Before->@BeforeEach
@After->@AfterEach
@BeforeClass->@BeforeAll
@AfterClass->@AfterAll
```

## 反射(Reflection)

- 反射：加载类，并允许以编程的方式解剖类中的各种成分（成员变量，方法，构造器等）

### 1.加载类

```java
public class Test1Class {
    public static void main(String[] args) throws Exception {
        // 1. 直接通过类名获取 Class 对象
        Class c1 = String.class;
        System.out.println(c1.getName());
        System.out.println(c1.getSimpleName());

        // 2. 通过 Class.forName() 方法根据类的全限定名获取 Class 对象
        Class c2 = Class.forName("com.showguan.reflect_demo.Student");
        System.out.println(c1 == c2);

        // 3. 通过实例对象的 getClass() 方法获取 Class 对象
        Student s = new Student();
        Class c3 = s.getClass();
        System.out.println(c1 == c3);
    }
}
```

### 2.获取构造器

```java
public class Test2Constructor {
    /**
     * 获取类的所有公共构造方法并输出构造方法的名称和参数个数。
     */
    @Test
    public void testGetConstructors(){
        Class c = Cat.class;
        Constructor[] constructors = c.getConstructors();

        for (Constructor constructor : constructors) {
            System.out.println(constructor.getName() + "--->" + constructor.getParameterCount());
        }
    }
    
    /**
     * 获取指定构造方法并通过 newInstance() 方法创建对象。
     * 使用 setAccessible(true) 方法解除私有构造方法的访问限制。
     */
    @Test
    public void testGetConstructor() throws Exception {
        Class c = Cat.class;
        
        // 获取无参构造方法
        Constructor constructor = c.getDeclaredConstructor();
        System.out.println(constructor.getName() + " " + constructor.getParameterCount());
        constructor.setAccessible(true);
        Cat cat = (Cat) constructor.newInstance();
        System.out.println(cat);
        
        // 获取带参数的构造方法
        Constructor constructor1 = c.getDeclaredConstructor(String.class, int.class);
        System.out.println(constructor1.getName() + " " + constructor1.getParameterCount());
        constructor1.setAccessible(true);
        Cat cat1 = (Cat) constructor1.newInstance("奶牛猫", 3);
        System.out.println(cat1);
    }
}
```

### 3.获取成员变量

```java
public class Test4Field {
    /**
     * 获取类的所有字段信息，并演示了如何操作类的字段。
     */
    @Test
    public void testGetFields() throws Exception {
        Class c = Cat.class;
        
        // 获取类的所有字段信息并输出字段名和类型
        Field[] fields = c.getDeclaredFields();
        for (Field field : fields) {
            System.out.println(field.getName() + "--->" + field.getType());
        }

        // 定位到特定成员变量
        Field fAge = c.getDeclaredField("age");
        System.out.println(fAge.getName() + "--->" + fAge.getType());
        Field fName = c.getDeclaredField("name");
        System.out.println(fName.getName() + "--->" + fName.getType());

        // 创建 Cat 对象，并通过反射操作字段
        Cat cat = new Cat();
        fName.setAccessible(true); // 设置私有字段可访问
        fName.set(cat, "奶牛猫"); // 设置字段值

        String name = (String) fName.get(cat); // 获取字段值
        System.out.println(name);
    }
}
```

### 4.获取类方法

```java
public class Test4Method {
    /**
     * 获取类的所有方法信息，并演示了如何通过反射调用类的方法。
     */
    @Test
    public void testGetMethods() throws Exception {
        Class c = Cat.class;

        // 获取类的所有方法信息并输出方法名、参数个数和返回类型
        Method[] methods = c.getDeclaredMethods();
        for (Method method : methods) {
            System.out.println(method.getName() + "--->"
                    + method.getParameterCount() + "--->"
                    + method.getReturnType());
        }

        // 获取特定方法信息并输出
        Method run = c.getDeclaredMethod("run");
        System.out.println(run.getName() + "--->"
                + run.getParameterCount() + "--->" +
                run.getReturnType());

        Method eat = c.getDeclaredMethod("eat", String.class);
        System.out.println(eat.getName() + "--->"
                + eat.getParameterCount() + "--->" +
                eat.getReturnType());

        // 创建 Cat 对象，并通过反射调用方法
        Cat cat = new Cat();

        run.setAccessible(true); // 设置私有方法可访问
        Object invoke = run.invoke(cat); // 调用方法
        System.out.println(invoke);

        eat.setAccessible(true); // 设置私有方法可访问
        Object invoke1 = eat.invoke(cat, "鱼饼干"); // 调用方法
        System.out.println(invoke1);
    }
}
```

### 案例

```java
public class ObjectFrame {
    /**
     * 将对象信息保存到文件。
     * 
     * @param obj 要保存信息的对象
     * @throws Exception 可能抛出的异常
     */
    public static void saveObject(Object obj) throws Exception {
        // 创建 PrintStream 对象，用于写入文件
        PrintStream ps = new PrintStream(new FileOutputStream("junit-reflect-annotation-proxy-app\\src\\com\\showguan\\reflect_demo\\data.txt", true));
        Class c = obj.getClass();
        String cName = c.getSimpleName();
        ps.println("--------------" + cName + "-----------------");

        // 获取对象的所有字段信息，并写入文件
        Field[]  fields = c.getDeclaredFields();
        for (Field field : fields) {
            String name = field.getName();
            field.setAccessible(true); // 设置字段可访问
            String value = field.get(obj) + ""; // 获取字段值
            ps.println(name + " " + value);
        }
        ps.close(); // 关闭流
    }
}
```

```java
/**
 * Test5 类是一个用于测试 ObjectFrame 类的主程序。
 * 主要创建了一个 Student 对象和一个 Teacher 对象，并将它们的信息保存到文件中。
 */
package com.showguan.reflect_demo;

public class Test5 {
    public static void main(String[] args) throws Exception{
        // 创建 Student 对象和 Teacher 对象
        Student s1 = new Student("Kennem", '男', 21, 181.2, "lelele");
        Teacher t1 = new Teacher("tytytyt", 21000);

        // 将对象信息保存到文件中
        ObjectFrame.saveObject(s1);
        ObjectFrame.saveObject(t1);
    }
}

```

## 注解（Annotation）

- 就是Java代码里的特殊标记，比如@Override, @Test等，作用是：让其他程序根据注解信息来决定怎么执行该程序
- 注意：注解可以用在类上，构造器上，方法上，成员变量上，参数上，等位置

### 自定义注解

```java
/**
 * MyTest1 注解是一个自定义注解，具有三个属性：
 * - a：字符串类型属性。
 * - b：布尔类型属性，默认值为 true。
 * - c：字符串数组类型属性。
 */
package com.showguan.annotation_demo;

public @interface MyTest1 {
    String a();
    boolean b() default true;
    String[] c();
}

/**
 * MyTest2 注解是一个自定义注解，具有两个属性：
 * - value：字符串类型属性。
 * - age：整数类型属性，默认值为 23。
 * 如果注解中只有一个 value 属性，使用注解时，value 名称可以不写。
 */
package com.showguan.annotation_demo;

public @interface MyTest2 {
    // 特殊属性名
    // 如果注解中只有一个value属性，使用注解时，value名称可以不写
    String value();
    int age() default 23;
}

/**
 * AnnotationTest1 类是一个演示注解的使用的示例程序。
 * 使用了 MyTest1 和 MyTest2 两个自定义注解。
 */
package com.showguan.annotation_demo;

@MyTest1(a="Kennem", c={"HTML", "CSS"})
public class AnnotationTest1 {
    /**
     * 使用了 MyTest1 注解，并指定了各属性的值。
     */
    @MyTest1(a="tytyt", b=false, c={"python", "c++"})
    public static void main(String[] args) {

    }

    /**
     * 使用了 MyTest2 注解，并指定了 value 属性的值。
     */
    @MyTest2("value")
    public static void test1(){

    }
}

```

### 元注解

- 修饰注解的注解

![image-20240422130006659](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240422130006659.png)

```java
/**
 * MyTest3 注解是一个自定义注解，用于标记类、方法和字段。
 * 它具有以下特性：
 * - @Target({ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})：指定该注解可以用于类、方法和字段上。
 * - @Retention(RetentionPolicy.RUNTIME)：声明注解的保留周期为运行时，使得该注解在运行时可以通过反射获取到。
 */
package com.showguan.annotation_demo;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyTest3 {
}

/**
 * AnnotationTest2 类是一个演示注解的使用的示例程序。
 * 使用了 MyTest3 注解，并标记了类、字段和方法。
 */
package com.showguan.annotation_demo;

import org.junit.Test;

@MyTest3
public class AnnotationTest2 {
    /**
     * 使用了 MyTest3 注解，标记了字段。
     */
    @MyTest3
    private int test;

    /**
     * 使用了 MyTest3 注解，标记了方法。
     */
    @MyTest3
    @Test
    public void m() {

    }
}

```

注解的解析

- 就是判断类上，方法上，成员变量上是否存在注解，并把注解里的内容给解析出来

### 解析注解

```java
package com.showguan.annotation_demo;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// 定义一个自定义注解 MyTest4
@Target({ElementType.TYPE, ElementType.METHOD}) // 可以标记在类和方法上
@Retention(RetentionPolicy.RUNTIME) // 注解信息在运行时保留
public @interface MyTest4 {
    String value(); // 声明一个属性 value，类型为 String
    double aaa() default 100; // 声明一个属性 aaa，类型为 double，默认值为 100
    String[] bbb(); // 声明一个属性 bbb，类型为 String 数组
}

package com.showguan.annotation_demo;

// 使用自定义注解 MyTest4 标记类和方法
@MyTest4(value = "Kennem", aaa = 10.0, bbb = {"至尊宝", "tytt"})
public class Demo {
    // 使用自定义注解 MyTest4 标记方法
    @MyTest4(value = "yl", aaa = 199.2, bbb = {"best", "牛夫人"})
    public void test1(){

    }
}

package com.showguan.annotation_demo;

import org.junit.Test;

import java.lang.annotation.Annotation;
import java.lang.reflect.Method;
import java.util.Arrays;

// 测试解析自定义注解 MyTest4
public class AnnotationTest3 {
    // 解析类上的自定义注解
    @Test
    public void parseClass(){
        Class c = Demo.class;

        if(c.isAnnotationPresent(MyTest4.class)){
            MyTest4 myTest4 =
                    (MyTest4) c.getDeclaredAnnotation(MyTest4.class);

            System.out.println(myTest4.value());
            System.out.println(myTest4.aaa());
            System.out.println(Arrays.toString(myTest4.bbb()));
        }
    }
    
    // 解析方法上的自定义注解
    @Test
    public void parseMethod() throws Exception{
        Class c = Demo.class;
        Method m = c.getDeclaredMethod("test1");
        if(m.isAnnotationPresent(MyTest4.class)){
            MyTest4 myTest4 =
                    (MyTest4) m.getDeclaredAnnotation(MyTest4.class);

            System.out.println(myTest4.value());
            System.out.println(myTest4.aaa());
            System.out.println(Arrays.toString(myTest4.bbb()));
        }
    }
}
```

## 模拟Junit框架

```java
package com.showguan.annotation_demo;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// 定义一个自定义注解 MyTest，用于标记方法
@Target(ElementType.METHOD) // 可以标记在方法上
@Retention(RetentionPolicy.RUNTIME) // 注解信息在运行时保留
public @interface MyTest {

}

package com.showguan.annotation_demo;

import org.junit.Test;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

// 测试自定义注解 MyTest
public class AnnotationTest4 {
    // 标记为 MyTest 的方法
    @MyTest
    public void test1(){
        System.out.println("---test1---");
    }
    // 标记为 MyTest 的方法
    @MyTest
    public void test2(){
        System.out.println("---test2---");
    }
    // 没有标记为 MyTest 的方法
    public void test3(){
        System.out.println("---test3---");
    }
    // 没有标记为 MyTest 的方法
    public void test4(){
        System.out.println("---test4---");
    }
    
    // 主方法用于运行测试标记为 MyTest 的方法
    public static void main(String[] args) throws Exception{
        AnnotationTest4 a = new AnnotationTest4();

        Class c = AnnotationTest4.class;
        Method[] methods = c.getDeclaredMethods();

        for (Method method : methods) {
            if(method.isAnnotationPresent(MyTest.class)){
                method.invoke(a); // 调用标记为 MyTest 的方法
            }
        }
    }
}

```

## 动态代理



#### Proxy类

```java
package com.showguan.proxy_demo;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

// 代理模式示例
public class ProxyUtil {
    // 创建代理对象的方法
    public static Star createProxy(BigStar bigStar) {
        // 使用Java的动态代理创建代理对象
        Star starProxy = (Star) Proxy.newProxyInstance(
            ProxyUtil.class.getClassLoader(), // 使用当前类的类加载器加载代理类
            new Class[]{Star.class}, // 指定代理对象实现的接口，这里是Star接口
            new InvocationHandler() { // 设置代理对象的调用处理器，即InvocationHandler
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    /**
                         * Object proxy：代理对象，用于调用被代理对象的方法或执行其他操作。
                         * Method method：表示被调用的方法，允许获取方法的相关信息或调用该方法。
                         * Object[] args：方法的参数数组，包含了被调用方法的参数列表，允许获取和处理参数。
                         */
                    // 对代理对象的方法进行拦截和增强
                    if (method.getName().equals("sing")) { // 如果是sing方法
                        System.out.println("准备话筒，收钱20万"); // 增强处理：准备话筒
                        return method.invoke(bigStar, args); // 调用原始对象的方法
                    } else if (method.getName().equals("dance")) { // 如果是dance方法
                        System.out.println("准备场地，收钱1000万"); // 增强处理：准备场地
                        return method.invoke(bigStar, args); // 调用原始对象的方法
                    }
                    return method.invoke(bigStar, args); // 对于其他方法，直接调用原始对象的方法
                }
            });
        return starProxy; // 返回创建的代理对象
    }
}

```

#### Star接口

```java
package com.showguan.proxy_demo;

// 明星接口，定义了唱歌和跳舞的方法
public interface Star {
    String sing(String name); // 唱歌方法，传入歌曲名字，返回感谢信息
    void dance(); // 跳舞方法
}

```

#### BigStar类

```java
package com.showguan.proxy_demo;

// 具体的明星类，实现了Star接口
public class BigStar implements Star {
    private String name; // 明星的姓名

    // 构造方法，传入明星的姓名
    public BigStar(String name) {
        this.name = name;
    }

    // 获取明星姓名的方法
    public String getName() {
        return name;
    }

    // 设置明星姓名的方法
    public void setName(String name) {
        this.name = name;
    }

    // 实现唱歌方法，传入歌曲名字，打印出歌手名字和歌曲名字，并返回感谢信息
    @Override
    public String sing(String name) {
        System.out.println(this.name + "正在唱歌:" + name);
        return "感谢大家";
    }

    // 实现跳舞方法，打印出歌手名字并提示正在跳舞
    @Override
    public void dance() {
        System.out.println(this.name + "正在优美的跳舞");
    }
}

```

#### Test类

```java
package com.showguan.proxy_demo;

// 测试类
public class Test {
    public static void main(String[] args) {
        BigStar s = new BigStar("ycy"); // 创建一个具体的明星对象
        Star starProxy = ProxyUtil.createProxy(s); // 使用代理工具类创建代理对象

        String rs = starProxy.sing("卡路里"); // 调用代理对象的唱歌方法
        System.out.println(rs); // 打印唱歌方法的返回值

        starProxy.dance(); // 调用代理对象的跳舞方法
    }
}

```

### 使用案例

#### 接口

```java
package com.showguan.proxy_demo2;

// 用户服务接口，定义了登录、删除用户和查询用户的方法
public interface UserService {
    void login(String loginName, String passWrod) throws Exception; // 登录方法，传入登录名和密码
    void deleteUsers() throws Exception; // 删除用户方法
    String[] selectUsers() throws Exception; // 查询用户方法，返回用户数组
}

```

#### 接口实现

```java
package com.showguan.proxy_demo2;

// 用户服务实现类，实现了UserService接口
public class UserServiceImpl implements UserService {

    // 实现登录方法，验证用户名密码并输出登录成功或失败信息
    @Override
    public void login(String loginName, String passWrod) throws Exception {
        if ("admin".equals(loginName) && passWrod.equals("123456")) {
            System.out.println("登录成功！");
        } else {
            System.out.println("用户名或密码错误！");
        }
        Thread.sleep(1000); // 模拟登录操作耗时
    }

    // 实现删除用户方法，输出删除成功信息
    @Override
    public void deleteUsers() throws Exception {
        System.out.println("成功删除了10000个用户");
        Thread.sleep(1000); // 模拟删除操作耗时
    }

    // 实现查询用户方法，输出查询结果并返回用户数组
    @Override
    public String[] selectUsers() throws Exception {
        System.out.println("查询出三个用户：");
        String[] names = {"kenene", "Kennem", "kkk"};
        Thread.sleep(1000); // 模拟查询操作耗时
        return names;
    }
}

```

#### 代理工具类

```java
package com.showguan.proxy_demo2;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

// 代理工具类，用于创建代理对象
public class ProxyUtil {
    // 创建代理对象的方法
    public static UserService createProxy(UserService userService) {
        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(
                ProxyUtil.class.getClassLoader(), // 使用当前类的类加载器加载代理类
                new Class[]{UserService.class}, // 指定代理对象实现的接口，这里是UserService接口
                new InvocationHandler() { // 设置代理对象的调用处理器，即InvocationHandler
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        if (method.getName().equals("login") || method.getName().equals("deleteUsers")
                                || method.getName().equals("selectUsers")) { // 如果是登录、删除用户或查询用户方法
                            long startTime = System.currentTimeMillis(); // 记录方法调用开始时间

                            Object rs = method.invoke(userService, args); // 调用原始对象的方法

                            long endTime = System.currentTimeMillis(); // 记录方法调用结束时间
                            System.out.println(method.getName() + "方法执行耗时:" + (endTime - startTime) / (1000.0) + "s"); // 输出方法执行耗时
                            return rs; // 返回方法调用结果
                        } else { // 对于其他方法，直接调用原始对象的方法
                            Object rs = method.invoke(userService, args);
                            return rs;
                        }
                    }
                });
        return userServiceProxy; // 返回创建的代理对象
    }
}

```

#### 测试类

```java
package com.showguan.proxy_demo2;

// 测试类
public class Test {
    public static void main(String[] args) throws Exception {
        UserService us = ProxyUtil.createProxy(new UserServiceImpl()); // 使用代理工具类创建代理对象
        us.login("admin", "123456"); // 调用代理对象的登录方法
        System.out.println("---------------");
        us.deleteUsers(); // 调用代理对象的删除用户方法
        System.out.println("---------------");
        us.selectUsers(); // 调用代理对象的查询用户方法
        System.out.println("---------------");
    }
}

```

