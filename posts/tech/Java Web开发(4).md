---
title : 'Java Web开发(4)'
date : 2024-04-27T22:30:13+08:00
lastmod: 2024-04-27T22:20:13+08:00
description : "JavaWeb学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["JavaWeb学习笔记"]
tags : ["Java", "Web"]
# password : leetcode
---

# Java Web开发(4)

## 数据库

- 数据库：DataBase(DB) ， 是存储和管理数据的仓库
- DBMS， SQL

MySql登录，首先在服务中启动mysql

```bash
mysql -uroot -p"password"
```

或者

```bash
mysql -uroot -p
```

之后输入密码

连接远程数据库

```bash
mysql -h192.168.88.130 -P3306 -utopeet -p"password"
```

查看当前的数据库

```sql
 select database();
```

**关系型数据库（RDBMS）**：建立在关系模型基础上，由多张相互连接的二维表组成的数据库

SQL：一门操作关系型数据库的编程语言，定义操作所有关系型数据库的统一标准

- SQL语句可以单行或多行书写，以分号结尾
- SQL语句可以使用空格/缩进来增强语句的可读性
- MySQL数据库的SQL语句不区分大小写
- 注释：
  - 单行注释： -- 注释内容 或者 #注释内容（MySQL特有）
  - 多行注释：/* 注释内容 */

```sql
create database db02;
```

DDL， DML， DQL， DCL

```sql
create database if not exists db02;
```

```sql
drop database if exists db03;
```

```sql
show databases;
```

```sql
select database();
show schemas;
```

### DDL

#### 创建表

约束：

- 非空约束 **not null**
- 唯一约束 **unique**
- 主键约束 **primary key** （auto_increment自增）
- 默认约束 **default**
- 外键约束 **foreign key** (至少有两张表)

#### 数据类型

主要分为三类：数值类型，字符串类型，日期时间类型

**数值类型**：

| 类型      | 大小 | 有符号范围                   | 无符号范围         | 描述               |                                              |
| --------- | ---- | ---------------------------- | ------------------ | ------------------ | -------------------------------------------- |
| tinyint   | 1    | （-128， 127）               | （0， 255）        | 小整数值           |                                              |
| smallint  | 2    | （-32768, 32767)             | （0, 65535)        | 大整数值           |                                              |
| mediumint | 3    | （-8388608， 8388607）       | （0， 16777215）   | 大整数值           |                                              |
| int       | 4    | （-2147483647， 2147483648） | （0， 4294967295） | 大整数值           |                                              |
| bigint    | 8    | （-2^63, 2^63-1)             | （0， 2^64-1)      | 极大整数值         |                                              |
| float     | 4    |                              |                    | 单精度浮点值       | float(5, 2)表示整个数字长度为5， 小数位数为2 |
| double    | 8    |                              |                    | 双精度浮点值       |                                              |
| decimal   |      |                              |                    | 小数值（精度更高） | 金额计算                                     |

查询当前数据库的所有表：

```sql
show tables;
```

查询表结构：

```sql
desc 表名
```

查询建表语句：

```sql
show create table 表名
```

```sql
-- 员工管理(带约束)
create table tb_emp
(
    id          int unsigned primary key auto_increment comment 'ID',
    username    varchar(20)      not null unique comment '用户名',
    password    varchar(32) default '123456' comment '密码',
    name        varchar(10)      not null comment '姓名',
    gender      tinyint unsigned not null comment '性别, 说明: 1 男, 2 女',
    image       varchar(300) comment '图像',
    job         tinyint unsigned comment '职位, 说明: 1 班主任,2 讲师, 3 学工主管, 4 教研主管',
    entrydate   date comment '入职时间',
    create_time datetime         not null comment '创建时间',
    update_time datetime         not null comment '修改时间'
) comment '员工表';
```

