---
title : 'NumPy'
date : 2025-03-29T10:30:13+08:00
lastmod: 2025-03-29T10:20:13+08:00
description : "NumPy" 
categories : ["AI"]
tags : ["AI"]
---

# NumPy

# 1. NumPy 简介

NumPy（Numerical Python）是 Python 进行科学计算的基础库，提供了高效的多维数组（ndarray）对象和丰富的数值计算函数。它在数据处理、机器学习、工程计算等领域都有广泛应用。

文档：https://numpy.org/doc/stable/

------

# 2. ndarray：核心数据结构

NumPy 中的核心数据结构是 ndarray（N-Dimensional Array），它是一个同类型元素组成的多维数组。

## 2.1 创建数组

- **从列表创建：**

```python
import numpy as np

# 一维数组
a = np.array([1, 2, 3, 4])
print(a)

# 二维数组
b = np.array([[1, 2, 3], [4, 5, 6]])
print(b)
```

- **使用内置函数创建：**

```python
# 创建全 0 数组
zeros = np.zeros((3, 4))
print(zeros)

# 创建全 1 数组
ones = np.ones((2, 3))
print(ones)

# 创建等间隔数组
arange_arr = np.arange(0, 10, 2)  # 从 0 到 10，步长为 2
print(arange_arr)

# 创建等间隔浮点数数组
linspace_arr = np.linspace(0, 1, 5)  # 在 0 到 1 之间生成 5 个数
print(linspace_arr)
```

------

# 3. 数组属性

了解数组的基本属性有助于后续操作：

```python
print("数组 a 的维度：", a.ndim)      # 维度
print("数组 b 的形状：", b.shape)      # 形状
print("数组 b 的元素个数：", b.size)    # 元素个数
print("数组 b 的数据类型：", b.dtype)   # 数据类型
```

------

# 4. 数组索引和切片

NumPy 支持多种索引和切片方式，适用于一维和多维数组。

## 4.1 一维数组

```python
# 索引
print(a[0])  # 第一个元素
print(a[-1]) # 最后一个元素

# 切片
print(a[1:3])  # 从索引 1 到 2 的元素
print(a[:2])   # 前两个元素
print(a[2:])   # 从第三个元素开始
```

## 4.2 多维数组

```python
# 二维数组索引：行索引, 列索引
print(b[0, 1])  # 第一行第二列的元素
print(b[1])     # 第二行的所有元素

# 多维切片
print(b[:, 1])   # 所有行的第二列
print(b[0, :2])  # 第一行前两个元素
```

------

# 5. 数组运算

NumPy 支持矢量化运算，极大提高运算效率。

## 5.1 基本算术运算

```python
x = np.array([1, 2, 3])
y = np.array([4, 5, 6])

# 加减乘除
print(x + y)  # [5, 7, 9]
print(x - y)  # [-3, -3, -3]
print(x * y)  # [ 4, 10, 18]
print(x / y)  # [0.25, 0.4, 0.5]

# 幂运算
print(x ** 2)  # [1, 4, 9]
```

## 5.2 广播机制

当数组形状不同时，NumPy 会自动进行广播，使得运算成为可能。

```python
A = np.array([[1, 2, 3],
              [4, 5, 6]])
b = np.array([10, 20, 30])

# 广播加法
result = A + b
print(result)
```

广播机制还支持标量与数组的运算，如：

```python
print(A * 2)  # 每个元素乘以 2
```

------

# 6. 数组的形状操作

改变数组形状在数据预处理和分析中非常常用。

## 6.1 reshape

```python
c = np.arange(12)
print(c)
d = c.reshape((3, 4))  # 将一维数组变为 3 行 4 列的二维数组
print(d)
```

## 6.2 flatten 与 ravel

```python
# flatten：返回一份扁平化副本
flat = d.flatten()
print(flat)

# ravel：返回原数组的视图（尽可能），改变其中一份也会影响另一份
raveld = d.ravel()
# 等价于 arr.flatten()，但 ravel() 更快且节省内存，因为它通常返回的是 视图（view） 而不是副本（copy）。
print(raveld)
```

