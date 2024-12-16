---
title : '苍穹外卖后端开发(Day2)'
date : 2024-10-13T22:30:13+08:00
lastmod: 2024-10-13T22:20:13+08:00
description : "苍穹外卖后端开发(Day2)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day2)

## 新增员工

- **管理端**发出的请求， 统一使用`/admin`作为前缀
- **用户端**发出的请求，统一使用`/user`作为前缀

![image-20241012160557684](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241012160557684.png)

### 代码开发

根据新增员工接口设计对应的DTO

**注意** ： 当前端提交的数据和实体类中对应的属性差别比较大时，建议使用DTO来封装数据

### 功能测试

功能测试方式：

- 通过接口文档测试
- 通过前后端联调测试

**注意**：由于开发阶段前端和后端时并行开发的，后端完成某个功能后，此时前端对应的功能可能还没有开发完成，导致无法进行前后端联调测试。所以在开发阶段，后端测试主要以接口文档测试为主。

### 代码完善

- 录入的用户名已存在，抛出异常后没有处理
- 新增员工时，创建人id和修改人id设置为了固定值

`ThreadLocal` 并不是一个Thread，而是Thread的局部变量。

`ThreadLocal`为每个线程提供单独一份存储空间，具有线程隔离的效果， 只有在线程内才能获取到对应的值，线程外则不能访问。

`ThreadLocal`常用方法：

- ```java
  public void set(T value) //设置当前线程的线程局部变量的值
  ```

- ```java
  public T get() //返回当前线程所对应的线程局部变量的值
  ```

- ```java
  public void remove() //移除当前线程的线程局部变量
  ```

## 员工分页查询

### 需求分析和设计

![image-20241012195812992](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241012195812992.png)

**业务规则** ： 

- 根据页码展示员工信息
- 每页展示10条数据
- 分页查询时可以根据需要，输入员工姓名进行查询

![image-20241012195931152](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241012195931152.png)

### 代码开发

```java
    /**
     * 员工分页查询
     * @param employeePageQueryDTO
     * @return
     */
    @GetMapping("/page")
    @ApiOperation("员工分页查询")
    public Result<PageResult> page(EmployeePageQueryDTO employeePageQueryDTO){
        log.info("员工分页查询， 参数为：{}", employeePageQueryDTO);
        PageResult pageResult = employeeService.pageQuery(employeePageQueryDTO);
        return Result.success(pageResult);
    }
```

根据分页查询接口设计对应的DTO：

解决方式：

- 方式一：在属性上加入注解，对日期进行格式化

```java
    //更新时间
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime updateTime;
```

- 方式二：在`WebMvcConfiguration`中扩展`Spring MVC`的消息转换器，统一对日期类型进行格式化处理

```java
/**
 * 扩展mvc框架的消息转换器
 * @param converters
 */
protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
    Log.info("开始扩展消息转换器...");

    // 创建一个消息转换器对象
    MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter();
    // 设置对象转换器，可以将Java对象转为json字符串
    converter.setObjectMapper(new JacksonObjectMapper());

    // 将我们自己的转换器放入Spring MVC框架的容器中
    converters.add(0, converter);
}
```

## 启用禁用员工账号

业务规则：

- 可以对状态为”启用“的员工账号进行“禁用”操作
- 可以对状态为“禁用”的员工账号进行“启用”操作
- 状态为“禁用”的员工账号不能登录系统

### 代码开发

![image-20241012210913594](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241012210913594.png)

```java
    /**
     * 启用禁用员工账号
     * @param status
     * @param id
     * @return
     */
    @PostMapping("status/{status}")
    @ApiOperation("启用禁用员工账号")
    public Result startOrStop(@PathVariable("status") Integer status, Long id){
        log.info("启用禁用员工账号：{}, {}", status, id);
        employeeService.startOrStop(status, id);
        return Result.success();
    }
```

## 编辑员工

涉及到两个接口：

- 根据id查询员工信息
- 编辑员工信息

![image-20241012213700270](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241012213700270.png)

![image-20241012213807135](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241012213807135.png)

### 代码开发

```java
    @GetMapping("/{id}")
    @ApiOperation("根据id查询员工信息")
    public Result<Employee> getById(@PathVariable Long id) {
        Employee employee = employeeService.getById(id);
        return Result.success(employee);
    }

	@PutMapping
    @ApiOperation("编辑修改员工信息")
    public Result update(@RequestBody EmployeeDTO employeeDTO) {
        log.info("编辑员工信息{}", employeeDTO);
        employeeService.update(employeeDTO);
        return Result.success();
    }
```

## 导入分类模块功能代码

业务规则：

- 分类名称必须是唯一的
- 分类按照类型可以分为菜品分类和套餐分类
- 新添加的分类状态默认为“禁用”