```sql
alter table tb_emp add qq varchar(11) comment  'QQ';

alter table tb_emp modify qq varchar(13) comment 'QQ';

alter table tb_emp change qq qq_num varchar(13) comment 'QQ';

alter table tb_emp drop column qq_num;

rename table tb_emp to emp;

drop table if exists tb_emp;
```

### DML

**Data Manipulation Language(数据操作语言)**， 用来对数据库中表的数据记录进行增，删，改操作。

```sql
insert into tb_emp(username,name,gender,create_time,update_time) values ('Kennem', '张无忌', '1',now(),now());

# 插入的内容包含所有字段（可以省略所有字段）
insert into tb_emp(id, username, password, name, gender, image, job, entrydate, create_time, update_time)
values (null, 'zhiruo', '123', 'kdkd', 2, '1.jpg', 1, '2021-01-20',now(), now());

# 插入多组数据
insert into tb_emp(username, password, name, gender, image, job, entrydate, create_time, update_time)
values ('Oliver', '123', 'kdkd', 2, '1.jpg', 1, '2021-01-20',now(), now()),('Linux', '123', 'kdkd', 2, '1.jpg', 1, '2021-01-20',now(), now());

# 更新数据
update tb_emp set name = '张三', update_time = now() where id = 1;

# 更新所有数据的此字段
update tb_emp set entrydate = '2010-01-01', update_time = now(); # 更新所有行

# 删除id=1的数据
delete from emp where id=1;

# 删除所有数据
delete from emp;
# delete不能删除某一字段的值，可以用update将这一字段设置为NULL
```

### DQL

- **Data Query Language（数据查询语言）**， 用来查询数据库表中的记录
- 关键字：**SELECT**

```sql
select 
	
from
	
where
	
group by

having

order by

limit
```

聚合函数：

- count
- max
- min
- avg
- sum

```sql
select name, entrydate from tb_emp;

select tb_emp.id, tb_emp.username, tb_emp.password, tb_emp.name, tb_emp.gender, tb_emp.image, tb_emp.job, tb_emp.entrydate, tb_emp.create_time, tb_emp.update_time from tb_emp;

select *
from tb_emp;

select name as 姓名, entrydate as 入职日期 from tb_emp;


select name as '姓 名', entrydate as 入职日期 from tb_emp;

select distinct job from tb_emp;
```

