---
title : 'LeetCode每日一题（202501）'
date : 2025-01-25T15:37:01+08:00
lastmod: 2025-01-25T15:37:01+08:00
description : "每日一题（202501）" 
image : img/cat.jpg
draft : false    
categories : ["LeetCode"]
tags : ["LeetCode笔记"]
---

# 每日一题（202501）

## 0124 [2944. 购买水果需要的最少金币数](https://leetcode.cn/problems/minimum-number-of-coins-for-fruits/)

### 1. 题目大意

给定一个整数数组 `prices`，其中 `prices[i]` 表示购买第 `i` 个水果所需的金币数。每购买一个水果后，可以根据一定的规则免费获得后续水果。规则是：购买第 `i` 个水果后，可以免费获得下标在 `[i+1, i+i]` 范围内的水果。你的目标是通过选择购买一些水果，最小化总花费，同时确保获得所有水果。

### 2. 实现思路

- **状态定义：**
   通过动态规划来计算最小的金币花费。使用 `dfs(i)` 表示从第 `i` 个水果开始购买所需的最少金币数。
- **状态转移：**
  - 如果购买第 `i` 个水果，可以免费获得从 `i+1` 到 `min(i+i, n-1)` 范围内的水果。
  - 对于每个水果 `i`，考虑购买它后，所需要的金币数是购买当前水果的金币 `prices[i]` 加上购买后免费获得的水果的最少金币数。
- **递归定义：**
  - `dfs(i)` 表示从第 `i` 个水果开始，最少需要多少金币。
  - 如果当前水果 `i` 能够免费获得之后的一些水果，可以跳过这些水果直接递归计算下一个需要购买的水果。
  - `dfs(i)` 通过递归的方式遍历后续可能的选择，选择最小的金币花费。
- **边界情况：**
  - 如果购买到最后一个水果，只需要购买这个水果的金币。
- **记忆化递归：**
   为了避免重复计算，可以使用缓存来存储已经计算过的结果。

### 3. 带有注释的代码

```python
class Solution:
    def minimumCoins(self, prices: List[int]) -> int:
        n = len(prices)

        # 使用缓存来存储已经计算过的结果，避免重复计算
        @cache
        def dfs(i):
            # 如果当前水果已是最后一个或接近最后一个，直接返回购买该水果的金币数
            if i + i >= n:
                return prices[i - 1]
            
            # 计算购买第i个水果后的最小金币花费
            # i+1 到 i+i 范围内的水果可以免费获得，因此我们从这个范围内选择最少花费的水果
            return min(dfs(j) for j in range(i + 1, i * 2 + 2)) + prices[i - 1]

        # 从第1个水果开始计算，返回最少的金币花费
        return dfs(1)
```

## 0125 [2412. 完成所有交易的初始最少钱数](https://leetcode.cn/problems/minimum-money-required-before-transactions/)

### 1. 题目大意

给定一系列交易，每笔交易由两部分组成：交易成本 `costi` 和交易后返还的现金 `cashbacki`。每次交易完成后，账户余额会减少 `costi`，并增加 `cashbacki`。为了确保所有交易都能够完成，你需要计算出最少需要多少初始金额 `money`，无论交易的顺序如何，都能顺利完成所有交易。

### 2. 实现思路

- **核心思想：**
  - 对于每一笔交易 `transactions[i] = [costi, cashbacki]`，如果 `costi > cashbacki`，那么进行该交易时，初始资金至少需要满足 `costi - cashbacki`，否则就只需要足够的资金来进行最贵的一笔交易。
  - 遍历所有交易，累加每一笔交易的差额 `costi - cashbacki`，并记录最大 `costi` 和最大 `cashbacki`，这两者代表了能影响最低资金需求的交易。
  - 最终的初始资金为所有差额的总和，再加上最大值 `max(max_cost, max_back)`，其中 `max_cost` 是最大交易成本，`max_back` 是最大现金返还。
- **步骤：**
  1. 遍历所有交易，分别更新 `total`（所有差额之和），`mx_back`（最大返还）和 `mx_cost`（最大成本）。
  2. 最终的最小初始资金为 `total + max(mx_cost, mx_back)`，这个值确保了无论交易顺序如何都能完成。

### 3. 代码实现

```python
class Solution:
    def minimumMoney(self, transactions: List[List[int]]) -> int:
        total = 0  # 用于存储所有交易的成本和返还的差额之和
        mx_back = mx_cost = 0  # 用于记录最大返还现金和最大交易成本
        
        # 遍历每一笔交易
        for cost, back in transactions:
            # 如果成本大于返还，则需要增加成本和返还的差额
            if cost > back:
                total += (cost - back)
                # 更新最大返还现金
                mx_back = max(mx_back, back)
            else:
                # 如果返还大于等于成本，只需要考虑最大交易成本
                mx_cost = max(mx_cost, cost)

        # 返回最小初始金额，即所有差额之和加上最大成本或最大返还中的较大者
        return total + max(mx_cost, mx_back)
```

