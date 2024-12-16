---
title : 'C 语言指针'
date : 2024-09-03T22:30:13+08:00
lastmod: 2024-09-03T22:20:13+08:00
description : "C 语言指针" 
draft : false    
categories : ["操作系统"]
tags : ["C"]
---

# C 语言指针

## C语言中的内存

**静态内存（Static Memory)**

- Global variables, accessible throughout the whole program.
- Defined with static keywork, as well as variables defined in global scope.

**栈内存（Stack Memory)**

- Local variables with functions. Destroyed after function exits.

**堆内存（Heap Memory）**

- You control creation and destruction of these variables: *malloc()*, *free()*
- Can lead to memory leaks, use-after-free issues.

指针 （变量）= 内存地址

定义指针的 `*` ： 标记

定义格式 ：

```c
	数据类型 * 变量名
    int a = 10;
    int *p1 = &a;

    double b = 10;
    double *p2 = &b;
```

## 指针的作用

指针是一个 64 位整数，其值为内存中的地址。

### 查询数据

`*` ： 解引用运算符

```c
    int a = 10;
    int *p1 = &a;
    printf("%d\n", *p1); // 10
```

- 指针变量的数据类型要和指向变量的类型保持一致

- 指针变量占用的大小，跟数据类型无关，跟编译器有关（32位 ： 4字节；64位：8字节）

- 给指针变量赋值的时候，不能把一个数赋值给指针变量

```c
    int a = 10;
    int *p1 = &a;
	// int *p1 = 100; 错误，将100看成内存地址
    printf("%d\n", *p1); // 10
    *p1 = 20;
    printf("%d\n", *p1); // 20
```

### 操作其他函数中的变量

```c
#include <stdlib.h>
#include <stdio.h>

void swap(int *p1, int *p2)
{
    int tmp = *p1;
    *p1 = *p2;
    *p2 = tmp;
}

int main(int argc, char const *argv[])
{
    int a = 10, b = 20;
    printf("%d, %d\n", a, b);
    swap(&a, &b);
    printf("%d, %d\n", a, b);
    return 0;
}
```

### 函数返回多个值

```c
#include <stdio.h>

void getMaxAndMin(int arr[], int len, int *max, int *min)
{
    *max = arr[0];
    *min = arr[0];
    for (int i = 1; i < len; i++)
    {
        if (arr[i] > *max)
        {
            *max = arr[i];
        }
    }
    for (int i = 1; i < len; i++)
    {
        if (arr[i] < *min)
        {
            *min = arr[i];
        }
    }
}

int main(int argc, char const *argv[])
{
    int arr[] = {1, 2, 3, 100, 4, 5, 6, 7, 8};
    int len = sizeof(arr) / sizeof(arr[0]);
    int max = arr[0];
    int min = arr[0];
    getMaxAndMin(arr, len, &max, &min);
    printf("Max: %d\n", max);
    printf("Min: %d\n", min);
    return 0;
}
```

### 函数的结果和计算状态分开

```c
#include <stdio.h>

int getRemainder(int num1, int num2, int *remainder)
{
    if (num2 == 0)
    {
        return 1;
    }
    *remainder = num1 % num2;
    return 0;
}

int main(int argc, char const *argv[])
{
    int a = 10;
    int b = 2;
    int res = 0;

    int flag = getRemainder(a, b, &res);

    if (!flag)
    {
        printf("Quotient: %d\n", res);
    }

    return 0;
}
```

### 方便的操作数组和函数

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    /*
        指针运算
            步长：指针移动一次，走的字节数目
                char : 1
                short : 2
                int : 4
                long : 4
                long long : 8
                加法/减法
     */
    int a = 10;
    int *p = &a;
    printf("p = %p, *p = %d\n", p, *p);
    printf("%p\n", p + 1);
    printf("%p\n", p - 1);
    printf("%p\n", p + 2);
    long x = 10;
    int x = 10;
    return 0;
}
```

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    /*
        指针运算
            步长：指针移动一次，走的字节数目
                char : 1
                short : 2
                int : 4
                long : 4
                long long : 8
                加法/减法
     */
    int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    int *p1 = &arr[0];

    printf("p1 指向的元素: %d\n", *p1);
    printf("p1 + 1 指向的元素: %d\n", *(p1 + 1));

    int *p2 = &arr[5];
    printf("%d\n", p1);
    printf("%d\n", p2);

    printf("%d\n", p2 - p1);

    return 0;
}
```

### 指向不明的指针

**野指针**：指针指向的空间未分配

**悬空指针**：指针指向的空间已经分配，但是被释放了

```c
#include <stdio.h>

int *method()
{
    int num = 13;
    int *p = &num;
    return p;
}

int main(int argc, char const *argv[])
{
    int a = 10;
    int *p1 = &a;
    printf("%p\n", p1);
    printf("%d\n", *p1);

    // p2 野指针
    int *p2 = p1 + 10;
    printf("%p\n", p2);
    printf("%d\n", *p2);

    // 悬空指针：指针指向的内存已分配，但是被释放了

    int *p3 = method();
    printf("delay some time\n");

    printf("%p\n", p3);
    printf("%d\n", *p3);
    return 0;
}
```