```sql
-- 选择tb_emp表中的name和entrydate字段
select name, entrydate
from tb_emp;

-- 选择tb_emp表中的所有字段，并且显式列出它们
select tb_emp.id,
       tb_emp.username,
       tb_emp.password,
       tb_emp.name,
       tb_emp.gender,
       tb_emp.image,
       tb_emp.job,
       tb_emp.entrydate,
       tb_emp.create_time,
       tb_emp.update_time
from tb_emp;

-- 选择tb_emp表中的所有字段（不直观，性能较低）
select *
from tb_emp;

-- 选择tb_emp表中的name和entrydate字段，并将它们重命名为“姓名”和“入职日期”
select name as 姓名, entrydate as 入职日期
from tb_emp;

-- 选择tb_emp表中的name和entrydate字段，并将它们重命名为“姓 名”和“入职日期”
select name '姓 名', entrydate 入职日期
from tb_emp;

-- 选择tb_emp表中不同的job字段值
select distinct job
from tb_emp;

-- 从tb_emp表中选择name为“杨逍”的记录
select *
from tb_emp
where name = '杨逍';

-- 从tb_emp表中选择id小于等于5的记录
select *
from tb_emp
where id <= 5;

-- 从tb_emp表中选择job字段为null的记录
select *
from tb_emp
where job is null;

-- 从tb_emp表中选择job字段不为null的记录
select *
from tb_emp
where job is not null;

-- 从tb_emp表中选择password字段不等于“123456”的记录
select *
from tb_emp
where password != '123456';

-- 从tb_emp表中选择password字段不等于“123456”的记录（另一种写法）
select *
from tb_emp
where password <> '123456';

-- 从tb_emp表中选择entrydate在2000-01-01到2010-01-01之间的记录
select *
from tb_emp
where entrydate >= '2000-01-01'
  and entrydate <= '2010-01-01';

-- 从tb_emp表中选择entrydate在2000-01-01到2010-01-01之间的记录（使用between）
select *
from tb_emp
where entrydate between '2000-01-01' and '2010-01-01';

-- 重复的语句，选择entrydate在2000-01-01到2010-01-01之间的记录（使用between）
select *
from tb_emp
where entrydate between '2000-01-01' and '2010-01-01';

-- 重复的语句，选择entrydate在2000-01-01到2010-01-01之间的记录
select *
from tb_emp
where entrydate >= '2000-01-01'
  and entrydate <= '2010-01-01';

-- 从tb_emp表中选择entrydate在2000-01-01到2010-01-01之间，且gender为2的记录
select *
from tb_emp
where entrydate >= '2000-01-01'
  and entrydate <= '2010-01-01'
  and gender = 2;

-- 从tb_emp表中选择job为2、3或4的记录
select *
from tb_emp
where job = 2
   or job = 3
   or job = 4;

-- 从tb_emp表中选择job在2, 3, 4中的记录（使用in）
select *
from tb_emp
where job in (2, 3, 4);

-- 使用通配符_表示单个字符，选择name字段长度为2的记录
select *
from tb_emp
where name like '__';

-- 使用通配符_表示单个字符，选择name字段长度为3的记录
select *
from tb_emp
where name like '___';

-- 使用通配符%表示任意个字符，选择name以“张”开头的记录
select *
from tb_emp
where name like '张%';

-- 计数id字段的非空值
select count(id)
from tb_emp;

-- 计数表中的记录数，常量'A'对计数没有影响
select count('A')
from tb_emp;

-- 推荐的计数表中记录数的写法
select count(*)
from tb_emp;

-- 选择tb_emp表中entrydate的最小值
select min(entrydate)
from tb_emp;

-- 选择tb_emp表中entry
-- 选择tb_emp表中entrydate的最大值
select max(entrydate)
from tb_emp;

-- 计算tb_emp表中id字段的平均值
select avg(id)
from tb_emp;

-- 计算tb_emp表中id字段的总和
select sum(id)
from tb_emp;

-- 按gender字段分组，并计数每个性别的记录数
select gender, count(*)
from tb_emp
group by gender;

-- 按job字段分组，并选择entrydate小于等于2015-01-01的记录
-- 只包括记录数大于等于2的组
select job, count(*)
from tb_emp
where entrydate <= '2015-01-01'
group by job
having count(*) >= 2;

-- 从tb_emp表中选择所有字段，并按entrydate字段升序排序
select * 
from tb_emp 
order by entrydate;

-- 从tb_emp表中选择所有字段，并按entrydate字段降序排序
select * 
from tb_emp 
order by entrydate DESC;

-- 从tb_emp表中选择所有字段，先按entrydate字段升序排序，
-- 对于相同entrydate的记录，再按update_time字段降序排序
select * 
from tb_emp 
order by entrydate, update_time DESC;

-- 从tb_emp表中选择所有字段，分页显示
-- 第二页的记录，每页显示5条记录
-- 起始索引为5，即从第6条记录开始（索引从0开始）
select * 
from tb_emp 
limit 5, 5;

-- 从tb_emp表中选择所有字段，分页显示
-- 第三页的记录，每页显示5条记录
-- 起始索引为10，即从第11条记录开始（索引从0开始）
select * 
from tb_emp 
limit 10, 5;
```

#### **面试题**

DQL-分组查询， where与having区别

- **执行时机不同**：where是分组之前进行过滤，不满足where条件，不参与分组，而having是分组之后对结果进行过滤
- **判断条件不同**：where不能对聚合函数进行判断，而having可以

**注意事项：**

- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段没有意义
- 执行顺序： where > 聚合函数 > having

### 案例

