---
title : 'Java Web开发(7)'
date : 2024-06-11T22:30:13+08:00
lastmod: 2024-06-11T22:20:13+08:00
description : "JavaWeb学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["JavaWeb学习笔记"]
tags : ["Java", "Web"]
# password : leetcode


---

# Java Web开发(7)

## Spring原理

### 配置优先级

SpringBoot中支持三种格式配置文件的优先级

- properties
- yaml
- **yml**

### 配置

- SpringBoot除了支持配置文件属性配置，还支持**Java系统属性**和**命令行参数**的方式进行属性配置

  - Java系统属性

  - ```java
    -Dserver.port=9000
    ```

  - 命令行参数

  - ```shell
    --server.port=10010
    ```

打包时指定端口号(cmd 命令)

```shell
java -Dserver.port=9000 -jar tilas-web-management-0.0.1-SNAPSHOT.jar --server.port=10011
```

powershell命令行参数解释不同

```powershell
java -D"server.port=9000" -jar .\tilas-web-management-0.0.1-SNAPSHOT.jar --server.port=10011
```

Java系统属性`-Dserver.port=9000`优先级高于三种配置文件， 而`--server.port=10011`高于Java系统属性

## Bean管理

获取bean

- 默认情况下，Spring项目启动时，会把bean都创建好放在IOC容器中，如果想要主动获取这些bean， 可以通过如下方式

  - 根据name获取bean 

    ```java
    Object getBean(String name)
    ```

  - 根据类型获取bean

    ```java
    <T> T getBean(Class<T> requiredType)
    ```

  - 根据name获取bean（**带类型转换**）

    ```java
    <T> T getBean(String name, Class<T> requiredType)
    ```

    

**BUG**`解决Could not autowire. No beans of 'ApplicationContext' type found.的问题`

idea导入出错

```java
import org.springframework.context.ApplicationContext;
```

```java
        DeptController bean1 = (DeptController) applicationContext.getBean("deptController");
        System.out.println(bean1);

        DeptController bean2 = (DeptController) applicationContext.getBean(DeptController.class);
        System.out.println(bean2);

        DeptController bean3 = (DeptController) applicationContext.getBean("deptController", DeptController.class);
        System.out.println(bean3);
```

### bean的作用域

|   作用域    |                      说明                       |
| :---------: | :---------------------------------------------: |
|  singleton  |   容器内同名称的bean只有一个实例（单例）默认    |
|  prototype  |    每次使用该bean时会创建新的实例（非单例）     |
|   request   | 每个请求范围内会创建新的实例（web环境中，了解） |
|   session   | 每个会话范围内会创建新的实例（web环境中，了解） |
| application | 每个应用范围内会创建新的实例（web环境中，了解） |

```java
//@Lazy
@Scope("prototype")
```

**注意事项**：

- 默认`singleton`的bean，在容器启动时背创建，可以使用@Lazy注解来延迟初始化（延迟到第一次使用）
- `prototype`的bean，每一次使用该bean的时候都会创建一个新的实例
- 实际开发中，绝大部分的bean都是单例的，也就是说绝大部分的bean不需要配置scope属性

### 第三方bean

- 如果要管理的bean对象来自第三方（不是自定义的），是无法使用`@Component`及衍生注解声明bean的，**就需要用到`@Bean`注解**
- 若要管理的第三方bean对象，建议对这些bean进行集中分类配置，可以通过`@Configuration`注解声明一个配置类。

```java
@Configuration
public class CommonConfig {
    @Bean
    // 第三方bean对象
    // 获取bean对象 : 如果没有指定bean名称，则默认为方法名
    // 第三方bean对象需要对象注入，只需要在方法参数中写该参数即可
    public SAXReader reader(DeptService deptService){
        System.out.println(deptService);
        return new SAXReader();
    }
}
```

`@Component`及衍生注解与`@Bean`注解使用场景：

1. 项目中自定义的，使用@Component及其衍生注解
2. 项目中引入第三方的，使用@Bean注解

