---
title : '苍穹外卖后端开发(Day5)'
date : 2024-10-15T22:30:13+08:00
lastmod: 2024-10-15T22:20:13+08:00
description : "苍穹外卖后端开发(Day5)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day5)

## Redis

Redis 是一个基于内存的key-value结构数据库的

- 基于内存存储，读写性能高
- 适合存储热点数据（热点商品，资讯，新闻）
- 企业应用广泛

### Redis安装和使用

安装Redis

启动Redis

```shell
redis-server.exe redis.windows.conf
```

![image-20241016112028226](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241016112028226.png)

之后在另一个窗口中连接Redis

```shell
redis-cli.exe -h localhost -p 6379 -a 123456
```

![image-20241016112335006](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241016112335006.png)

其中`-a`后面是密码， 默认没有密码，

![image-20241016112512020](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241016112512020.png)

## 五种常用数据类型介绍

Redis存储的是key-value结构的数据，其中key是字符串类型，value有5种常用的数据类型：

- 字符串 string
- 哈希 hash 
- 列表 list 
- 集合 set 
- 有序集合 sorted set / zset 

![image-20241016113738897](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241016113738897.png)

- 字符串（`string`）：普通字符串，Redis种最简单的数据类型
- 哈希（`hash`）：也叫散列，类似于Java种HashMap结构
- 列表（`list`）：按照插入顺序排序，可以有重复元素，类似于Java种的LinkedList
- 集合（set）：无序集合，没有重复元素，类似于Java种的HashSet
- 有序集合（sorted set / zset）：集合种每个元素关联一个分数（score），根据分数升序排列，没有重复元素

## Redis命令

### 字符串操作命令

- SET key value 设置指定key的值
- GET key 获取指定key的值
- SETEX key seconds value 设置指定 key 的值， 并将 key 的过期时间设为seconds 秒
- SETNX key value 只有在key不存在时设置key的值

### 哈希操作命令

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash特别适合用于存储对象，常用命令：

- HSET key field value 将哈希表 key 种的字段field的值设为 value
- HGET key field 获取存储在哈希表中指定字段的值
- HDEL key field 删除存储在哈希表种的指定字段
- HKEYS key 获取哈希表种所有字段
- HVALS key 获取哈希表种所有值

### 列表操作命令

Redis列表是最简单的字符串列表，按照插入顺序排序，常用命令：

- LPUSH key value1 [value2] 将一个或多个值插入到列表头部
- LRANGE key start stop 获取列表指定范围内的元素
- RPOP key 移除并获取列表最后一个元素
- LLEN key 获取列表长度

### 集合操作命令

Redis set 是 string 类型的无序集合。集合成员是唯一的，集合中不能出现重复的数据，常用命令：

- SADD key member1 [member2] 向集合添加一个或多个成员
- SMEMBERS key 返回集合中的所有成员
- SCARD key 获取集合的成员数
- SINTER key1 [key2] 返回给定所有集合的交集
- SUNION key1 [key2] 返回所有给定集合的并集
- SREM key member1 [member2] 删除集合种一个或多个成员

![image-20241016171022817](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241016171022817.png)

### 有序集合操作命令

Redis有序集合是string类型元素的集合， 且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令：

- ZADD key score1 member1 [score2 member2] 向有序集合添加一个或多个成员
- ZRANGE key start stop [WITHSCORES] 通过索引区间返回有序集合种指定区间内的成员
- ZINCRBY key increment member 有序集合中对指定成员的分数加上增量 increment
- ZREM key member [member … ] 移除有序集合中的一个或多个成员

![image-20241016173955524](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241016173955524.png)

### 通用命令

Redis 的通用命令是不分数据类型的，都可以使用的命令：

- KEYS pattern 查找所有符合给定模式（pattern）的key
- EXISTS key 检查给定key是否存在
- TYPE key 返回key所存储的值的类型
- DEL key 用于在key存在时删除key

## Redis的Java客户端

- Jedis 
- Lettuce 
- Spring Data Redis 

Spring Data Redis 是 Spring 的一部分， 对 Redis 底层开发包进行了高度封装。

在Spring项目中，可以使用Spring Data Redis来简化操作。

## Spring Data Redis 使用方式

操作步骤：

1. 导入Spring Data Redis 的 maven 坐标
2. 配置Redis数据源
3. 编写配置类，创建RedisTemplate对象
4. 通过RedisTemplate对象操作Redis 

### pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### application.yml

```yml
spring:
  redis:
    host: localhost
    port: 6379
    password: 123456
```

### 配置类

```java
@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        Log.info("开始创建redis模板类...");
        RedisTemplate redisTemplate = new RedisTemplate();
        // 设置Key的序列化器，默认为JdkSerializationRedisSerializer
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        return redisTemplate;
    }

}
```

### Java程序中进行字符串操作