```sql
-- 从tb_emp表中选择指定字段，满足以下条件：
-- name包含“张”，gender为1，entrydate在2000-01-01到2015-12-31之间
-- 结果按update_time降序排序，并分页显示
-- 显示第2页的记录，每页10条记录，即从第11条到第20条记录
select tb_emp.id,
       tb_emp.username,
       tb_emp.password,
       tb_emp.name,
       tb_emp.gender,
       tb_emp.image,
       tb_emp.job,
       tb_emp.entrydate,
       tb_emp.create_time,
       tb_emp.update_time
from tb_emp
where name like '%张%'
  and gender = 1
  and entrydate between '2000-01-01' and '2015-12-31'
order by update_time DESC
limit 10, 10;

-- 使用IF函数，根据gender字段的值判断性别，并计数每个性别的记录数
-- 使用IF函数：如果gender为1，则显示“男性员工”；否则显示“女性员工”
select if(gender = 1, '男性员工', '女性员工') 性别, count(*) 人数
from tb_emp
group by gender;

-- 使用CASE语句，根据job字段的值确定职位名称，并计数每个职位的记录数
-- 使用CASE语句：当job为1时，显示“班主任”；当job为2时，显示“讲师”；
-- 当job为3时，显示“学工主管”；当job为4时，显示“教研主管”；否则显示“未分配职位”
select (case job
            when 1 then '班主任'
            when 2 then '讲师'
            when 3 then '学工主管'
            when 4 then '教研主管'
            else '未分配职位'
        end) 职位,
       count(*) 人数
from tb_emp
group by job;
```

## 多表设计

项目开发中，在进行数据表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系

- 一对多(多对一)
- 多对多
- 一对一

### 一对多

- 部门和员工

**物理外键**

- 概念：使用`foreign key`定义外键关联另外一张表。
- 缺点：
  - 影响增，删，改的效率（需要检查外键关系）
  - 仅用于单节点数据库，不适用与分布式，集群场景
  - 容易引发数据库的死锁问题，消耗性能

```sql
alter table tb_emp
    add constraint tb_emp___fk_dept_id
        foreign key (dept_id) references tb_dept (id);
```

**逻辑外键**

- 概念：在业务逻辑中，解决外键关联
- 通过逻辑外键，就可以很方便的解决上述问题

### 一对一

- **案例**：用户与身份证信息的关系
- **关系**：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他字段放在另一张表中，以提升操作效率

- **实现**：在任意一方加入外键，关联另外一方，并且设置外键为唯一的（**UNIQUE**）

```sql
-- 创建用户信息表 tb_user
create table tb_user
(
    id     int unsigned primary key auto_increment comment 'ID', -- 自增主键ID
    name   varchar(10)      not null comment '姓名', -- 用户姓名
    gender tinyint unsigned not null comment '性别, 1 男  2 女', -- 性别，1表示男，2表示女
    phone  char(11) comment '手机号', -- 手机号
    degree varchar(10) comment '学历' -- 学历
) comment '用户信息表'; -- 表注释

-- 插入用户信息表 tb_user 的数据
insert into tb_user
values 
(1, '白眉鹰王', 1, '18812340001', '初中'),
(2, '青翼蝠王', 1, '18812340002', '大专'),
(3, '金毛狮王', 1, '18812340003', '初中'),
(4, '紫衫龙王', 2, '18812340004', '硕士');

-- 创建用户详细信息表 tb_user_card
create table tb_user_card
(
    id           int unsigned primary key auto_increment comment 'ID', -- 自增主键ID
    nationality  varchar(10)  not null comment '民族', -- 民族
    birthday     date         not null comment '生日', -- 生日
    idcard       char(18)     not null comment '身份证号', -- 身份证号
    issued       varchar(20)  not null comment '签发机关', -- 签发机关
    expire_begin date         not null comment '有效期限-开始', -- 有效期限开始日期
    expire_end   date comment '有效期限-结束', -- 有效期限结束日期
    user_id      int unsigned not null unique comment '用户ID', -- 用户ID，唯一且非空
    constraint fk_user_id foreign key (user_id) references tb_user (id) -- 外键约束，引用 tb_user 表的 id 字段
) comment '用户信息表'; -- 表注释

-- 插入用户详细信息表 tb_user_card 的数据
insert into tb_user_card
values 
(1, '汉', '1960-11-06', '100000100000100001', '朝阳区公安局', '2000-06-10', null, 1),
(2, '汉', '1971-11-06', '100000100000100002', '静安区公安局', '2005-06-10', '2025-06-10', 2),
(3, '汉', '1963-11-06', '100000100000100003', '昌平区公安局', '2006-06-10', null, 3),
(4, '回', '1980-11-06', '100000100000100004', '海淀区公安局', '2008-06-10', '2028-06-10', 4);
```



