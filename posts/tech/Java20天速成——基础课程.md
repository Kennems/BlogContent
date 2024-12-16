---
title : 'Java20天速成——基础课程'
date : 2024-04-15T22:20:13+08:00
lastmod: 2024-10-12T22:20:13+08:00
description : "Java学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Java学习笔记"]
tags : ["Java"]
# password : leetcode
---


# JAVA 20天速成

有志者事竟成

没有完完全全的投入，检测，学习是学不到东西的。

自欺欺人，眼高手低是绝对没有任何作用的。

![Java快速入门， IDEA开发工具使用](https://cdn.jsdelivr.net/gh/kennems/blog-image/Java%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8%EF%BC%8C%20IDEA%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7%E4%BD%BF%E7%94%A8.png)

## JAVA背景信息

JAVA是一门高级编程语言。

属于ORACLE公司

JAVA之父——詹姆斯·高斯林

JAVA主要做互联网系统的开发

主要技术平台：**JAVASE**， **JAVAEE**， JAVAME

## JDK的发展史

Java Development Kit 开发者工具包

当前使用更多的是`JDK-8`，`JDK-11`

教学只用`JDK-17`

技术类网站 ： 安装路径下不要包含中文和空格

所有的开发工具最好安装到统一目录

> 电脑内JAVA安装路径：
>
> F:\Java\jdk17
>
> F:\Java\jdk11
>

> javac.exe : 编译工具
> java.exe : 执行工具

常用命令

```cmd
cls： 清屏
退回到盘符根目录 ： cd\
先切换盘符在切换目录
```

Java 程序要经历的步骤：

- 编写、编译（javac), 运行(java)

Java代码编写有什么基本要求？

- 文件名称和后缀必须是java结尾
- 文件名称必须与代码的类名称一致
- 必须使用英文模式下的符号

```shel
javac .\HelloWorld.java
java HelloWorld
```

```java
public class Main{
    public static void main(String[] args){
        System.out.println("Hello World!");
    }
}
```

JDK11开始支持`java`直接运行源代码文件

## JDK 组成

- JRE（Java Runtime Environment）：Java的运行环境

  - JVM（Java Vitual Machine）：Java虚拟机， 真正运行java程序的地方。

  - 核心类库：Java自己写好的程序，给程序员调用的

- 开发工具：java, javac
- JDK（Java Development Kit）：Java开发工具包（包括上面所有）。

**Java的跨平台、工作原理**

- 一次编译，处处可用

Path环境变量：用于记住程序路径，方便在命令行窗口的**任意目录**启动程序

IDEA管理JAVA程序的结构

- project（项目，工程）
- module（模块）
- package（包）
- class（类）

**IDEA快捷键**

- > main/psvm sout
  
- > ctrl + D 复制当前行到下一行
  
- > ctrl + Y 删除所在行，建议用ctrl + x
  
- > ctrl + alt + L 格式化代码
  
- > ALT + SHIFT + ⬆ | ⬇
  > 上下移动当前代码
  
- > ctrl + /, ctrl + shift + /
  > 对代码进行注释

- > shift + enter , ctrl + alt + enter
  > 在当前行下面/上面添加一行

IDEA常用操作：

- 删除类文件
- 修改类名称
- 修改模块
- **导入模块**

**方法1：**

![image-20240408144814410](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240408144814410.png)

![image-20240408144908375](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240408144908375.png)

注：生成`imi`文件

```shell
mvn idea:module
```

**方法2：**

复制代码到project目录， 之后再导入， 否则只是相关导入，修改module内容会修改源代码

- 删除模块
- 打开工程
- 关闭工程

## JAVA基础语法