### 没有类型的指针

**特点**：无法获取数据，无法计算，但是可以接受任意地址

```c
#include <stdio.h>

// swap 函数用于交换两个内存区域的内容
// p1 和 p2 是指向要交换的内存地址的指针，len 是要交换的字节数
void swap(void *p1, void *p2, int len)
{
    // 将 void 指针转换为 char 指针，便于字节级别的操作
    char *pc1 = p1;
    char *pc2 = p2;

    char temp = 0; // 临时变量，用于存储字节内容

    // 循环 len 次，逐字节交换内容
    for (int i = 0; i < len; i++)
    {
        temp = *pc1;   // 保存 pc1 指向的字节
        *pc1 = *pc2;   // 将 pc2 指向的字节赋值给 pc1
        *pc2 = temp;   // 将原 pc1 指向的字节赋值给 pc2

        pc1++; // 移动到下一个字节
        pc2++; // 移动到下一个字节
    }
}

int main(int argc, char const *argv[])
{
    int a = 10; // 整数变量 a
    short b = 20; // 短整型变量 b
    int *p1 = &a; // 指向 a 的指针
    short *p2 = &b; // 指向 b 的指针

    // 打印变量 a 和 b 的值
    printf("a = %d, b = %d\n", a, b);
    printf("*p1 = %d, *p2 = %d\n", *p1, *p2);

    // 不同类型的指针之间不能直接赋值
    // void 指针可以接受任何类型的指针
    char *p3 = (char *)p1; // 将整型指针 p1 转换为字符型指针
    // printf("%c\n", *p3); // 不建议解引用，因为 p3 可能指向非字符数据

    void *p4 = p1; // 使用 void 指针 p4 存储 p1 的地址
    printf("%p\n", p4); // 打印 p4 的地址
    printf("%p\n", p4 + 1); // 虽然可以进行指针算术运算，但这里的结果不安全

    void *p5 = p2; // 使用 void 指针 p5 存储 p2 的地址
    // printf("%d\n", *p5); // 不建议解引用，因为 p5 是 void 指针

    // 使用 swap 函数交换两个 long long int 类型的值
    long long int c = 100; // 声明 long long int 变量 c
    long long int d = 200; // 声明 long long int 变量 d
    printf("%lld,%lld\n", c, d); // 打印 c 和 d 的值

    swap(&c, &d, sizeof(long long int)); // 调用 swap 函数进行交换
    printf("%lld,%lld\n", c, d); // 打印交换后的 c 和 d 的值

    return 0;
}
```

### 二级指针和多级指针

指针数据类型：跟指向空间中，数据的类型是保持一致的。

作用：二级指针可以操作一级指针记录的地址

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{

    int a = 10;
    int b = 20;

    int *p = &a;
    int **pp = &p;

    // 作用一：利用二级指针修改一级指针里面记录的内存地址
    *pp = &b;

    printf("%p\n", &a);
    printf("%p\n", &b);
    printf("%p\n", p);
    // 作用二：利用二级指针获取变量中记录的数据
    printf("%d\n", **pp);

    return 0;
}
```

### 数组指针的基本用法

概念：指向数组的指针，叫做数组指针

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    int arr[] = {10, 20, 30, 40, 50};
    int len = sizeof(arr) / sizeof(int);

    int *p1 = arr;
    int *p2 = &arr[0];

    printf("%p\n", p1);
    printf("%p\n", p2);

    printf("%d\n", *p1);
    printf("%d\n", *(p1 + 1));
    printf("%d\n", *(p1 + 2));
    printf("%d\n", *(p1 + 3));

    for (int i = 0; i < len; i++)
    {
        printf("%d\n", *p1++);
    }

    return 0;
}
```