### 多对多

- **案例**：学生与课程的关系
- **关系**：一个学生可以选修多门课程，一门课程也可以供多个学生选择

- 使用中间表来对应多对多的关系

```sql
-- 创建学生表 tb_student
create table tb_student
(
    id   int auto_increment primary key comment '主键ID', -- 自增主键ID
    name varchar(10) comment '姓名', -- 学生姓名
    no   varchar(10) comment '学号' -- 学生学号
) comment '学生表'; -- 表注释

-- 插入学生表 tb_student 的数据
insert into tb_student (name, no)
values 
('黛绮丝', '2000100101'),
('谢逊', '2000100102'),
('殷天正', '2000100103'),
('韦一笑', '2000100104');

-- 创建课程表 tb_course
create table tb_course
(
    id   int auto_increment primary key comment '主键ID', -- 自增主键ID
    name varchar(10) comment '课程名称' -- 课程名称
) comment '课程表'; -- 表注释

-- 插入课程表 tb_course 的数据
insert into tb_course (name)
values 
('Java'),
('PHP'),
('MySQL'),
('Hadoop');

-- 创建学生课程中间表 tb_student_course，用于表示学生和课程的多对多关系
create table tb_student_course
(
    id         int auto_increment comment '主键' primary key, -- 自增主键ID
    student_id int not null comment '学生ID', -- 学生ID，非空
    course_id  int not null comment '课程ID', -- 课程ID，非空
    constraint fk_courseid foreign key (course_id) references tb_course (id), -- 外键约束，引用 tb_course 表的 id 字段
    constraint fk_studentid foreign key (student_id) references tb_student (id) -- 外键约束，引用 tb_student 表的 id 字段
) comment '学生课程中间表'; -- 表注释

-- 插入学生课程中间表 tb_student_course 的数据，表示学生选择的课程
insert into tb_student_course (student_id, course_id)
values 
(1, 1), -- 学生 ID 1 选择了课程 ID 1 (Java)
(1, 2), -- 学生 ID 1 选择了课程 ID 2 (PHP)
(1, 3), -- 学生 ID 1 选择了课程 ID 3 (MySQL)
(2, 2), -- 学生 ID 2 选择了课程 ID 2 (PHP)
(2, 3), -- 学生 ID 2 选择了课程 ID 3 (MySQL)
(3, 4); -- 学生 ID 3 选择了课程 ID 4 (Hadoop)
```

### 案例

参考页面原型及需求，设计合理的表结构

1. 阅读页面原型及需求文档，分析各个模块涉及到的表结构，及表结构之间的关系
2. 根据页面原型及需求文档，分析各个表结构中具体的字段及约束

### 总结

1. 一对多

   在多的一方添加外键，关联另外一方的主键

2. 一对一

   任意一方，添加外键，关联另外一方的主键

3. 多对多

   通过中间表来维护，中间表的两个外键，分别关联另外两个表的主键

## 多表查询

从多张表中查询数据

分类

- 连接查询
  - 内连接：相当于查询A， B交集部分数据

