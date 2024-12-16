---
title : 'Java Web开发(3)'
date : 2024-04-27T22:30:13+08:00
lastmod: 2024-04-27T22:20:13+08:00
description : "JavaWeb学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["JavaWeb学习笔记"]
tags : ["Java", "Web"]
# password : leetcode
---

# Java Web开发(3)

## Maven

Maven是一款管理和构建java项目的工具

- 依赖管理
- 统一的项目结构
- 项目构建

```xml
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
```

### MVN坐标

- Maven中的坐标是资源的唯一标识，通过该坐标可以唯一定位资源位置
- 使用坐标来定义项目或引入项目中所需要的依赖

**Maven**的坐标主要组成

- `groupid`:定义当前Maven项目隶属组织名称（通常是域名反写）
- `artifactId`：定义当前Maven项目名称（通常是模块名称，例如 order-service, goods-service）
- `version`：定义当前项目版本号

导入maven工程：

![image-20240427155144627](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240427155144627.png)

### Maven中项目依赖配置

在`<dependencies>`中添加

```xml
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.3</version>
    </dependency>
```

- 如果所引入的依赖，在本地仓库不存在，将会连接远程仓库/中央仓库，然后下载依赖
- 如果不知道依赖的坐标信息，到https://mvnrepository.com/中搜索。

### Maven依赖管理

- 依赖具有传递性
  - **直接依赖**：在当前项目中通过依赖配置建立的依赖关系
  - **间接依赖**：被依赖的资源如果依赖其他资源，当前项目间接依赖其他资源

### 排除依赖

```xml
            <!-- 排除依赖-->
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
```

### 依赖范围

依赖的jar包，默认情况下，可以在任何地方使用，可以通过`<scope>...</scope>`设置其作用范围

- 作用范围：
  - 主程序范围有效。（main文件夹范围内）
  - 测试程序范围有效。（test文件夹范围内）
  - 是否参与打包运行。（package指令范围内）

| scope值  | 提供库 | 编译时 | 运行时 |    范例     |
| :------: | :----: | :----: | :----: | :---------: |
| compile  |   Y    |   Y    |   Y    |    log4j    |
|   test   |   -    |   Y    |   -    |    junit    |
| provided |   Y    |   Y    |   -    | servlet-api |
| runtime  |   -    |   Y    |   Y    |  jdbc驱动   |

### Maven生命周期

每套生命周期包含一些阶段（`phase`），阶段是有顺序的，后面的阶段依赖于前面的阶段

- `clean` : 移除上一次构建生成的文件
- `compile` : 编译项目源代码
- `test` : 使用合适的单元测试框架运行测试（junit）
- `package` ： 将编译后的文件打包，如：jar, war等
- `install` ： 安装项目到本地仓库

同一套生命周期中，当运行后面的阶段时，前面的阶段都会运行。

### SpringBoot开发

创建SpringWeb工程

```java
package com.showguan;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello() {
        System.out.println("hello world");
        return "hello world!";
    }
}

```

### HTTP协议

- Hyper Text Transfer Protocol, 超文本传输协议，规定了浏览器和服务器之间的数据传输的规则

**特点**：

1. 基于TCP协议：面向连接，安全
2. 基于请求-响应模型的：一次请求对应一次响应
3. HTTP协议是无状态的协议：对于事物处理没有记忆能力。每次请求-响应都是独立的。
   - **缺点**：多次请求间不能共享数据
   - **优点**：速度快

#### HTTP请求协议

##### 请求行（请求方式，资源路径, 协议）

```
GET /hello HTTP/1.1
POST /hello HTTP/1.1
```

##### 请求头：第二行开始，格式 ： key:value

