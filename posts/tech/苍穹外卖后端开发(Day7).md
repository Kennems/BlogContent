---
title : '苍穹外卖后端开发(Day7)'
date : 2024-10-18T22:30:13+08:00
lastmod: 2024-10-18T22:20:13+08:00
description : "苍穹外卖后端开发(Day7)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day7)

## 缓存菜品

用户端小程序展示的菜品数据都是通过查询数据库获得，如果用户端访问量比较大，数据库访问的压力随之增大。

#### 实现思路

通过Redis来缓存菜品数据，减少数据库查询操作。



缓存逻辑分析：

- 每个分类下的菜品保存一份缓存数据
- 数据库中菜品数据有变更时清理缓存数据

```java
    /**
     * 根据分类id查询菜品
     *
     * @param categoryId
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("根据分类id查询菜品")
    public Result<List<DishVO>> list(Long categoryId) {

        // 构造redis中的 key, 规则：dish_分类id
        String key = "dish_" + categoryId;

        // 查询redis中是否存在菜品数据
        List<DishVO> list = (List<DishVO>) redisTemplate.opsForValue().get(key);
        if (list != null && list.size()> 0) {
            // 如果存在，直接返回，无须查询数据库
            return Result.success(list);
        }

        Dish dish = new Dish();
        dish.setCategoryId(categoryId);
        dish.setStatus(StatusConstant.ENABLE);//查询起售中的菜品

        // 如果不存在，查询数据库，将查询到的数据放入redis中
        list = dishService.listWithFlavor(dish);
        redisTemplate.opsForValue().set(key, list);

        return Result.success(list);
    }
```

### Spring Cache

Spring Cache 是一个框架，实现了基于**注解**的缓存功能，只需要简单地加一个注解，就能实现缓存功能

Spring Cache 提供了一层抽象，底层可以切换不同的缓存实现，例如：

- EHCache

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
    <version>2.7.3</version>
</dependency>
```

- Caffeine
- Redis 

### 常用注解

| 注解             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| `@EnableCaching` | 开启缓存注解功能，通常加在启动类上                           |
| `@Cacheable`     | 在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中。 |
| `@CachePut`      | 将方法的返回值放到缓存中                                     |
| `@CacheEvict`    | 将一条或多条数据从缓存中删除                                 |



## 缓存套餐



## 添加购物车

```java
/**
 * 添加购物车
 * @param shoppingCartDTO
 * @return
 */
@PostMapping("/add")
@ApiOperation("添加购物车")
public Result add(@RequestBody ShoppingCartDTO shoppingCartDTO){
    log.info("添加购物车，商品信息为：{}",shoppingCartDTO);

    shoppingCartService.addShoppingCart(shoppingCartDTO);
    return Result.success();
}
```



## 查看购物车

```java
    @GetMapping("/list")
    @ApiOperation("查看购物车")
    public Result<List<ShoppingCart>> list(){
        List<ShoppingCart> list = shoppingCartService.showShoppingCart();
        return Result.success(list);
    }

```



## 清空购物车

```

```



## 导入地址簿功能



## 用户下单

需求分析和设计

用户下单业务说明：

再电商系统中，用户是通过下单的方式通知商家，用户已经购买了商品，需要商家进行备货和发货。

### 数据库设计

- 订单表 orders 
- 订单明细表 order_detail 





## 订单支付



## 内网穿透 Cpolar

```shell
cpolar.exe http 80
```

将对应的端口号进行内网穿透
