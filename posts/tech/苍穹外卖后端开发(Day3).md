---
title : '苍穹外卖后端开发(Day3)'
date : 2024-10-14T22:30:13+08:00
lastmod: 2024-10-14T22:20:13+08:00
description : "苍穹外卖后端开发(Day3)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day3)

## 菜品管理

### 公共字段自动填充

对于创建时间、创建人id，修改时间，修改人id等字段 重复性代码进行统一编写

![image-20241014140028159](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241014140028159.png)

#### 自定义注解`AutoFill`，用于标识需要进行公共字段自动填充的方法

```java
package com.sky.annotation;

/**
 * 自定义注解，用于标识某个方法需要进行功能字段自动填充处理。
 * 这个注解可以应用于方法上，用于指示在特定的数据库操作时
 * 需要自动填充某些功能字段。
 */
@Target(ElementType.METHOD) // 指定注解可以用于方法
@Retention(RetentionPolicy.RUNTIME) // 指定注解在运行时可用
public @interface AutoFill {
    /**
     * 数据库操作类型：用于指示自动填充的操作类型。
     * 可选值包括 UPDATE 和 INSERT。
     * 
     * @return 操作类型
     */
    OperationType value();
}
```

#### 自定义切面类`AutoFillAspect`， 统一拦截加入了`AutoFill`注解的方法，通过反射为公共字段赋值。

```java
package com.sky.aspect;

/**
 * 自定义切面， 实现公共字段自动填充
 */
@Aspect
@Component
@Slf4j
public class AutoFillAspect {

    /**
     * 定义切入点，匹配com.sky.mapper包下的所有方法，并且方法上需有@AutoFill注解
     */
    @Pointcut("execution(* com.sky.mapper.*.*(..)) && @annotation(com.sky.annotation.AutoFill)")
    public void autoFillPointCut() {
    }

    /**
     * 前置通知，在目标方法执行前进行公共字段赋值
     *
     * @param joinPoint 连接点，表示被拦截的方法调用
     */
    @Before("autoFillPointCut()")
    public void autoFill(JoinPoint joinPoint) {
        log.info("开始进行公共字段自动填充...");

        // 获取当前被拦截方法的签名（方法信息）
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        // 从方法签名中获取@AutoFill注解
        AutoFill autoFill = signature.getMethod().getAnnotation(AutoFill.class);
        // 获取操作类型（INSERT或UPDATE）
        OperationType operationType = autoFill.value();

        // 获取目标方法的参数（假设第一个参数是实体对象）
        Object[] args = joinPoint.getArgs();
        if (args == null || args.length == 0) {
            return;  // 如果没有参数则直接返回
        }

        // 获取实体对象（目标方法的第一个参数）
        Object entity = args[0];
        // 获取当前时间和当前操作用户ID
        LocalDateTime now = LocalDateTime.now();
        Long currentId = BaseContext.getCurrentId();

        // 根据操作类型判断是INSERT还是UPDATE操作
        if (operationType == OperationType.INSERT) {
            try {
                // 通过反射调用实体对象的set方法，赋值创建和更新时间、创建和更新用户
                Method setCreateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_CREATE_TIME, LocalDateTime.class);
                Method setCreateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_CREATE_USER, Long.class);
                Method setUpdateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_TIME, LocalDateTime.class);
                Method setUpdateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_USER, Long.class);

                // 调用方法进行赋值
                setCreateTime.invoke(entity, now);
                setCreateUser.invoke(entity, currentId);
                setUpdateTime.invoke(entity, now);
                setUpdateUser.invoke(entity, currentId);
            } catch (Exception e) {
                e.printStackTrace();  // 捕获异常并打印
            }
        } else if (operationType == OperationType.UPDATE) {
            try {
                // 通过反射调用实体对象的set方法，赋值更新时间和更新用户
                Method setUpdateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_TIME, LocalDateTime.class);
                Method setUpdateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_USER, Long.class);

                // 调用方法进行赋值
                setUpdateTime.invoke(entity, now);
                setUpdateUser.invoke(entity, currentId);
            } catch (Exception e) {
                e.printStackTrace();  // 捕获异常并打印
            }
        }
    }
}
```

#### 在`Mapper`的方法上加入`AutoFill`注解

![image-20241014144841925](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241014144841925.png)

```java
@AutoFill(value = OperationType.INSERT)
@AutoFill(value = OperationType.UPDATE)
```

## 新增菜品

### 产品原型

![image-20241014145217798](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241014145217798.png)

### 业务规则：

- 菜品名称必须是唯一的
- 菜品必须属于某个分类下，不能单独存在
- 新增菜品时可以根据情况选择菜品的口味
- 每个菜品必须对应一张图片

### 接口设计：

- 根据类型查询分配
- 文件上传
- 新增菜品

### 数据库设计（dish菜品表和dish_flavor口味表）：

![image-20241014145855210](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241014145855210.png)

配置OSS图像存储服务

可以通过下面的Properties文件配置OSS的属性。

```java
package com.sky.properties;

@Component
@ConfigurationProperties(prefix = "sky.alioss")
@Data
public class AliOssProperties {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

}
```

![image-20241014170935265](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241014170935265.png)

在`application-dev`中详细配置id, key等。

![image-20241014171007674](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241014171007674.png)

### 通用接口

