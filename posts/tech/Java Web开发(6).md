---
title : 'Java Web开发(6)'
date : 2024-06-10T22:30:13+08:00
lastmod: 2024-06-10T22:20:13+08:00
description : "JavaWeb学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["JavaWeb学习笔记"]
tags : ["Java", "Web"]
# password : leetcode

---

# Java Web开发(6)

**事务**：事务是一组操作的集合，它是一个不可分割的工作单位，这些操作要么同时成功，要么同时失败

```sql
start transaction / begin
commit
rollback
```

## Spring事务管理

- `@Transactional`
- 位置：业务（service）层的方法上、类上、接口上
- 作用：将当前方法交给spring进行事务管理，方法执行前，开启事务；成功执行完毕，提交事务；出现异常，回滚事务。

事务属性

- 默认情况下，只有出现`RuntimeException` 才回滚异常。`rollbackFor`属性用于控制出现何种异常类型是回滚事务

```java
    @Transactional(rollbackFor = Exception.class)
```

传播行为

- `propagation`
- 事务传播行为：指的是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行事务控制

- `REQUIRED`：大部分情况下都是用该传播行为即可
- `REQUIRES_NEW`：当我们不希望**事务之间相互影响**时，可以使用该传播行为。比如：下订单前需要记录日志，不论订单保存成功与否，都需要保证日志记录能够记录成功。

## AOP

- **AOP**（Aspect Oriented Programming）面向切面编程，面向方面编程，其实就是**面向特定方法编程**。

**场景**：

统计`Service`各项方法的运行耗时

```java
@Slf4j
@Component
@Aspect
public class TimeAspect {
    @Around("execution(* com.showguan.service.*.*(..))")
    public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long begin = System.currentTimeMillis();

        Object result = joinPoint.proceed();

        long end = System.currentTimeMillis();

        log.info(joinPoint.getSignature() + "方法耗时 :{} ms", (end-begin));
        return result;
    }
}
```

**AOP核心概念**

- **连接点**：`JoinPoint`，可以被AOP控制的方法（暗含方法执行时的相关信息）
- **通知**：`Advice`， 指哪些重复的逻辑，也就是共性功能（最终体现为一个方法）
- **切入点**：`PointCut`，匹配连接点的条件，通知仅会在切入点方法执行时被应用
- **切面**：`Aspect`，描述通知与切入点的对应关系（通知+切入点）
- **目标对象**：`Target`, 通知所应用的对象

通知类型

- **`@Around`：环绕通知**，此注解标注的通知方法在目标方法前后都被执行
- `@Before`：前置通知，此注解标注的通知方法在目标方法前被执行
- `@After`：后置通知，此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行
- `@AfterReturning`：返回后通知，此注解标注的通知方法在目标方法后被执行，有异常不会执行
- `@AfterThrowing`：异常后通知，此注解标注的通知方法发生异常后执行

注意事项；

- **@Around** 环绕通知需要自己调用 

```java
Object result = proceedingJoinPoint.proceed();
```

来让原始方法执行，其他通知不需要考虑目标方法执行

- **@Around** 环绕通知方法的返回值，必须指定为Object，来接收原始方法的返回值

### 通知顺序

当有多个切面的切入点都匹配到了目标方法，目标方法运行时，多个通知方法都会被执行

**执行顺序**

1. 不同切面类中，默认按照切面类的类名字母排序
   - 目标方法前的通知方法：字母排名靠前的先执行
   - 目标方法后的通知方法：字母排名靠前的后执行
2. 用`@Order`（数字）加在切面类上来控制顺序
   - 目标方法前的通知方法：数字小的先执行
   - 目标方法后的通知方法：数字小的后执行

切入点表达式

- 描述切入点方法的一种表达式
- 作用：主要用来决定项目中哪些方法需要加入通知
- 常见形式
  1. `execution(...)`：根据方法的签名来匹配
  2. `@annotation(...)`：根据注解匹配

execution根据方法的返回值，包名，类名，方法名，方法参数等信息来匹配，语法为：

```java
execution(访问修饰符? 返回值 包名.类名.?方法名(方法参数) throws 异常?)
```

- 其中带 `?` 表示可以省略的部分
  - 访问修饰符：可省略（public, protected)
  - 包名.类名：可省略
  - throws异常：可省略（注意时方法上声明抛出的异常，不是实际抛出的异常）