- arr参与计算的时候，会退化为第一个元素的指针
  - 特殊情况：
    - sizeof 运算的时候，不会退化，arr还是整体
    - &arr获取地址的时候，不会退化

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    int arr[] = {10, 20, 30, 40, 50};
    int len = sizeof(arr) / sizeof(int);

    // sizeof 运算的时候，arr还是整体
    printf("%zu\n", sizeof(arr));

    printf("%p\n", arr);
    // &arr 时， arr还是整体
    printf("%p\n", &arr);

    printf("%p\n", arr + 1);
    printf("%p\n", &arr + 1);

    return 0;
}
```

### 通过指针便利数组

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    /*
        二维数组的定义和遍历方式

        定义格式方式一:
        int arr[行数][列数] = {
            {元素1, 元素2, ...},
            {元素1, 元素2, ...},
            ...
        };
    */

    // 定义一个 3x5 的二维数组并初始化
    int arr[3][5] = {
        {1, 2, 3, 4, 5}, // 第一行
        {1, 2, 3, 4, 5}, // 第二行
        {1, 2, 3, 4, 5}  // 第三行
    };

    // 定义一个指向包含 5 个整数的数组的指针，指向 arr
    int(*p)[5] = arr;

    // 遍历二维数组
    for (int i = 0; i < 3; i++) // 外层循环遍历行
    {
        for (int j = 0; j < 5; j++) // 内层循环遍历列
        {
            // 使用指针解引用和偏移来访问数组元素
            printf("%d ", *(*p + j)); // 打印当前行的元素
        }
        printf("\n"); // 换行以分隔不同的行
        p += 1;       // 移动指针到下一行
    }

    return 0; // 返回 0 表示程序正常结束
}
```

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    /*
        二维数组的定义和遍历方式

        定义格式方式二:
        通过一维数组的指针数组来实现二维数组的效果。
    */

    // 定义三个一维数组
    int arr1[3] = {1, 2, 3};                   // 第一个数组，包含 3 个元素
    int arr2[5] = {1, 2, 3, 4, 5};             // 第二个数组，包含 5 个元素
    int arr3[9] = {1, 2, 3, 4, 5, 6, 7, 8, 9}; // 第三个数组，包含 9 个元素

    // 计算每个数组的长度
    int len1 = sizeof(arr1) / sizeof(int); // arr1 的长度
    int len2 = sizeof(arr2) / sizeof(int); // arr2 的长度
    int len3 = sizeof(arr3) / sizeof(int); // arr3 的长度

    // 将数组长度存入一个一维数组
    int len[3] = {len1, len2, len3}; // 存储各数组的长度

    // 定义一个指针数组，指向三个一维数组
    int *arr[3] = {arr1, arr2, arr3}; // arr 数组中的每个元素都是指向整型的指针

    // 定义一个指向指针数组的指针
    int **p = arr; // p 是指向指向整型的指针的指针

    // 遍历指针数组
    for (int i = 0; i < 3; i++) // 外层循环遍历指针数组的行
    {
        for (int j = 0; j < len[i]; j++) // 内层循环遍历当前数组的列
        {
            // 使用指针解引用，打印当前数组的元素
            printf("%d ", *(*p + j)); // 打印当前行的第 j 个元素
        }
        printf("\n"); // 换行以分隔不同的数组
        p += 1;       // 移动指针到下一个数组
    }

    return 0; // 返回 0 表示程序正常结束
}
```

### 数组指针和指针数组

**数组指针**：指向数组的指针

作用：方便的操作数组中的各种数据

举例：`int* p = arr`; （步长为：int 4个字节）

举例：`int (*p)[5] = &arr`; （步长为 ：int 乘5 20个字节）

**指针数组**：存放指针的数组

作用：用来存放指针 

举例：`int *p[5]`， 这个数组里面存放着int类型的指针

### 函数和指针

格式：返回值类型（*指针名）（形参列表）

作用：利用函数指针，可以动态的调用函数

```c
#include <stdio.h>

// 定义一个无参数无返回值的函数
void method1()
{
    printf("method1\n"); // 打印函数名
}

// 定义一个接受两个整数并返回整数的函数
int method2(int num1, int num2)
{
    printf("method2\n"); // 打印函数名
    return num1 + num2;  // 返回两个参数的和
}

int main(int argc, char const *argv[])
{
    // 声明一个指向无参数无返回值函数的指针
    void (*p1)() = method1; // p1 指向 method1 函数

    // 声明一个指向接受两个整数并返回整数的函数的指针
    int (*p2)(int, int) = method2; // p2 指向 method2 函数

    // 调用无参数函数
    p1(); // 通过指针调用 method1

    // 调用有参数的函数，并存储返回值
    int num = p2(1, 2);  // 通过指针调用 method2，传递参数 1 和 2
    printf("%d\n", num); // 打印 method2 的返回值

    return 0; // 返回 0 表示程序正常结束
}
```

### 函数指针

```c
#include <stdio.h>

// 定义加法函数
int add(int num1, int num2)
{
    return num1 + num2; // 返回两个数的和
}

// 定义减法函数
int subtract(int num1, int num2)
{
    return num1 - num2; // 返回第一个数减去第二个数的结果
}

// 定义乘法函数
int multiply(int num1, int num2)
{
    return num1 * num2; // 返回两个数的积
}

// 定义除法函数
int divide(int num1, int num2)
{
    return num1 / num2; // 返回第一个数除以第二个数的结果
}

int main(int argc, char const *argv[])
{
    int a = 10; // 示例变量 a
    int b = 20; // 示例变量 b

    // 创建一个函数指针数组，存储四个基本运算函数
    int (*arr[4])(int, int) = {add, subtract, multiply, divide};

    // 提示用户输入两个数字
    printf("please input two number to calculate\n");
    int num1, num2;
    scanf("%d%d", &num1, &num2); // 读取用户输入的两个数字
    printf("%d\n", num1);        // 打印第一个数字
    printf("%d\n", num2);        // 打印第二个数字

    // 提示用户选择运算
    int choose;
    printf("please input one number to specify the operation you need\n");
    scanf("%d", &choose); // 读取用户输入的选择

    // 调用相应的运算函数并存储结果
    int res = (arr[choose - 1])(num1, num2); // 根据选择调用函数
    printf("%d\n", res);                     // 打印运算结果

    return 0; // 返回 0 表示程序正常结束
}
```



## 