![Java基础语法](https://cdn.jsdelivr.net/gh/kennems/blog-image/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95.png)

### 1、注释

```java
// 单行注释

/*
多行注释
*/

/** 文档注释
 * 目标：
 */
```

### 2、变量

变量就是内存中的一块区域。

```java
        // 二进制表示，前缀 0b 表示二进制
        int a1 = 0b01100001;
        System.out.println(a1); // 输出：97

        // 十进制表示
        int a2 = 97;
        System.out.println(a2); // 输出：97

        // 八进制表示，前缀 0 表示八进制
        int a3 = 0141;
        System.out.println(a3); // 输出：97

        // 十六进制表示，前缀 0x 表示十六进制
        int a4 = 0xFA;
        System.out.println(a4); // 输出：250
```

```java
        // 声明并初始化不同类型的变量
        byte a = 10;           // byte类型，表示范围为 -128 到 127
        short b = 8888;        // short类型，表示范围为 -32768 到 32767
        int c = 130;           // int类型，表示范围为 -2147483648 到 2147483647
        long d = 9393939393993L;  // long类型，需要在结尾加上L或l，表示范围为 -9223372036854775808 到 9223372036854775807

        // 输出不同类型的整数变量
        System.out.println(a); // 输出：10
        System.out.println(b); // 输出：130
        System.out.println(c); // 输出：9393939393993
        System.out.println(d); // 输出：8888

        // 浮点数类型
        float e = 2.17F;       // float类型，需要在结尾加上F或f
        double f = 3.141592657; // double类型，默认为double，可以不加任何标识

        // 输出浮点数变量
        System.out.println(e); // 输出：2.17
        System.out.println(f); // 输出：3.141592657

        // 字符类型
        char ch = 'c';         // char类型，表示单个字符
        System.out.println(ch); // 输出：c

        // 布尔类型
        boolean flag = false;  // boolean类型，表示true或false
        System.out.println(flag); // 输出：false

        // 字符串类型
        String st = "string";  // String类型，表示字符串
        System.out.println(st); // 输出：string
```

#### 类型转换

```java
        byte a = 10;    // 声明并初始化byte类型变量a
        int b = a;      // 将byte类型的变量a赋值给int类型的变量b，发生拓宽转换
        System.out.println(b); // 输出：10

        int c = 100;    // 声明并初始化int类型变量c
        double d = c;   // 将int类型的变量c赋值给double类型的变量d，发生拓宽转换
        System.out.println(d); // 输出：100.0

        char e = 'b';   // 声明并初始化char类型变量e
        int f = e;      // 将char类型的变量e赋值给int类型的变量f，发生拓宽转换
        System.out.println(f); // 输出：98（字符'b'对应的ASCII码值）
```

```java
        byte a = 10;   // 声明并初始化byte类型变量a
        short b = 20;  // 声明并初始化short类型变量b
        int c = 30;    // 声明并初始化int类型变量c
        long d = 40;   // 声明并初始化long类型变量d

        // 对a、b、c、d进行加法运算，结果存储在long类型变量res1中
        long res1 = a + b + c + d;
        System.out.println(res1); // 输出：100

        // 对a、b进行加法运算，并将1.0（double类型）相加，结果存储在double类型变量res2中
        double res2 = a + b + 1.0;
        System.out.println(res2); // 输出：31.0

        // 对a、b进行加法运算，结果存储在int类型变量res3中
        int res3 = a + b;
        System.out.println(res3); // 输出：30

        byte a1 = 10;  // 声明并初始化byte类型变量a1
        byte a2 = 20;  // 声明并初始化byte类型变量a2
        // 对a1、a2进行加法运算，由于byte类型相加可能溢出，因此结果存储在int类型变量res4中
        int res4 = a1 + a2; // 进行运算时a1和a2自动转换成int类型，然后相加
        System.out.println(res4); // 输出：30
```

#### 强制类型转换

```java
        int a = 1111111;
        char b = (char) a; // 快捷键， ALT+ENTER
        System.out.println(b);

        double c = 99.8;
        int d = (int) c; // 丢掉小数部分
        System.out.println(d);
```

### 3、运算符

##### 使用`+`符号做连接符的情况

```java
        int a = 10; // 声明并初始化整数变量a

        // 使用字符串连接符将字符串 "abc" 和整数变量a 连接起来并打印
        System.out.println("abc" + a); // 输出："abc10"

        // 打印整数变量a 加 5 的结果
        System.out.println(a + 5); // 输出：15

        // 将字符串 "abc"、整数变量a 和字符 'a' 连接起来并打印
        System.out.println("abc" + a + 'a'); // 输出："abc10a"

        // 将字符 'a' 的ASCII码值（97）和整数变量a 相加，再将结果与字符串 "abc" 连接起来并打印
        // 注：字符 'a' 的ASCII码值被当作整数参与运算，然后再将结果转换为字符
        System.out.println('a' + a + "abc"); // 输出："107abc"
```

```java
        // 初始化变量n和m分别为3和5
        int n = 3, m = 5;

        // 计算res的值
        // ++m: 先自增m，m变成6，然后返回6
        // --m: 先自减m，m变成5，然后返回5
        // m--: 先返回m的值5，然后再自减m，m变成4
        // ++n: 先自增n，n变成4，然后返回4
        // n--: 先返回n的值4，然后再自减n，n变成3
        // 计算：6 - 5 + 5 - 4 + 4 + 3 = 9
        int res = ++m - --m + m-- - ++n + n-- + 3;
        System.out.println(res); // 输出res的值

        System.out.println(n); // 输出n的值，此时n为3
        System.out.println(m); // 输出m的值，此时m为4

        // 初始化变量c和d分别为10和5
        int c = 10, d = 5;

        // 计算res2的值
        // c++: 先返回c的值10，然后再自增c，c变成11
        // ++c: 先自增c，c变成12，然后返回12
        // --d: 先自减d，d变成4，然后返回4
        // ++d: 先自增d，d变成5，然后返回5
        // 计算：10 + 12 - 4 - 5 + 1 + c--，其中c此时为12，然后再自减c，c变成11
        // 计算：10 + 12 - 4 - 5 + 1 + 12 = 26
        int res2 = c++ + ++c - --d - ++d + 1 + c--;
        System.out.println(res2); // 输出res2的值

        System.out.println(c); // 输出c的值，此时c为11
        System.out.println(d); // 输出d的值，此时d为5

```

##### 逻辑运算

```java
        // 定义变量
        double size = 9.8; // size的值为9.8
        double storage = 16; // storage的值为16

        // 第一组条件判断
        System.out.println("original " + storage);
        // 使用按位与运算符"&"，即使第一个条件不满足，storage也会自增
        boolean res11 = size < 8 & storage++ >= 16; // false
        System.out.println(res11); // 输出false
        System.out.println(storage); // 输出17.0，因为storage在条件判断之后自增了
        // 使用逻辑与运算符"&&"，若第一个条件不满足，则不会执行第二个条件，storage不会自增
        boolean res12 = size < 8 && storage++ >= 16; // false
        System.out.println(res12); // 输出false
        System.out.println(storage); // 输出17.0，因为storage没有自增

        // 第二组条件判断
        System.out.println("-------------------");
        System.out.println("original " + storage);
        // 使用按位或运算符"|"，即使第一个条件满足，storage也会自增
        boolean res21 = size >= 8 | storage++ >= 8; // true
        System.out.println(res21); // 输出true
        System.out.println(storage); // 输出18.0，因为storage在条件判断之后自增了
        // 使用逻辑或运算符"||"，若第一个条件满足，则不会执行第二个条件，storage不会自增
        boolean res22 = size >= 8 || storage++ >= 16; // true
        System.out.println(res22); // 输出true
        System.out.println(storage); // 输出18.0，因为storage没有自增

        System.out.println("-------------------");

        // 其他操作
        // 使用逻辑非运算符"!"，对true取反，结果为false
        System.out.println((!true)); // 输出false
        // 使用按位异或运算符"^"，对两个数的二进制进行异或操作，结果为0
        System.out.println((1^1)); // 输出0
```

```java
        int grade = 10; // 定义成绩变量为10
        // 使用三元运算符判断成绩是否合格
        String res = grade >= 60 ? "成绩合格" : "成绩不合格";
        System.out.println(res); // 输出 "成绩不合格"，因为grade的值为10，小于60

        // 第一条逻辑表达式
        System.out.println(10 > 3 || 10 > 3 && 10 < 3); // 输出true
        // 逻辑运算符"&&"的优先级高于"||"，所以先执行10 > 3 && 10 < 3，结果为false，然后执行10 > 3 || false，结果为true。

        // 第二条逻辑表达式
        System.out.println((10 > 3 || 10 > 3) && 10 < 3); // 输出false
        // 加了括号后，先执行括号内的10 > 3 || 10 > 3，结果为true，然后与10 < 3进行逻辑与运算，结果为false。
```

### 输入

```java
        // 1. 创建Scanner对象sc，用于从System.in（即标准输入流，通常为控制台）读取用户输入
        Scanner sc = new Scanner(System.in);
        // 2. 输出提示信息，要求用户输入年龄
        System.out.println("Please input your age:");
        // 3. 使用Scanner对象sc读取用户输入的整数型年龄，并将其存储在变量age中
        int age = sc.nextInt();
        // 4. 根据用户输入的年龄计算其出生年份（假设当前为2024年），然后输出结果
        System.out.println("Your birth year is :" + (2024 - age));
        // 5. 输出提示信息，要求用户输入姓名
        System.out.println("Please input your name:");
        // 6. 使用Scanner对象sc读取用户输入的字符串型姓名，并将其存储在变量name中
        String name = sc.next();
        // 7. 重复输出用户出生年份（与步骤4相同，此处可能是代码冗余或笔误）
        System.out.println("Your birth year is :" + (2024 - age));
        // 8. 输出欢迎信息，包含用户输入的姓名
        System.out.println("Welcome: " + name);
```

`switch`注意事项：

- 表达式类型只能是`byte`, `short`, `int`, `char`, JDK5开始支持`枚举`， JDK7开始支持`String`，不支持`double`，`float`, `long`。
- case给出的值不允许重复，且只能是**字面量**，不能是**变量**
- 正常使用时需要写`break`， 否则会出现**穿透现象**

**`switch`** 技巧：运用穿透性合并同样结果的分支。

```java
public class SwitchExample {
    public static void main(String[] args) {
        int day = 2;
        String dayType;

        switch (day) {
            case 1:
            case 2:
            case 3:
            case 4:
            case 5:
                dayType = "工作日";
                break;
            case 6:
            case 7:
                dayType = "周末";
                break;
            default:
                dayType = "无效的日子";
                break;
        }

        System.out.println("今天是: " + dayType);
    }
}

```

**`while`**

```java
 // 创建一个Random对象用于生成随机数
        Random random = new Random();
        // 生成1到100之间的随机数作为幸运数字
        int luckNumber = random.nextInt(100) + 1;
        // 创建一个Scanner对象用于接收用户的输入
        Scanner sc = new Scanner(System.in);
        // 提示用户输入猜测的数字
        System.out.println("请输入你猜的数字");

        // 循环，直到用户猜中为止
        while (true) {
            // 获取用户输入的猜测数字
            int guessNumber = sc.nextInt();
            // 判断用户输入的数字与幸运数字的关系，并给出相应提示
            if (guessNumber == luckNumber) {
                // 如果猜中了，输出恭喜信息，并结束循环
                System.out.println("恭喜你，你中了100元大奖");
                break;
            } else if (guessNumber > luckNumber) {
                // 如果猜的数字太大，提示用户数字太大
                System.out.println("你猜的数字太大了");
            } else {
                // 如果猜的数字太小，提示用户数字太小
                System.out.println("你猜的数字太小了");
            }
        }
```

### 数组

```java
        //1.标准写法
        // 初始化整型数组ages，包含5个元素，分别为10, 20, 30, 40, 50
        int[] ages = new int[]{
                10, 20, 30, 40, 50
        };
        // 初始化双精度浮点型数组scores，包含5个元素，分别为99.9, 88.8, 77.7, 66.6, 55.5
        double[] scores = new double[]{
                99.9, 88.8, 77.7, 66.6, 55.5
        };
        // 输出整型数组ages的引用地址
        System.out.println(ages);
        // 输出双精度浮点型数组scores的引用地址

        //2.简化写法
        // 使用简化语法初始化整型数组ages2，包含5个元素，分别为10, 20, 30, 40, 50
        int[] ages2 = {
                10, 20, 30, 40, 50
        };
        // 使用简化语法初始化双精度浮点型数组scores2，包含5个元素，分别为99.9, 88.8, 77.7, 66.6, 55.5

        //3.写法三
        int age3[] = new int[10];
```

### Java 内存分配

- **方法区**
- **栈**
- **堆** 

- 本地方法栈
- 程序计数器

```java
        int a = 20;
        int[] arr = new int[3];
```

以上两行代码的执行原理：

- a是变量，**直接放在栈中**，a变量中存储的数据就是20这个值
- `new int[3]`是创建一个数组对象，会在**堆内存**中开辟区存储3个整数
- arr是变量，在**栈**中，arr中存储的是数组对象在**堆内存中地址值**

### Java小案例——双色球

```java
package com.show.test;

import java.util.Random;
import java.util.Scanner;

public class Test4 {
    public static void main(String[] args) {
        System.out.println(1);
        int[] userNumber = userChooseNumber(); // 用户选择号码
        int[] luckNumer = createLuckNumber(); // 创建幸运号码
        check(userNumber, luckNumer); // 检查用户选择的号码与幸运号码的匹配情况
    }

    // 用户选择号码的方法
    public static int[] userChooseNumber() {
        System.out.println("Welcome to this select ball system!"); // 欢迎语
        int[] numbers = new int[6]; // 存储用户选择的号码数组
        Scanner sc = new Scanner(System.in);
        for (int i = 0; i < 5; i++) {
            System.out.println("Please input your No." + (i + 1) + " ball"); // 提示用户输入号码
            while (true) {
                int n = sc.nextInt(); // 接收用户输入的号码
                if (n < 1 || n > 33) { // 判断号码是否在有效范围内
                    System.out.println("Your number not in the available zone: [1,33], Please input again");
                } else {
                    if (exist(numbers, n)) { // 判断号码是否已经被选择
                        System.out.println("Current number has been chosen, Please input again");
                    } else {
                        numbers[i] = n; // 将号码存入数组
                        break;
                    }
                }
            }
            System.out.println("Your input number is :" + numbers[i]); // 输出用户输入的号码
        }
        System.out.println("Input your last number");
        while (true) {
            int n = sc.nextInt();
            if (n < 1 || n > 16) {
                System.out.println("Your number is not available, Please input the number between 1 and 16");
            } else {
                numbers[5] = n; // 存储最后一个号码
                break;
            }
        }
        System.out.println("Blow is your input numbers");
        printArray(numbers); // 打印用户选择的号码
        return numbers;
    }

    // 判断数字是否已存在于数组中的方法
    public static boolean exist(int[] arr, int x) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == x) {
                return true;
            }
        }
        return false;
    }

    // 打印数组的方法
    public static void printArray(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i] + " ");
        }
        System.out.println();
    }

    // 创建幸运号码的方法
    public static int[] createLuckNumber() {
        int[] numbers = new int[6]; // 存储幸运号码数组
        Random r = new Random();
        for (int i = 0; i < 5; i++) {
            while (true) {
                int n = r.nextInt(33) + 1; // 生成随机号码
                if (exist(numbers, n)) {
                    continue;
                } else {
                    numbers[i] = n; // 将号码存入数组
                    break;
                }
            }
        }
        int n = r.nextInt(16) + 1;
        numbers[5] = n; // 存储最后一个号码（蓝球）
        System.out.println("Blow is the LuckNumber!"); // 输出幸运号码
        printArray(numbers); // 打印幸运号码
        return numbers;
    }

    // 检查用户选择的号码与幸运号码的匹配情况
    public static void check(int[] arr1, int[] arr2) {
        int redCount = 0, blueCount = 0; // 红球匹配数、蓝球匹配数初始化为0
        for (int i = 0; i < arr1.length - 1; i++) {
            if (arr1[i] == arr2[i]) { // 判断红球是否匹配
                redCount += 1;
            }
        }
        if (arr1[5] == arr2[5]) { // 判断蓝球是否匹配
            blueCount += 1;
        }
        // 输出命中的红球数量和蓝球数量
        System.out.println("您命中的红球数量是：" + redCount);
        System.out.println("您命中的蓝球数量是：" + blueCount);
        // 根据命中情况输出不同的中奖信息
        if (redCount == 6 && blueCount == 1) {
            System.out.println("恭喜您，中奖1000万，可以开始享受人生了~~~");
        } else if (redCount == 6 && blueCount == 0) {
            System.out.println("恭喜您，中奖500万，可以稍微开始享受人生了~~~");
        } else if (redCount == 5 && blueCount == 1) {
            System.out.println("恭喜您，中奖3000元，可以出去吃顿小龙虾了~");
        } else if (redCount == 5 && blueCount == 0 || redCount == 4 && blueCount == 1) {
            System.out.println("恭喜您，中了小奖：200元~");
        } else if (redCount == 4 && blueCount == 0 || redCount == 3 && blueCount == 1) {
            System.out.println("中了10元~");
        } else if (redCount < 3 && blueCount == 1) {
            System.out.println("中了5元~");
        } else {
            System.out.println("感谢您对福利事业做出的巨大贡献~~~");
        }
    }
}
```

## 面向对象

对象就是一张表。

### this的执行原理

`this`：就是一个变量，可以用在方法中，`来拿到当前对象`。

`this`主要用来

```java
public class PrintThis {
    public void printThis(){
        System.out.println(this);
    }
}
public class Test1 {
    public static void main(String[] args) {
        PrintThis t = new PrintThis();  
        t.printThis();//com.show.thisDemo.PrintThis@3b07d329
        System.out.println(t);          //com.show.thisDemo.PrintThis@3b07d329
    }
}
```

```java
public class Student1 {
    int score;
    public void check(int score){
        if(this.score>score){
            System.out.println("Congratulations!");
        }else{
            System.out.println("What's the pity!");
        }
    }
}
    public static void main(String[] args) {
        Student1 s1 = new Student1();
        s1.score = 10;
        s1.check(100);
    }
```

### 构造器需要注意的问题

- 不写无参数构造器时，编译器会自动生成一个无参数构造器
- 而写了有参数构造器之后，就不会自动生成无参数构造器了， 需要手动写上。

### 封装

- 就是用类设计对象处理某一个事物的数据时，应该把要处理的数据，以及处理这些数据的方法，设计到一个对象中去

**合理隐藏，合理暴露**（get, set方法）

### 实例JavaBean

实体类：一种特殊的类

#### Movie

```java
package com.show.ObjectDemo;

public class Movie {
    // 私有属性
    private int id; // 电影ID
    private String name; // 电影名称
    private double price; // 电影票价
    private double score; // 电影评分
    private String director; // 导演
    private String actor; // 主演
    private String info; // 电影信息

    // 无参构造方法
    public Movie() {
    }

    // 带参构造方法
    public Movie(int id, String name, double price, double score, String director, String actor, String info) {
        this.id = id;
        this.name = name;
        this.price = price;
        this.score = score;
        this.director = director;
        this.actor = actor;
        this.info = info;
    }

    // Getter和Setter方法
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    public double getScore() {
        return score;
    }

    public void setScore(double score) {
        this.score = score;
    }

    public String getDirector() {
        return director;
    }

    public void setDirector(String director) {
        this.director = director;
    }

    public String getActor() {
        return actor;
    }

    public void setActor(String actor) {
        this.actor = actor;
    }

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }
}
```

#### MovieOperator

```java
package com.show.ObjectDemo;

public class MovieOperator {
    // 电影数组
    private Movie[] movies;

    // 构造方法
    public MovieOperator(Movie[] movies) {
        this.movies = movies;
    }

    // 打印所有电影信息
    public void printAllMovies() {
        System.out.println("系统全部电影信息如下————————————————————————————");
        for (int i = 0; i < movies.length; i++) {
            Movie m = movies[i];
            System.out.println("编号：—————————————" + m.getId());
            System.out.println("电影名：—————————————" + m.getName());
            System.out.println("价格：—————————————" + m.getPrice());
            System.out.println("-----------------------");
        }
    }

    // 根据ID查询电影信息
    public void searchMovieById(int id) {
        for (int i = 0; i < movies.length; i++) {
            Movie m = movies[i];
            if (id == m.getId()) {
                System.out.println("存在该电影！");
                System.out.println("编号：—————————————" + m.getId());
                System.out.println("电影名：—————————————" + m.getName());
                System.out.println("价格：—————————————" + m.getPrice());
                System.out.println("导演：—————————————" + m.getDirector());
                System.out.println("演员：—————————————" + m.getActor());
                System.out.println("评分：—————————————" + m.getScore());
                System.out.println("其他信息：—————————————" + m.getInfo());
                return;
            }
        }
        System.out.println("不存在该电影");
    }
}
```



#### Test

```java
package com.show.ObjectDemo;

import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        // 创建Movie对象数组，存储电影信息
        Movie[] movies = new Movie[4];
        movies[0] = new Movie(1, "水门桥", 38.9, 9.8, "徐克", "吴京", "12万人想看");
        movies[1] = new Movie(2, "出拳吧", 39, 7.8, "唐晓白", "田雨", "3.5万人想看");
        movies[2] = new Movie(3, "月球陨落", 42, 7.9, "罗兰", "贝瑞", "17.9万人想看");
        movies[3] = new Movie(4, "一点就到家", 35, 8.7, "许宏宇", "刘昊然", "10.8万人想看");

        // 创建MovieOperator对象，用于操作Movie数组
        MovieOperator movieOperator = new MovieOperator(movies);

        System.out.println("在这里，你可以查询关于最新的电影信息！");
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("输入1查看所有电影信息");
            System.out.println("输入2可以查询某个特定电影的详细信息");

            int cmd = sc.nextInt();
            switch (cmd) {
                case 1:
                    movieOperator.printAllMovies(); // 打印所有电影信息
                    break;
                case 2:
                    System.out.println("请输入你需要查询的电影ID");
                    int id = sc.nextInt();
                    movieOperator.searchMovieById(id); // 根据ID查询电影信息
                    break;
                default:
                    System.out.println("非法命令，请重新输入");
            }
        }
    }
}
```

### 成员变量和局部变量

- **成员变量**：类中， 方法外， **有默认值**， 存于**堆内存**中， 属于整个对象， 与对象同生共死

- **局部变量**：常见于方法中，**无默认值**，需要初始化， 存于**栈内存**中， 属于所属大括号中， 方法调用而生，结束而亡

## API

### 注意事项

1. 同一个包下的程序，可以直接访问
2. 访问其他包下的程序，必须导包才可以访问
3. 自己的程序中调用Java提供的程序，也需要先导包才可以使用
   - 注意`Java.lang`包下的程序是不需要导包， 可以直接使用
4. 访问多个其他包下的程序，在这些程序名一样的情况下，默认只能只能导入一个程序，另外一个程序必须带包名和类名来访问。

```java
        Demo d = new Demo();
        d.print();
        Demo1 d1 = new Demo1();
        d1.print();
        com.show.pkgDemo3.Demo1 d2 = new com.show.pkgDemo3.Demo1();
        d2.print();
```

### String

```java
        String name = "Java";
        System.out.println(name);
        String rs1 = new String();
        System.out.println(rs1);
        String rs2 = new String("NewStringJava");
        System.out.println(rs2);
```

1. String 是不可变的
2. 只要是`"..."`的方式写出的字符串对象，会存储到**字符串常量池**中，且相同内容的字符只会存储一份
3. 而`new String`创建字符串对象时，每次都会`new`出来一个新对象，放在堆内存中。

```java
        String s1 = "abc";
        String s2 = "abc";
        System.out.println(s1==s2); //true

        char[] ch = {'1', '2', '3'};
        String s3 = new String(ch);
        String s4 = new String(ch);
        System.out.println(s3==s4); //false
```

```java
        String s5 = new String("abc");
        // 实际创建了两个对象，一个在字符串常量池当中
        // 另一个在堆内存中
        String s6 = "abc";
        // 实际创建了0个对象，因为"abc"已经在常量池当中了
        System.out.println(s5==s6); //false
```

```java
        String s7 = "abc";
        String s8 = "a"+'b'+"c"; //编译器会自动处理
        System.out.println(s7==s8); //true

        String s9 = "abc";
        String s10 = "ab";
        String s11 = s10 + "c"; //而对于变量则不会自动处理
        System.out.println(s9==s11); //false
```

### ArrayList

```java
        ArrayList<String> list = new ArrayList<>();
        list.add("heima");
        list.add("Java");
        list.add("爪洼");
        System.out.println(list);//[heima, Java, 爪洼]

        list.add(1, "JAVAJAVA");
        System.out.println(list); //[heima, JAVAJAVA, Java, 爪洼]

        System.out.println(list.get(1));//JAVAJAVA
        System.out.println(list.size());//4
        System.out.println(list.remove(1));//JAVAJAVA
        System.out.println(list.remove("Java")); //true 默认删除第一个出现的元素
        System.out.println(list);//[heima, 爪洼]

        System.out.println(list.set(1, "ZhuaWa"));//爪洼
        System.out.println(list);//[heima, ZhuaWa]
```

## 综合实践项目——ATM

### Account

```java
package com.showguan;

public class Account {
    private int userId;
    private String userName;
    private String cardId;
    private char sex;
    private String possword;
    private double money;
    private double limit;

    public int getUserId() {
        return userId;
    }

    public void setUserId(int userId) {
        this.userId = userId;
    }

    public String getUserName() {
        return userName + (this.sex == '男' ? "先生" : "女士");
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public char getSex() {
        return sex;
    }

    public void setSex(char sex) {
        this.sex = sex;
    }

    public String getPossword() {
        return possword;
    }

    public void setPossword(String possword) {
        this.possword = possword;
    }

    public double getMoney() {
        return money;
    }

    public void setMoney(double money) {
        this.money = money;
    }

    public double getLimit() {
        return limit;
    }

    public void setLimit(double limit) {
        this.limit = limit;
    }

    public String getCardId() {
        return cardId;
    }

    public void setCardId(String cardId) {
        this.cardId = cardId;
    }
}
```

### ATM

```java
package com.showguan;

import java.util.ArrayList;
import java.util.Random;
import java.util.Scanner;

public class ATM {
    private ArrayList<Account> accounts = new ArrayList<>();

    private Account loginAcc;
    private Scanner sc = new Scanner(System.in);

    public void start() {
        /**
         * 启动ATM系统，展示欢迎界面
         */
        while (true) {
            System.out.println("欢迎您进入ATM系统！");
            System.out.println("1. 用户登录");
            System.out.println("2. 用户开户");
            System.out.println("请输入你需要进行的操作：");
            String command = sc.next();
            switch (command) {
                case "1":
                    login();
                    break;
                case "2":
                    createAccount();
                    break;
                default:
                    System.out.println("你输入的操作有误，请重新书输入！");
                    System.out.println();
                    break;
            }
        }

    }

    private void createAccount() {
        System.out.println("===系统开户操作===");
        Account acc = new Account();
        System.out.println("请输入用户名");
        String name = sc.next();
        acc.setUserName(name);

        while (true) {
            System.out.println("请输入性别：");
            char sex = sc.next().charAt(0);
            if (sex == '男' || sex == '女') {
                acc.setSex(sex);
                break;
            } else {
                System.out.println("你输入的性别有误！请重新输入");
            }
        }

        while (true) {
            System.out.println("请输入您的账户密码：");
            String password = sc.next();
            System.out.println("请输入您的确认密码：");
            String confirmPassword = sc.next();
            if (password.equals(confirmPassword)) {
                acc.setPossword(password);
                System.out.println("密码设置成功！");
                break;
            } else {
                System.out.println("两次密码不一致，请重新设置密码！");
            }
        }

        System.out.println("请输入您的取现最大额度");
        double limit = sc.nextDouble();
        acc.setLimit(limit);

        String cardId = createCardId();
        acc.setCardId(cardId);

        accounts.add(acc);
        System.out.println("恭喜你！" + acc.getUserName() + "开户完成, 您的卡号是:" + acc.getCardId());
    }

    private String createCardId() {
        while (true) {
            String cardId = "";
            Random r = new Random();
            for (int i = 0; i < 8; i++) {
                int data = r.nextInt(10);
                cardId += data;
            }
            Account acc = getAccountByCardID(cardId);
            if (acc == null) {
                return cardId;
            }
        }
    }

    private Account getAccountByCardID(String cardId) {
        for (int i = 0; i < accounts.size(); i++) {
            Account acc = accounts.get(i);
            if (acc.getCardId().equals(cardId)) {
                return acc;
            }
        }
        return null;
    }

    private void login() {
        System.out.println("===系统登录===");
        if (accounts.size() == 0) {
            System.out.println("当前系统内还没有任何账户，请先创建账户再来登录！");
            return;
        }
        System.out.println("请输入您的卡号");
        String cardId = sc.next();
        Account acc = getAccountByCardID(cardId);
        if (acc == null) {
            System.out.println("用户不存在");
        } else {
            while (true) {
                System.out.println("请输入用户密码");
                String passWord = sc.next();
                if (passWord.equals(acc.getPossword())) {
                    System.out.println("登陆成功！");
                    loginAcc = acc;
                    showUerCommand();
                    break;
                } else {
                    System.out.println("密码错误！请重新输入");
                }
            }
        }
    }

    private void showUerCommand() {
        while (true) {
            System.out.println(loginAcc.getUserName() + "您可以进行如下操作");
            System.out.println("1. 查询账户");
            System.out.println("2. 存款");
            System.out.println("3. 取款");
            System.out.println("4. 转账");
            System.out.println("5. 修改密码");
            System.out.println("6. 退出");
            System.out.println("7. 注销当前账户");
            System.out.println("请选择您要进行的操作：");
            String command = sc.next();
            switch (command) {
                case "1":
                    showloginAccount();
                    break;
                case "2":
                    depositMoney();
                    break;
                case "3":
                    withDrawMoney();
                    break;
                case "4":
                    transferMoney();
                    break;
                case "5":
                    updatePassword();
                    return;
                case "6":
                    System.out.println("用户" + loginAcc.getUserName() + "成功退出系统！");
                    return;
                case "7":
                    if (deleteAccount()) {
                        return;
                    }
                    break;
                default:
                    break;

            }
        }


    }

    private void updatePassword() {
        System.out.println("===账号密码修改操作===");
        while (true) {
            System.out.println("请输入当前账户密码");
            String password = sc.next();

            if (loginAcc.getPossword().equals(password)) {
                while (true) {
                    System.out.println("请输入你要修改为的新密码：");
                    String newPassword = sc.next();
                    System.out.println("请重复你需要修改为的密码：");
                    String confirmNewPassword = sc.next();
                    if (newPassword.equals(confirmNewPassword)) {
                        System.out.println("密码修改成功");
                        loginAcc.setPossword(newPassword);
                        return;
                    }
                    System.out.println("两次密码不一致， 请重新输入");
                }
            } else {
                System.out.println("您当前输入的密码错误！");
            }
        }
    }

    private boolean deleteAccount() {
        System.out.println("===销户操作===");
        System.out.println("确定要销户吗？y/n");
        String command = sc.next();
        switch (command) {
            case "y":
                if (loginAcc.getMoney() == 0) {
                    accounts.remove(loginAcc);
                    System.out.println("销户成功");
                    return true;
                } else {
                    System.out.println("账户下余额不为0， 不允许销户");
                }
                break;
        }
        return false;
    }

    private void transferMoney() {
        System.out.println("===用户转账===");
        if (accounts.size() < 2) {
            System.out.println("系统内只有您一个账户，无法转账");
            return;
        }
        if (loginAcc.getMoney() < 0) {
            System.out.println("您自己都没钱了，就别转了吧！");
            return;
        }

        while (true) {
            System.out.println("请您输入对方的卡号:");
            String receiveCardID = sc.next();
            Account acc = getAccountByCardID(receiveCardID);
            if (acc == null) {
                System.out.println("您输入的卡号不存在！");
            } else {
                String receiveName = "*" + acc.getUserName().substring(1);
                System.out.println("请您输入【" + receiveName + "】姓氏");
                System.out.println(receiveName);
                String firstName = sc.next();
                if (acc.getUserName().startsWith(firstName)) {
                    System.out.println("姓氏验证成功！");
                    while (true) {
                        System.out.println();
                        System.out.println("请您输入转让给对方的金额:");
                        double transfermoney = sc.nextDouble();
                        if (loginAcc.getMoney() >= transfermoney) {
                            System.out.println("转账成功！");
                            loginAcc.setMoney(loginAcc.getMoney() - transfermoney);
                            acc.setMoney(acc.getMoney() + transfermoney);
                            return;
                        } else {
                            System.out.println("余额不足，无法转账！！最多可转" + loginAcc.getMoney());
                        }
                    }
                } else {
                    System.out.println("对不起， 您认证的姓氏有问题！");
                }
            }
        }
    }

    private void withDrawMoney() {
        System.out.println("===取钱操作===");
        if (loginAcc.getMoney() < 100) {
            System.out.println("您的账户余额不足100元，不允许取钱");
            return;
        }
        while (true) {
            System.out.println("请您输入取款金额：");
            double drawMoney = sc.nextDouble();
            if (loginAcc.getMoney() >= drawMoney) {
                if (drawMoney > loginAcc.getLimit()) {
                    System.out.println("取款金额大于限定额度， 您当前的限定额度为:" + loginAcc.getLimit());
                    break;
                } else {
                    loginAcc.setMoney(loginAcc.getMoney() - drawMoney);
                    System.out.println("取款成功， 取款金额为" + drawMoney + "账户内剩余余额:" + loginAcc.getMoney());
                    return;
                }
            } else {
                System.out.println("余额不足， 您当前的账户余额是：" + loginAcc.getMoney());
            }
        }
    }

    private void showloginAccount() {
        System.out.println("===当前您的账户信息如下===");
        System.out.println("用户名: " + loginAcc.getUserName());
        System.out.println("性别: " + loginAcc.getSex());
        System.out.println("卡号: " + loginAcc.getCardId());
        System.out.println("余额: " + loginAcc.getMoney());
        System.out.println("取现最大额度: " + loginAcc.getLimit());
    }

    private void depositMoney() {
        System.out.println("==存款操作==");
        System.out.println("请输入你要存款的金额");
        double money = sc.nextDouble();
        loginAcc.setMoney(loginAcc.getMoney() + money);
        System.out.println("恭喜您" + "存款：" + money + "存款后余额:" + loginAcc.getMoney());
    }
}
```

### Test

```java
package com.showguan;

public class Test {
    public static void main(String[] args) {
        ATM atm = new ATM();
        atm.start();
    }
}
```