## 6.3 Transpose

转置数组：

```python
print(d.T)  # 行列互换
```

------

# 7. 数组统计与聚合

NumPy 提供了丰富的统计函数，便于快速求解均值、方差等。

```python
# 均值
print(np.mean(d))
# 按行求均值
print(np.mean(d, axis=1))
# 按列求均值
print(np.mean(d, axis=0))

# 求和
print(np.sum(d))
# 最大值、最小值
print(np.max(d))
print(np.min(d))
```

其他常用统计函数还包括 `np.median()`、`np.std()`、`np.var()` 等。

------

# 8. 逻辑运算与条件筛选

利用逻辑运算，可以对数组元素进行条件筛选。

```python
# 条件筛选：返回布尔数组
mask = d > 5
print(mask)

# 筛选满足条件的元素
filtered = d[d > 5]
print(filtered)
```

------

# 9. 常用函数和技巧

## 9.1 随机数生成

NumPy 的 random 模块提供了大量随机数生成函数：

```python
# 生成 0 到 1 的均匀分布随机数
rand_arr = np.random.rand(3, 4)
print(rand_arr)

# 生成标准正态分布随机数
randn_arr = np.random.randn(3, 4)
print(randn_arr)

# 生成指定范围内的随机整数
rand_int = np.random.randint(0, 10, size=(3, 3))
print(rand_int)
```

## 9.2 数学函数

NumPy 提供了许多数学函数，如三角函数、指数、对数等：

```python
angles = np.array([0, np.pi/2, np.pi])
print(np.sin(angles))  # 正弦值
print(np.log(np.array([1, np.e, np.e**2])))  # 自然对数
```

## 9.3 累积运算

```python
arr = np.array([1, 2, 3, 4])
print(np.cumsum(arr))  # 累积和
print(np.cumprod(arr)) # 累积积
```

------

# 10. 文件读写

虽然 NumPy 的主要功能是数值计算，但也提供了简单的数组文件读写功能。

## 10.1 保存和加载二进制文件

```python
# 保存数组到二进制文件
np.save('array.npy', d)

# 加载数组
loaded_array = np.load('array.npy')
print(loaded_array)
```

## 10.2 文本文件读写

```python
# 保存数组到文本文件
np.savetxt('array.txt', d, fmt='%d', delimiter=',')

# 从文本文件加载数组
loaded_text_array = np.loadtxt('array.txt', delimiter=',')
print(loaded_text_array)
```

------

# 11. 综合示例

下面是一个综合示例，展示了如何创建数组、进行运算、形状转换和条件筛选等操作。

```python
import numpy as np

# 1. 创建一个 5x5 的随机整数数组，取值范围 0 到 9
arr = np.random.randint(0, 10, size=(5, 5))
print("原始数组：")
print(arr)

# 2. 计算每行的均值
row_mean = np.mean(arr, axis=1)
print("每行均值：", row_mean)

# 3. 将数组扁平化并排序
flat_sorted = np.sort(arr.ravel())
print("扁平化并排序后的数组：", flat_sorted)

# 4. 筛选出所有大于 5 的元素
filtered = arr[arr > 5]
print("大于 5 的元素：", filtered)

# 5. 将数组重塑为 1x25 的数组
reshaped = arr.reshape(1, -1)
print("重塑后的数组：")
print(reshaped)
```

------

# 12. 总结

NumPy 是进行高效数值计算的基础工具，其核心的 ndarray 对象和丰富的数学、统计、随机数生成函数，使得数据处理和科学计算变得简单且高效。掌握数组的创建、索引切片、运算、广播机制以及形状操作是深入使用 NumPy 的关键。建议在实践中不断探索其高级功能，如线性代数、傅里叶变换、稀疏矩阵等，进一步提高计算效率和代码质量。

希望这份详细教程能帮助你全面掌握 NumPy 的常用 API 及其应用！