## SpringBoot原理

**起步依赖**：`SpringBoot`使用`Maven`依赖传递管理依赖，可以自动配置依赖

**自动配置**：

- `SpringBoot`的自动配置就是当`Spring`容器启动后，一些配置类，`bean`对象就自动存入到了`IOC`容器中，不需要我们手动去声明，从而简化了开发，省去了繁琐的配置操作。

### 自动配置原理

- 方案一：`@ComponentScan` 组件扫描

  ```java
  @ComponentScan({"com.example", "com.showguan"})
  public class TilasWebManagementApplication {
  ```

- 方案二：`@Import`导入。使用`@Import`导入的类会被Spring加载到IOC容器中，导入形式

  - 导入普通类
  - 导入配置类
  - 导入`ImportSelector`接口实现类
  - `@EnableXXX`注解，封装`@Import`注解

```java
//@Import({TokenParser.class})
//@Import({HeaderConfig.class})
//@Import({MyImportSelector.class})
@EnableHeaderConfig
public class TilasWebManagementApplication {
}
```

#### 源码跟踪

`@SpringBootApplication`

- 该注解标识在SpringBoot工程引导类上，是SpringBoot中**最最最**重要的注解。
  - `@SpringBootConfiguration`：该注解与`@Configuration`注解作用相同，用来声明当前也是一个配置类
  - `@ComponentScan`：组件扫描，默认扫描当前引导类所在包及其子包
  - `@EnableAutoConfiguration`：SpringBoot实现自动化配置的核心注解

![image-20240612192502791](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612192502791.png)

#### **@Conditional**

- 作用：按照一定的条件进行判断，在满足给定条件后才会注册对应的bean对象到Spring IOC容器中
- 位置：方法，类
- `@Conditional` 本身是一个父注解，派生出大量的子注解
  - `@ConditionalOnClass`：判断环境中是否有对应**字节码文件**，才注册bean到IOC容器
  - `@ConditonalOnMissingBean`：判断环境中没有对应的bean（类型或名称），才注册这个bean到IOC容器中 （常用场景：先使用自己创建的bean，如果自己没有创建，则使用系统默认的bean）-- 指定类型（value属性） 或 名称（name属性）
  - `@ConditionalProperty`：判断配置文件中有对应属性和值，才注册bean到IOC容器中

#### 自定义`starter`

- 场景：在实际开发中，经常会定义一些公共组件，提供给项目团队使用。而在SpringBoot的项目中，一般会将这些公共组件封装为SpringBoot的starter

步骤：

- 创建`aliyun-oss-spring-boot-starter`模块
- 创建`aliyun-oss-spring-boot-autoconfigure`模块，**在starter中引入该模块**
- 在`aliyun-oss-spring-boot-autoconfigure` 模块中的定义自动配置功能，并定义自动配置文件 `META-INF/spring/xxx.imports`

文件目录结构：

![image-20240612211132888](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612211132888.png)

外部类需要使用OSS功能时：

##### 1、引入依赖

![image-20240612211241678](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612211241678.png)

##### 2、编写yml文件的必要参数

![image-20240612211329606](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612211329606.png)

3、直接注入即可

![image-20240612212448857](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612212448857.png)

## 总结

Web三层架构

![image-20240612213834814](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612213834814.png)

![image-20240612213933054](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612213933054.png)

**SpringMVC 是 Spring framework中的Web模块**

![image-20240612214111620](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240612214111620.png)

**SSM**(SpringMVC + Spring framework + Mybatis)

## MVN高级

**maven生成imi文件**

```shell
mvn idea:module
```

### 为什么要分模块设计？

方便项目的管理维护，扩展，也方便模块间的相互调用，资源共享。

### 注意事项

- 分模块设计需要先针对模块功能进行设计，再进行编码。不会先将工程开发完毕，然后进行拆分。

### 继承关系实现

**常见打包方式**：