```sql
-- 这是一个笛卡尔积查询，返回tb_emp和tb_dept表的所有组合。
select *
from tb_emp,          -- tb_emp 表（员工表）
     tb_dept;         -- tb_dept 表（部门表）

-- 这是一个内连接查询，返回tb_emp和tb_dept表中满足连接条件的所有记录。
select *
from tb_emp,          -- tb_emp 表（员工表）
     tb_dept          -- tb_dept 表（部门表）
where tb_emp.dept_id = tb_dept.id;  -- 连接条件：tb_emp表中的dept_id等于tb_dept表中的id

-- 这是一个内连接查询，只返回员工的姓名和所属部门的名称。
select tb_emp.name,          -- tb_emp 表中的 name 列（员工姓名）
       tb_dept.name          -- tb_dept 表中的 name 列（部门名称）
from tb_emp,                 -- tb_emp 表（员工表）
     tb_dept                 -- tb_dept 表（部门表）
where tb_emp.dept_id = tb_dept.id;  -- 连接条件：tb_emp表中的dept_id等于tb_dept表中的id

-- 这是一个使用INNER JOIN关键字的内连接查询，返回员工的姓名和所属部门的名称。
select tb_emp.name,          -- tb_emp 表中的 name 列（员工姓名）
       tb_dept.name          -- tb_dept 表中的 name 列（部门名称）
from tb_emp                  -- tb_emp 表（员工表）
         inner join tb_dept  -- tb_dept 表（部门表），使用INNER JOIN关键字进行连接
                    on tb_emp.dept_id = tb_dept.id;  -- 连接条件：tb_emp表中的dept_id等于tb_dept表中的id

-- 这是一个使用JOIN关键字的内连接查询，返回员工的姓名和所属部门的名称。
select tb_emp.name,          -- tb_emp 表中的 name 列（员工姓名）
       tb_dept.name          -- tb_dept 表中的 name 列（部门名称）
from tb_emp                  -- tb_emp 表（员工表）
         join tb_dept        -- tb_dept 表（部门表），使用JOIN关键字进行连接
                    on tb_emp.dept_id = tb_dept.id;  -- 连接条件：tb_emp表中的dept_id等于tb_dept表中的id

-- 这是一个使用表别名的内连接查询，返回员工的姓名和所属部门的名称。
select e.name,               -- e 表（tb_emp 表的别名）中的 name 列（员工姓名）
       p.name                -- p 表（tb_dept 表的别名）中的 name 列（部门名称）
from tb_emp e,               -- tb_emp 表（员工表），起别名为 e
     tb_dept p               -- tb_dept 表（部门表），起别名为 p
where e.dept_id = p.id;      -- 连接条件：e 表（tb_emp 表）的 dept_id 等于 p 表（tb_dept 表）的 id

```

- 外连接：
  - 左外连接：查询左表所有数据（包括两张表交集部分数据
  - 右外连接：查询右表所有数据（包括两张表交集部分数据）

```sql
-- 左连接：查询所有员工及其所属部门的名称，包含没有部门的员工
select e.name,       -- e 表（tb_emp 表的别名）中的 name 列（员工姓名）
       p.name        -- p 表（tb_dept 表的别名）中的 name 列（部门名称）
from tb_emp e        -- tb_emp 表（员工表），起别名为 e
         left join tb_dept p on e.dept_id = p.id;  -- 左连接：tb_emp表中的dept_id等于tb_dept表中的id

-- 右连接：查询所有部门及其员工的姓名，包含没有员工的部门
select e.name,       -- e 表（tb_emp 表的别名）中的 name 列（员工姓名）
       p.name        -- p 表（tb_dept 表的别名）中的 name 列（部门名称）
from tb_emp e        -- tb_emp 表（员工表），起别名为 e
         right join tb_dept p on e.dept_id = p.id;  -- 右连接：tb_emp表中的dept_id等于tb_dept表中的id

-- 左连接：查询所有部门及其员工的姓名，包含没有员工的部门（和上面的右连接查询结果相同）
select e.name,       -- e 表（tb_emp 表的别名）中的 name 列（员工姓名）
       p.name        -- p 表（tb_dept 表的别名）中的 name 列（部门名称）
from tb_dept p       -- tb_dept 表（部门表），起别名为 p
         left join tb_emp e on p.id = e.dept_id;  -- 左连接：tb_dept表中的id等于tb_emp表中的dept_id

```