- 可以使用通配符描述切入点

  - **`*`**：单个独立的任意符号，可以通配任意返回值，包名，类名，方法名，任意类型的**一个参数**，也可以统配包，类，方法名的一部分

  - ```java
    execution(* com.*.service.*.update*(*))
    ```

  - `..` : 多个连续的任意符号，可以通配任意层级的包，或任意类型，**任意个数的参数**

  - ```java
    execution(* com.showguan..DeptService.*(..))
    ```

根据业务需要， 可以使用 `&&`， `||`， `! `来组合比较复杂的切入点表达式

书写建议：

- 所有业务方法名在命名时尽量规范，方便切入点表达式快速匹配。如：查询类方法都是`find`开头，更新类方法都是`update`开头
- 描述切入点方法通常基于**接口描述**，而不是直接描述实现类，增强拓展性
- 在满足业务需要的前提下，**尽量缩小切入点的匹配范围**，如：包名尽量不使用...， 使用*匹配单个包名

### 注解形式

在apo文件夹内新建注解文件 Mylog.java

```java
@Retention(RetentionPolicy.RUNTIME) //什么时候运行
@Target(ElementType.METHOD) // 运行范围
public @interface MyLog {


}
```

Aspect文件配置：

```java
@Slf4j
@Component
@Aspect
public class MyAspect2 {

//    @Pointcut("execution(* com.showguan.service.DeptService.list()) ||" +
//            "execution(* com.showguan.service.DeptService.delete(java.lang.Integer))"
//        )
    @Pointcut("@annotation(com.showguan.apo.MyLog)")
    private void pt(){}

    @Before("pt()")
    private void before(){
        log.info("My Aspect6 ... Before ...");
    }

}
```

在需要匹配的方法前面加上 `@MyLog` 注解即可。

连接点

- 在`Spring`中用`JoinPoint`抽象了连接点，用它可以后的方法执行时的相关信息，如目标类名，方法名，方法参数等
  - 对于`@Around`通知，获取连接点信息只能用`ProceedingJoinPoing`
  - 对于其他四种通知，获取连接点信息只能使用`JointPoint`， 它是`ProceedingPoint`的父类型

```java
package com.showguan.apo;

import lombok.extern.slf4j.Slf4j;
import net.sf.jsqlparser.statement.select.Join;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Slf4j
@Component
@Aspect
public class MyAspect3 {
    @Pointcut("execution(* com.showguan.service.impl.DeptServiceImpl.*(..))")
//    private void pt(){}
    public void pt(){}

    @Before("pt()")
    public void before(JoinPoint joinPoint){
        log.info("before ...");
    }


    @Around("pt()")
    public Object around(ProceedingJoinPoint JoinPoint) throws Throwable {
        log.info("around before");

        Object target = JoinPoint.getTarget();
        log.info("目标 对象的类名：{}", JoinPoint);

        String name = JoinPoint.getSignature().getName();
        log.info("目标方法的方法名", name);

        Object[] args = JoinPoint.getArgs();
        log.info("目标方法运行时传入的参数：{}", args);

        Object result = JoinPoint.proceed();
        log.info("目标方法的返回值：{}", result);

        log.info("around after ...");

        return result;

    }

}
```

### 实践

对员工和部分的增删改部分进行日志记录

```java
@Slf4j
@Component
@Aspect
public class LogAspect {
    @Autowired
    private OperateLogMapper operateLogMapper;

    @Autowired
    private HttpServletRequest request;

    @Around("@annotation(com.showguan.anno.Log)")
    public Object recordLog(ProceedingJoinPoint joinPoint) throws Throwable {
        String jwt = request.getHeader("token");
        Claims claims = JwtUtils.parseJWT(jwt);

        Integer operateUser = (Integer) claims.get("id");

        LocalDateTime operateTime = LocalDateTime.now();

        String className = joinPoint.getTarget().getClass().getName();

        String methodName = joinPoint.getSignature().getName();

        Object[] args = joinPoint.getArgs();

        String methodParams = Arrays.toString(args);

        long begin = System.currentTimeMillis();
        Object result = joinPoint.proceed();
        long end = System.currentTimeMillis();

        String returnValue = JSONObject.toJSONString(result);

        Long costTime = end - begin;

        OperateLog operateLog = new OperateLog(null, operateUser, operateTime, className, methodName, methodParams, returnValue, costTime);

        operateLogMapper.insert(operateLog);
        return result;

    }

}
```

