---
title : 'Pandas'
date : 2025-03-29T10:30:13+08:00
lastmod: 2025-03-29T10:20:13+08:00
description : "NumPy" 
categories : ["AI"]
tags : ["AI"]
---

# Pandas

# 1. pandas 简介

pandas 是 Python 中一个功能强大的数据分析和数据处理库，它提供了高性能、易用的数据结构（如 Series 和 DataFrame），方便进行数据的导入、清洗、转换、分析和可视化。pandas 广泛应用于金融、统计、科学计算等领域。

**文档查阅**：pandas 的[官方文档](https://pandas.pydata.org/docs/)非常详细，是学习和解决问题的好帮手。

------

# 2. 基本数据结构

## 2.1 Series

Series 是一种类似一维数组的对象，同时包含数据和索引。常用于表示单列数据。

```python
import pandas as pd

# 创建一个 Series
data = [10, 20, 30, 40]
s = pd.Series(data, index=['a', 'b', 'c', 'd'])
print(s)
```

输出示例：

```
a    10
b    20
c    30
d    40
dtype: int64
```

## 2.2 DataFrame

DataFrame 是一种二维的表格型数据结构，每一列可以是不同的数据类型，类似于数据库中的表格。

```python
# 创建一个 DataFrame
data = {
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'Score': [88, 92, 95]
}
df = pd.DataFrame(data)
print(df)
```

输出示例：

```
      Name  Age  Score
0    Alice   25     88
1      Bob   30     92
2  Charlie   35     95
```

------

# 3. 数据导入与导出

pandas 提供了非常多的数据导入导出方法，常用的包括：

## 3.1 读取 CSV 文件

```python
# 读取 CSV 文件
df_csv = pd.read_csv('data.csv')
```

常用参数说明：

- `sep`：指定分隔符，默认是逗号（`,`）。
- `header`：指定文件的表头行号。
- `encoding`：指定文件编码，如 `utf-8` 或 `gbk`。

## 3.2 写入 CSV 文件

```python
# 将 DataFrame 写入 CSV 文件
df.to_csv('output.csv', index=False)
```

参数说明：

- `index`：是否保存行索引，通常设为 False。

## 3.3 读取 Excel 文件

```python
# 读取 Excel 文件
df_excel = pd.read_excel('data.xlsx', sheet_name='Sheet1')
```

## 3.4 写入 Excel 文件

```python
# 写入 Excel 文件
df.to_excel('output.xlsx', index=False)
```

------

# 4. 数据查看和探索

在处理数据前，我们需要了解数据的基本情况。以下是一些常用方法：

## 4.1 查看前几行和后几行

```python
print(df.head(5))  # 显示前 5 行，默认 head() 显示前 5 行
print(df.tail(5))  # 显示后 5 行
```

## 4.2 数据维度和基本信息

```python
print(df.shape)  # 返回 (行数, 列数)
print(df.info())  # 显示 DataFrame 的信息（数据类型、缺失值等）
print(df.describe())  # 显示数值型数据的统计信息
```

------

# 5. 数据选择与索引

pandas 提供了多种方式来选取数据。

## 5.1 通过列名选择

```python
# 选择单个列
age_series = df['Age']

# 选择多个列
subset = df[['Name', 'Score']]
```

## 5.2 使用 loc 和 iloc

- **.loc** 用于通过标签选择数据（包括行和列）。

```python
# 选择行索引为 0 到 1 的数据，并选取 'Name' 和 'Score' 列
subset_loc = df.loc[0:1, ['Name', 'Score']]
```

- **.iloc** 用于通过位置选择数据。

```python
# 选择第一行到第二行（不包含第三行），所有列
subset_iloc = df.iloc[0:2, :]
```

## 5.3 布尔索引

```python
# 筛选年龄大于 28 的数据
filtered_df = df[df['Age'] > 28]
```

------

# 6. 数据处理与清洗

## 6.1 处理缺失值

缺失值（NaN）处理是数据清洗的重要部分。

```python
# 查看缺失值情况
print(df.isnull().sum())

# 删除含有缺失值的行
df_drop = df.dropna()

# 填充缺失值（例如，用 0 填充）
df_fill = df.fillna(0)
```

## 6.2 重命名列名

```python
df_renamed = df.rename(columns={'Name': '姓名', 'Age': '年龄'})
```

## 6.3 修改数据类型

```python
# 将 'Age' 列转换为浮点型
df['Age'] = df['Age'].astype(float)
```

## 6.4 去重

```python
# 删除重复行
df_unique = df.drop_duplicates()
```

## 6.5 排序

```python
# 按年龄升序排序
df_sorted = df.sort_values(by='Age')

# 按多列排序，如先按年龄，再按分数降序排序
df_sorted_multi = df.sort_values(by=['Age', 'Score'], ascending=[True, False])
```

------

# 7. 数据聚合与分组

分组操作常用于数据的聚合和统计分析。

## 7.1 groupby

```python
# 假设 DataFrame 中有一列 'Category'，分组计算每组的平均分数
grouped = df.groupby('Category')['Score'].mean()
print(grouped)
```

groupby 常用方法：

- `mean()`：计算均值
- `sum()`：求和
- `count()`：计数
- `agg()`：支持多种聚合函数组合

```python
# 使用 agg() 同时计算均值和标准差
grouped_stats = df.groupby('Category').agg({'Score': ['mean', 'std']})
```

## 7.2 pivot_table 数据透视表

数据透视表可以快速对数据进行汇总。

```python
pivot = pd.pivot_table(df, values='Score', index='Category', columns='Gender', aggfunc='mean')
print(pivot)
```

------

# 8. 数据合并与连接

处理分散数据时，经常需要将多个 DataFrame 合并。

## 8.1 concat 合并

```python
# 纵向拼接（按行合并）
df_concat = pd.concat([df1, df2], axis=0)

# 横向拼接（按列合并）
df_concat_columns = pd.concat([df1, df2], axis=1)
```

## 8.2 merge 合并

类似 SQL 的 join 操作，可以根据指定的键合并两个 DataFrame。

```python
# 内连接：只保留两个 DataFrame 中共有的键
df_merged = pd.merge(df1, df2, on='id', how='inner')

# 左连接：以左侧 DataFrame 为主，右侧数据补充
df_left = pd.merge(df1, df2, on='id', how='left')
```

## 8.3 join 方法

DataFrame 自带的 join 方法也可以进行索引对齐的合并。

```python
# 以 df1 的索引为主合并 df2
df_joined = df1.join(df2, how='left')
```

------

# 9. 时间序列处理

pandas 对时间序列数据有很好的支持。

## 9.1 日期时间解析

```python
# 将字符串转换为 datetime 类型
df['date'] = pd.to_datetime(df['date'])
```

## 9.2 设置索引为日期

```python
# 将日期列设置为索引
df.set_index('date', inplace=True)
```

## 9.3 重采样

```python
# 按月计算均值
df_monthly = df.resample('M').mean()
```

------

# 10. 常用 API 总结示例

以下是一个综合示例，展示了如何使用 pandas 完成数据导入、处理、分组和导出操作。

```python
import pandas as pd

# 1. 导入数据
df = pd.read_csv('sales_data.csv')

# 2. 数据清洗：去除缺失值
df.dropna(inplace=True)

# 3. 转换日期类型，并设置为索引
df['Date'] = pd.to_datetime(df['Date'])
df.set_index('Date', inplace=True)

# 4. 根据产品类别分组，计算总销售额和平均单价
grouped = df.groupby('Product').agg({
    'Sales': 'sum',
    'Price': 'mean'
}).reset_index()

# 5. 排序
grouped = grouped.sort_values(by='Sales', ascending=False)

# 6. 导出结果
grouped.to_csv('sales_summary.csv', index=False)

print("数据处理完成，汇总结果已保存。")
```

------

# 11. 补充技巧

- **链式调用**：为了让代码更简洁，可以使用链式调用。例如：

  ```python
  df_clean = (
      pd.read_csv('data.csv')
      .dropna()
      .rename(columns=lambda x: x.strip().lower())
  )
  ```

- **调试与检查**：在处理数据过程中，多使用 `head()`、`tail()`、`info()` 等方法及时检查数据情况，避免后续操作出错。

------

# 12. 总结

pandas 提供了丰富的 API 和灵活的数据处理能力，本文介绍了从数据导入、查看、清洗、处理到数据聚合、合并以及时间序列处理等常用操作。掌握这些常用 API 后，你可以快速进行数据分析和数据科学项目开发。建议在实践中不断探索更多高级功能，例如多层索引、窗口函数、以及与其他库（如 NumPy、Matplotlib）的结合使用，从而进一步提升数据处理的效率和能力。

希望这份教程对你学习和使用 pandas 能有所帮助！