```java
package com.sky.controller.admin;

/**
 * 通用接口
 */
@RestController
@RequestMapping("/admin/common")
@Api(tags = "通用接口")
@Slf4j
public class CommonController {

    @Autowired
    private AliOssUtil aliOssUtil;

    /**
     * 文件上传
     * @param file
     * @return
     */
    @PostMapping("/upload")
    public Result<String> upload(MultipartFile file){
        log.info("文件上传：{}",file);
        try {
            // 原始文件名
            String originalFilename = file.getOriginalFilename();
            // 截取原始文件名的后缀
            String extension = originalFilename.substring(originalFilename.lastIndexOf('.'));
            // 构造新文件名称
            String objectName = UUID.randomUUID().toString() + extension;

            // 文件的请求路径
            String filePath = aliOssUtil.upload(file.getBytes(), objectName);
            return Result.success(filePath);
        } catch (IOException e) {
            log.error("文件上传失败：{}", e);
        }
        return Result.error(MessageConstant.UPLOAD_FAILED);
    }
}
```

### 菜品接口

```java
package com.sky.controller.admin;

@RestController
@RequestMapping("/admin/dish")
@Api(tags = "菜品相关接口")
@Slf4j
public class DishController {

    @Autowired
    private DishService dishService;

    @PostMapping
    @ApiOperation("新增菜品")
    public Result save(@RequestBody DishDTO dishDTO){
        log.info("新增菜品：{}", dishDTO);
        dishService.saveWithFlavor(dishDTO);
        return Result.success();
    }
}
```

在`DishServiceImpl`中实现`saveWithFlavor`

```java
package com.sky.service.impl;

@Service
public class DishServiceImpl implements DishService {
    @Autowired
    private DishMapper dishMapper;
    @Autowired
    private DishFlavorMapper dishFlavorMapper;

    /**
     * 新增菜品和对应的口味
     * @param dishDTO
     */
    @Transactional
    public void saveWithFlavor(DishDTO dishDTO) {
        Dish dish = new Dish();

        BeanUtils.copyProperties(dishDTO, dish);

        // 向菜品表插入1条数据
        dishMapper.insert(dish);

        Long dishId = dish.getId();

        List<DishFlavor> flavors = dishDTO.getFlavors();
        if (flavors != null && flavors.size() > 0) {
            flavors.forEach(dishFlavor ->
                    dishFlavor.setDishId(dishId));
            // 向口味表插入n条数据
            dishFlavorMapper.insertBatch(flavors);
        }
    }
}
```

### 在Mapper中实现SQL

#### DishMapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.DishMapper">

    <insert id="insert" useGeneratedKeys="true" keyProperty="id">
        insert into dish (name, category_id, price, image, description, create_time, update_time, create_user, update_user)
            values
            (#{name}, #{categoryId}, #{price}, #{image}, #{description}, #{createTime}, #{updateTime}, #{createUser}, #{updateUser})
    </insert>
</mapper>

```

- `useGeneratedKeys="true"`: 表示在插入操作后，使用数据库生成的主键值。这个选项通常与主键自增（如 MySQL 的 AUTO_INCREMENT）一起使用。

- `keyProperty="id"`: 指定存储生成主键值的对象属性。在插入成功后，数据库生成的主键值会被设置到这个属性上。

#### DishFlavorMapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.DishFlavorMapper">
    <insert id="insertBatch">
        insert into dish_flavor(dish_id, name, value) VALUES
        <foreach collection="flavors" item="df" separator=",">
            (#{df.dishId}, #{df.name}, #{df.value})
        </foreach>
    </insert>
</mapper>
```

## 菜品分页查询

### 需求分析和设计

![image-20241015141155451](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241015141155451.png)

####  DishController

```java
/**
 * 菜品分页查询
 * @param dishPageQueryDTO
 * @return
 */
@GetMapping("/page")
@ApiOperation("菜品分页查询")
public Result<PageResult> page(DishPageQueryDTO dishPageQueryDTO){
    log.info("菜品分页查询{}", dishPageQueryDTO);
    PageResult pageResult = dishService.pageQuery(dishPageQueryDTO);
    return Result.success(pageResult);
}
```

#### 实现pageQuery

```java
@Override
public PageResult pageQuery(DishPageQueryDTO dishPageQueryDTO) {
    PageHelper.startPage(dishPageQueryDTO.getPage(), dishPageQueryDTO.getPageSize());
    Page<DishVO> page = dishMapper.pageQuery(dishPageQueryDTO);
    return new PageResult(page.getTotal(), page.getResult());
}
```

#### Mapper

```xml
    <select id="pageQuery" resultType="com.sky.vo.DishVO">
        select d.*, c.name as categoryName from dish d left join category c on d.category_id = c.id
        <where>
            <if test="name != null">
                and d.name like concat('%', #{name},'%')
            </if>
            <if test="categoryId != null">
                and d.category_id = #{categoryId}
            </if>
            <if test="status != null">
                and d.status = #{status}
            </if>
        </where>
        order by d.create_time desc
    </select>
```

## 菜品删除

业务规则：

- 可以一次删除一个菜品，也可以批量删除多个菜品
- 启售的菜品不能删除
- 被套餐关联的菜品不能删除
- 删除菜品后，关联的口味数据也需要删除掉

```java
@DeleteMapping
@ApiOperation("菜品批量删除")
public Result delete(@RequestParam List<Long> ids){
    log.info("菜品批量删除:{}",ids);
    dishService.deleteBatch(ids);
    return Result.success();
}
```
