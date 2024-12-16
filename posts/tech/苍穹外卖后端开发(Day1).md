---
title : '苍穹外卖后端开发(Day1)'
date : 2024-10-12T22:30:13+08:00
lastmod: 2024-10-12T22:20:13+08:00
description : "苍穹外卖后端开发(Day1)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day1)

## 软件开发流程

**需求分析** ： 需求规格说明说、产品原型

**设计** ： UI设计、 数据库设计、接口设计

**编码** ： 项目代码、单元测试

**测试** ： 测试用例、测试报告

**上线运维** ： 软件环境安装、配置

### 角色分工

- 项目经理：对整个项目负责，任务分配，把控进度。
- 产品经理：进行需求调研，输出需求调研文档、产品原型等
- UI设计师：根据产品原型输出界面效果图
- 架构师：项目整体架构设计、技术选型等
- **开发工程师**：代码实现
- 测试工程师：编写测试用例，输出测试报告
- 运维工程师：软件环境搭建，项目上线

### 软件环境

- 开发环境（development）：开发人员在开发阶段使用的环境，一般外部用户无法访问
- 测试环境（testing）：专门给测试人员使用的环境，用于测试项目，一般外部用户无法访问
- 生产环境（production）：即线上环境，正式提供对外服务的环境。

## 苍穹外卖项目介绍

### 项目介绍

**定位** ： 专门为餐饮企业（餐厅、饭店）定制的一款软件产品

![image-20241010171318175](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241010171318175.png)

### 产品原型

产品原型 ：用于展示项目的业务功能，一般由产品经理进行设计

### 技术选型

技术选型 ： 展示项目中使用到的技术框架和中间件等。

![image-20241010180244898](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241010180244898.png)

## 整体结构

![image-20241010215538610](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241010215538610.png)

## 前端环境搭建

### Nginx服务器

![image-20241011151636709](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241011151636709.png)

## 后端环境搭建

后端工程基于maven进行项目构建，并且进行分模块开发

![image-20241011152051191](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241011152051191.png)

- `sky-take-out` : maven 父工程， 统一管理依赖版本， 聚合其他子模块
- `sky-common` : 子模块， 存放公共类，例如 ： 工具类、常量类、异常类等
- `sky-pojo` : 子模块 ，存放实体类，VO， DTO等

| 名称   | 说明                                         |
| ------ | -------------------------------------------- |
| Entity | 实体，通用和数据库中的对应                   |
| DTO    | 数据传输对象，通用用程序中各层之间传递数据   |
| VO     | 视图对象，为前端展示数据提供的对象           |
| POJO   | 普通Java对象，只有属性和对应的getter和setter |

- `sky-server` : 子模块，后端服务，存放配置文件,  配置类，拦截器，Controller， Service， Mapper等

### 数据库环境搭建

MySQL + Navicat

### 后端环境搭建 - 前后端联调

后端的初始工程中已经实现了登录功能， 直接进行前后端联调测试即可。

![image-20241011211915611](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241011211915611.png)

前后端请求地址不对应，但是前后端联调成功

nginx反向代理， 就是讲前端发送的动态请求由nginx转发到后端服务器

nginx反向代理的好处：

- 提高访问速度
- 进行负载均衡
- 保证后端服务安全

nginx负载均衡策略：

| 名称       | 说明                                                     |
| ---------- | -------------------------------------------------------- |
| 轮询       | 默认为方式                                               |
| weight     | 权重方式，默认为，权重越高，被分配的客户终端资源就越多   |
| ip_hash    | 依赖于IP地址分配，这样每个访客可以固定访问同一个后端服务 |
| least_conn | 依赖于连接数，把请求优先分配到连接数最少的后端服务       |
| url_hash   | 依赖于请求的URL，这样请求对应的服务名称会被优先分配      |
| fair       | 依赖于响应时间，响应时间越短的服务将被优先分配           |

## 完善登录功能

将密码加密后存储，提高安全性

将后续需要完善的代码用 TODO 标识出来

```java
        password = DigestUtils.md5DigestAsHex(password.getBytes());
```

## 导入接口文档

![image-20241011214624732](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241011214624732.png)

使用apifox导入json文件

## [Swagger](https://swagger.io/)

测试工具

使用Swagger只需要按照它的规范去定义接口以及接口相关的信息，就可以做到生成接口文档，以及在线接口调试页面

`Knife4j` 是为 Java MVC 框架集成Swagger生成 `Api` 文档的增强解决方案。

1、导入knife4j坐标

```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>3.0.2</version>
</dependency>
```

2、在配置类中加入knife4j相关配置

```java
@Bean
public Docket docket() {
    ApiInfo apiInfo = new ApiInfoBuilder()
        .title("苍穹外卖项目接口文档")
        .version("2.0")
        .description("苍穹外卖项目接口文档")
        .build();

    Docket docket = new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo)
        .select()
        // 指定生成接口需要扫描的包
        .apis(RequestHandlerSelectors.basePackage("com.sky.controller"))
        .paths(PathSelectors.any())
        .build();

    return docket;
}
```

3、设置静态资源映射，否则接口文档页面无法访问

```java
/**
 * 设置静态资源映射
 * @param registry
 */
protected void addResourceHandlers(ResourceHandlerRegistry registry) {
    log.info("开始设置静态资源映射...");
    registry.addResourceHandler("/doc.html").addResourceLocations("classpath:/META-INF/resources/");
    registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
}
```

`YAPI`(`ApiFox`)用于设计接口时使用， 而`Swagger`用于后端开发好之后测试使用。

### 常用注解

| 注解              | 说明                                                   |
| ----------------- | ------------------------------------------------------ |
| @Api              | 用在类上，例如Controller，表示对类的说明               |
| @ApiModel         | 用在类上，例如entity、DTO、VO                          |
| @ApiModelProperty | 用在属性上，描述属性信息                               |
| @ApiOperation     | 用在方法上，例如Controller的方法，说明方法的用途、作用 |