- **jar**：普通模块打包，springboot项目基本都是jar包（内嵌tomcat运行）
- **war**：普通web程序打包，需要部署在外部的tomcat服务器上
- **pom**：父工程或聚合工程，该模块不写代码，仅进行依赖管理

步骤:

1. 创建maven模块 tlias-parent，该工程为父工程， 设置打包方式**pom**

   ```xml
   <!--    设置spring工程的父工程-->
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>3.2.6</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
   
       <groupId>com.showguan</groupId>
       <artifactId>tlias-parent</artifactId>
       <version>1.0-SNAPSHOT</version>
   <!--    设置打包方式-->
       <packaging>pom</packaging>
   ```

2. 在子工程的**pom.xml**文件中，配置继承关系

   ```xml
       <parent>
           <groupId>com.showguan</groupId>
           <artifactId>tlias-parent</artifactId>
           <version>1.0-SNAPSHOT</version>
           <relativePath>../tlias-parent/pom.xml</relativePath>
       </parent>
   ```

   **注意事项**：

   - 在子工程中，配置了继承关系之后，坐标中的**groupid**是可以省略的，因为会自动继承父工程的。
   - relativePath指定父工程的pom文件的相对位置（如果不指定，将从本地仓库/远程仓库查找该工程）。

3. 在父工程中配置各个工程共有的依赖（子工程会自动继承父工程的依赖）

   ```xml
       <dependencies>
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
           </dependency>
       </dependencies>
   ```

   - 若父子工程都配置了同一个依赖，以子工程的依赖为准。

### 版本锁定

- 在maven中，可以在父工程的pom文件中 通过 `<dependencyManagement>` 来统一管理依赖版本

父工程：

```xml
    <properties>
        <jjwt.version>0.9.1</jjwt.version>
    </properties>
        
    <dependencyManagement>
            <dependencies>
                <dependency>
                    <groupId>io.jsonwebtoken</groupId>
                    <artifactId>jjwt</artifactId>
                    <version>${jjwt.version}</version>
                </dependency>
            <dependencies>
    <dependencyManagement>
```

子工程（仍需引入依赖，但是不需要版本号）：

```xml
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
        </dependency>
```

**注意事项**：

- 子工程引入依赖时，无需指定`<version>`版本号，父工程统一管理，变更依赖版本，只需要父工程中统一变更。

## 聚合

将多个模块组织成一个整体，同时进行项目的构建

- **聚合工程**

一个不具有业务功能的"空"工程（有且仅有一个pom文件

**作用**：快速构建项目（无需根据依赖关系手动构建，直接在聚合工程上构建即可）

在parent 的pom.xml文件内添加

```xml
    <modules>
        <module>../tlias-pojo</module>
        <module>../tlias-utils</module>
        <module>../tlias-web-management</module>
    </modules>
```

聚合和继承：

- 作用
  - 聚合用于快速构建项目
  - 继承用于简化依赖配置、统一管理依赖
- **相同点**
  - 聚合与继承的pom.xml文件打包方式均为pom， 可以将两种关系制作到同一个pom文件中
  - 聚合与继承均属于**设计型模块**，**并无实际的模块内容**
- **不同点**
  - 聚合是在聚合工程中配置关系，聚合可以感知到参与聚合的模块有哪些
  - 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己

## 私服

- 私服是一种特殊的仓库，它是架设在局域网内的仓库服务，用来代理位于外部的中央仓库，用于解决团队内部的资源共享和资源同步问题。

资源上传与下载

**项目版本**：

```xml
    <artifactId>tlias-web-management</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>tilas-web-management</name>
    <description>tilas-web-management</description>
```

- **RELEASE（发行版本）**：功能趋于稳定，当前更新停止，可以用于发行的版本，存储在私服中的RELEASE仓库中。
- **SNAPSHOT**（快照版本）：功能不稳定，尚处于开发中的版本，即快照版本，存储在私服的SNAPSHOT仓库中。