- 子查询

```sql
-- 子查询：查询id等于教研部的部门id的员工信息
select *
from tb_emp           -- tb_emp 表（员工表）
where id = (select id from tb_dept where tb_dept.name = '教研部');  -- 子查询：从tb_dept表中选择名称为'教研部'的部门id

-- 标量子查询：查询入职日期晚于方东白的员工信息
select *
from tb_emp           -- tb_emp 表（员工表）
where entrydate > (select tb_emp.entrydate from tb_emp where name = '方东白');  -- 子查询：从tb_emp表中选择名称为'方东白'的员工入职日期

-- 列子查询：查询部门为教研部或咨询部的员工信息
select *
from tb_emp           -- tb_emp 表（员工表）
where dept_id in (select id from tb_dept where name in ('教研部', '咨询部'));  -- 子查询：从tb_dept表中选择名称为'教研部'或'咨询部'的部门id

-- 行子查询：查询入职日期和职位均与韦一笑相同的员工信息
select *
from tb_emp           -- tb_emp 表（员工表）
where entrydate = (select entrydate from tb_emp where name = '韦一笑')  -- 子查询：从tb_emp表中选择名称为'韦一笑'的员工入职日期
  and job = (select job from tb_emp where name = '韦一笑');  -- 子查询：从tb_emp表中选择名称为'韦一笑'的员工职位

-- 行子查询的另一种写法：查询入职日期和职位均与韦一笑相同的员工信息
select *
from tb_emp           -- tb_emp 表（员工表）
where (entrydate, job) = (select entrydate, job from tb_emp where name = '韦一笑');  -- 子查询：从tb_emp表中选择名称为'韦一笑'的员工入职日期和职位

-- 表子查询：查询入职日期在2006-01-01之后的员工信息
select *
from tb_emp           -- tb_emp 表（员工表）
where entrydate > '2006-01-01';  -- 条件：入职日期在2006-01-01之后

-- 表子查询的另一种写法：查询入职日期在2006-01-01之后的员工及其所属部门信息
select *
from (select * from tb_emp where entrydate > '2006-01-01') e,  -- 子查询：从tb_emp表中选择入职日期在2006-01-01之后的员工，并起别名为 e
     tb_dept p                                              -- tb_dept 表（部门表），起别名为 p
where e.dept_id = p.id;                                     -- 连接条件：子查询结果的dept_id等于tb_dept表中的id
```

### 案例

对菜品(dish)， 套餐(setmeal)，类别(category)，菜品和套餐(setmeal_dish)对应关系表进行多表查询