| 名称            | 值                                                           |
| --------------- | ------------------------------------------------------------ |
| Host            | 请填写您的网站域名                                           |
| User-Agent      | 用户代理，标识发出请求的浏览器和操作系统，例如Mozilla/5.0 (Windows NT...) like Gecko |
| Accept          | 可接受的响应内容类型，例如text/*, image/*等                  |
| Accept-Language | 可接受的响应内容语言，例如zh-CN,zh;q=0.8                     |
| Accept-Encoding | 可接受的响应内容编码方式，例如gzip, deflate等                |
| Content-Type    | 请求体的媒体类型                                             |
| Content-Length  | 请求体的长度（单位：字节）                                   |

##### 请求体：POST请求，存放请求参数

- 请求方式-GET：请求参数在请求行中，没有请求体
- 请求方式-POST：请求参数在请求体中，POST请求大小是没有限制的

#### HTTP响应协议

#### 响应格式

##### 响应行：响应数据第一行（协议，状态码，描述）

| 状态码分类 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 1xx        | **响应中** --- 临时状态码。表示请求已经接受，告诉客户端应该继续请求或者如果已经完成则忽略 |
| 2xx        | **成功** --- 表示请求已经被成功接收，处理已完成              |
| 3xx        | **重定向** --- 重定向到其它地方，让客户端再发起一个请求以完成整个处理 |
| 4xx        | **客户端错误** --- 处理发生错误，责任在客户端，如：客户端的请求一个不存在的资源，客户端未被授权，禁止访问等 |
| 5xx        | **服务器端错误** --- 处理发生错误，责任在服务端，如：服务端抛出异常，路由出错，HTTP版本不支持等 |

## 一、状态码大类

| 状态码分类 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 1xx        | **响应中**——临时状态码，表示请求已经接受，告诉客户端应该继续请求或者如果它已经完成则忽略它 |
| 2xx        | **成功**——表示请求已经被成功接收，处理已完成                 |
| 3xx        | **重定向**——重定向到其它地方：它让客户端再发起一个请求以完成整个处理。 |
| 4xx        | **客户端错误**——处理发生错误，责任在客户端，如：客户端的请求一个不存在的资源，客户端未被授权，禁止访问等 |
| 5xx        | **服务器端错误**——处理发生错误，责任在服务端，如：服务端抛出异常，路由出错，HTTP版本不支持等 |

## 二、常见的响应状态码

| 状态码  | 英文描述                               | 解释                                                         |
| ------- | -------------------------------------- | ------------------------------------------------------------ |
| ==200== | **`OK`**                               | 客户端请求成功，即**处理成功**，这是我们最想看到的状态码     |
| 302     | **`Found`**                            | 指示所请求的资源已移动到由`Location`响应头给定的 URL，浏览器会自动重新访问到这个页面 |
| 304     | **`Not Modified`**                     | 告诉客户端，你请求的资源至上次取得后，服务端并未更改，你直接用你本地缓存吧。隐式重定向 |
| 400     | **`Bad Request`**                      | 客户端请求有**语法错误**，不能被服务器所理解                 |
| 403     | **`Forbidden`**                        | 服务器收到请求，但是**拒绝提供服务**，比如：没有权限访问相关资源 |
| ==404== | **`Not Found`**                        | **请求资源不存在**，一般是URL输入有误，或者网站资源被删除了  |
| 405     | **`Method Not Allowed`**               | 请求方式有误，比如应该用GET请求方式的资源，用了POST          |
| 428     | **`Precondition Required`**            | **服务器要求有条件的请求**，告诉客户端要想访问该资源，必须携带特定的请求头 |
| 429     | **`Too Many Requests`**                | 指示用户在给定时间内发送了**太多请求**（“限速”），配合 Retry-After(多长时间后可以请求)响应头一起使用 |
| 431     | **` Request Header Fields Too Large`** | **请求头太大**，服务器不愿意处理请求，因为它的头部字段太大。请求可以在减少请求头域的大小后重新提交。 |
| ==500== | **`Internal Server Error`**            | **服务器发生不可预期的错误**。服务器出异常了，赶紧看日志去吧 |
| 503     | **`Service Unavailable`**              | **服务器尚未准备好处理请求**，服务器刚刚启动，还未初始化好   |

状态码大全：https://cloud.tencent.com/developer/chapter/13553 

##### 响应头：第二行开始，格式 `key:value`

##### 响应体：最后一部分，存放响应数据

## Web服务器

Web服务器是一个软件程序，对HTTP协议的操作进行封装，使得程序员不必直接对协议进行操作，让Web开发更加便捷。主要功能是“提供网上信息浏览服务”。

### TomCat

- Apache软件基金会一个核心小木，是一个开源免费的轻量级Web服务器，支持Servlet/JSP少量JavaEE规范
- TomCat也被称为**Web容器**，Servlet容器。Servlet程序需要依赖于TomCat才能运行

启动方式：`bin/startup.bat`

修改TomCat端口号

`conf/server.xml`

Localhost默认端口号就是`80`

用tomcat部署，将html文件复制到`webapps`文件夹中

起步依赖：

![image-20240428131914916](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240428131914916.png)

- 基于SpringBoot开发的web应用程序，内置了tomcat服务器，当启动类运行时，会自动启动内嵌的tomcat服务器

![image-20240428133041663](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240428133041663.png)

## 请求

前后端分离开发

PostMan 

### 实体参数

实体对象参数：请求参数名与形参对象属性名相同， 即可直接通过POJO接受

### 数组集合参数

- **数组参数**：请求参数名与形参数组名称相同且请求参数为多个，定义数组类型形参即可接受参数

### 路径参数

- 通过请求URL直接传递参数，使用`{...}`来标识该路径参数，使用`@PathVariable`获取路径参数

## 响应

`ResponseBody`

- 类型：方法注解，类注解
- 位置：`Controller`方法上/类上
- 作用：将方法返回值直接响应，如果返回值类型时 **实体类型/集合**，**将会转换为JSON格式响应**
- 说明：`@RestController` = `@Controller` + `@ResponseBody`

### 统一响应结果

```java
Result(code, msg, data)
    public class Result {
    private Integer code ;//1 成功 , 0 失败
    private String msg; //提示信息
    private Object data; //数据 data
    }
```

## 分层解耦

三层架构

- `controller`:控制层，接受前端发送的请求，对请求进行处理，并响应数据
- `service`:业务逻辑层，处理具体的业务逻辑
- `dao`:数据访问层（`Data Access Object`）（持久层），负责数据访问操作，包括数据的增、删、改、查。

**内聚**：软件中各个功能模块内部的功能联系

**耦合**：衡量软件中各个层/模块之间的依赖、关联的程度

软件设计原则：高内聚低耦合

- **控制反转**：`Inversion Of Control（IOC）`。对象的创建控制权由车光绪自身转移到外部(容器)， 这种思想称为控制反转。

- **依赖注入**：`Dependency Injection`, 简称`DI`。 容器为应用程序提供运行时，所以来的组员，称之为依赖注入。

- **Bean对象**：IOC容器中创建、管理的对象，称之为`bean`。

用`@Component`标识容器

### IOC控制反转

除了`@Component`之外还有`@Service`, `@Repository`， `@Controller`

注意事项：

- 声明bean的时候，可以通过value属性指定bean的名字，如果没有指定，默认为类名首字母小写
- 使用以上四个注解都可以声明bean， 但是在springboot继承web开发中，声明控制器bean只能用@Controller

Bean组件扫描：

- 声明bean若要想生效，需要被组件扫描注解@ComponentScan扫描
- `@ComponentScan`注解虽然没有显式配置，但是实际上已经包含在了启动类声明注解`@SpringBootApplication`中，默认扫描的范围时启动类所在包及其子包

### Bean注入

- @Autowired注解，默认时按照类型进行，如果存在多个相同类型的bean， 将会报出如下错误：
- 如果同类型的bean存在多个：
  - `@Primary`
  - `@Autowired` + `@Qualifier("bean的名称")`
  - `@Resource(name = "bean的名称")`

- **面试题**
  - `@Resource`和`@Autowired`的区别：
    - `@Autowired`是`spring`框架提供的注解，而`@Resource`是`JDK`提供的注解。
    - `@Autowired`默认是按照类型注入，而`@Resource`默认是按照名称注入的。