```sql
-- 查询价格低于10元的菜品的名称和价格
SELECT d.name, d.price
FROM dish d
WHERE d.price < 10;

-- 查询价格低于10元的菜品的名称、价格以及所属分类的名称
SELECT d.name, d.price, c.name
FROM dish d,
     category c
WHERE d.category_id = c.id -- 使用category_id连接菜品和分类
  AND d.price < 10;        -- 过滤价格低于10元的菜品

-- 查询价格在10到50元之间且状态为1的菜品的名称、价格以及所属分类的名称
SELECT d.name, d.price, c.name
FROM dish d
         LEFT JOIN category c ON d.category_id = c.id -- 左连接，包含所有菜品，即使它们没有分类
WHERE d.price BETWEEN 10 AND 50 -- 过滤价格在10到50元之间的菜品
  AND d.status = 1;             -- 过滤状态为1的菜品

-- 查询所有有菜品的分类的名称，按分类名称分组
SELECT c.name
FROM dish d,
     category c
WHERE d.category_id = c.id -- 使用category_id连接菜品和分类
GROUP BY c.name;           -- 按分类名称分组，获取唯一的分类名称

-- 查询有3个或更多菜品的分类的名称及其菜品数量
SELECT c.name, COUNT(*)
FROM dish d,
     category c
WHERE d.category_id = c.id -- 使用category_id连接菜品和分类
GROUP BY c.name            -- 按分类名称分组，计算每个分类的菜品数量
HAVING COUNT(*) >= 3;      -- 仅包含菜品数量大于或等于3的分类

-- 查询每个分类的名称及其菜品的最高价格
SELECT c.name, MAX(d.price)
FROM dish d,
     category c
WHERE d.category_id = c.id -- 使用category_id连接菜品和分类
GROUP BY c.name;           -- 按分类名称分组，获取每个分类的最高价格

-- 查询名为'商务套餐A'的套餐及其菜品的名称、价格和份数
SELECT s.name, s.price, d.name, d.price, sd.copies
FROM setmeal s,
     setmeal_dish sd,
     dish d
WHERE s.id = sd.setmeal_id    -- 使用setmeal_id连接套餐和套餐菜品
  AND sd.dish_id = d.id       -- 使用dish_id连接套餐菜品和菜品
  AND s.name = '商务套餐A';   -- 过滤出名称为'商务套餐A'的套餐

-- 查询价格低于所有菜品平均价格的菜品的所有列
SELECT *
FROM dish
WHERE price < (SELECT AVG(d.price) FROM dish d); -- 子查询获取所有菜品的平均价格

```



## 事物

事物是一组操作的集合，它是一个不可分割的工作单位， 事物会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作**要么同时成功， 要么同时失败**。

```sql
start transaction;

delete from tb_dept where id = 2;

delete from tb_emp where dept_id = 2;

commit;
-- 如果在事务中的语句出现了问题， 则不能直接提交，需要回滚保证所有语句都没有问题
rollback;
```

### 四大特性

ACID

- **原子性**（Atomicity）：事务是不可分割的最小单元，要么全部成功，要么全部失败
- **一致性**（Consistency）：事物完成后， 必须使所有的数据都保持一致状态
- **隔离性**（Isolation）：数据库系统提供的隔离机制，保证事物在不受外部并发操作影响的独立环境下运行
- **持久性**（Durability）：事物一旦提交或回滚，它对数据库中的数据的改变就是永久的

## 数据库优化

**索引（index）**：是帮助数据库高效获取数据的数据结构

### 索引优缺点：

**优点**：

- 提高数据查询的效率，降低数据库的IO成本
- 通过索引列对数据进行排序，降低数据排序的成本，降低CPU消耗

**缺点**：

- 索引会占用存储空间
- 索引大大提高了查询效率，同时也降低了`insert`, `update`, `delete`的效率

### 索引的数据结构

MySQL数据库支持的索引结构有很多， 如：Hash索引， B+Tree索引， Full-Test索引等。如果没有特别指明，都是指默认的B+Tree结构组织的索引。

页是数据库存储的最小单位，一个页为16KB

- **B+Tree(多路平衡搜索树)**

每个节点可以存储多个Key， 所有的Key都会在叶子节点中出现

![image-20240607115403341](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240607115403341.png)

- 每个节点，可以存储多个key（有n个key， 就有n个指针）。
- 所有的数据都存储在叶子节点内，非叶子节点仅用于索引数据。
- 叶子节点形成了一颗**双向链表**，便于**数据的排序**及区间范围查询。

### 索引创建的SQL

```sql
-- 在tb_emp表的name列上创建索引idx_tb_emp_name
CREATE INDEX idx_tb_emp_name ON tb_emp(name);

-- 显示tb_emp表中的所有索引
SHOW INDEX FROM tb_emp;

-- 删除tb_emp表上的索引idx_tb_emp_name
DROP INDEX idx_tb_emp_name ON tb_emp;

```

