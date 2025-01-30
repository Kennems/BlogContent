---
title : 'LeetCode 1215 周赛'
date : 2024-12-15T15:37:01+08:00
lastmod: 2024-12-15T15:37:01+08:00
description : "LeetCode 1215 周赛" 
categories : ["LeetCode"]
tags : ["LeetCode周赛"]
---

# LeetCode 1215 周赛

当你思路对了，等最终调出来最多差两三分钟，但是代码有错误错一次就是五分钟。

## T2 [3387. 两天自由外汇交易后的最大货币数](https://leetcode.cn/problems/maximize-amount-after-two-days-of-conversions/)

### 1. 题目大意

你初始拥有 1 单位的指定货币 `initialCurrency`。在两天内，可以按照分别提供的汇率 `rates1` 和 `rates2` 进行任意次数的货币兑换。每种货币对的汇率可以反向兑换（汇率为 1/rate1/\text{rate}1/rate）。目标是经过两天后，使得最终持有的 `initialCurrency` 数量最大化。

------

### 2. 实现思路

1. **构建汇率图**：
   - 使用图表示两天的汇率关系，每种货币为节点，货币对的汇率为有向边的权重。
   - 构建两个独立的图 `graph1` 和 `graph2`，分别表示第 1 天和第 2 天的汇率图。
2. **广度优先搜索 (BFS)**：
   - 对每一天的汇率图，从某个货币出发，使用 BFS 计算以此货币为起点可到达其他货币的最大兑换值。
   - BFS 过程中维护一个字典 `max_values`，记录当前货币的最大兑换值。
3. **两天的最大化过程**：
   - 第 1 天，从初始货币出发，计算所有可能的货币及其对应的最大数量。
   - 第 2 天，尝试从第 1 天的结果出发，计算回到初始货币的最大可能数量。
4. **返回结果**：
   - 返回经过两天兑换后，回到初始货币 `initialCurrency` 的最大可能值。

```py
class Solution:
    def maxAmount(self, c: str, p1: List[List[str]], r1: List[float], p2: List[List[str]], r2: List[float]) -> float:
        from collections import defaultdict, deque

        # 构建汇率图
        def build(pairs, rates):
            g = defaultdict(list)
            for (s, t), rate in zip(pairs, rates):
                g[s].append((t, rate))      # 添加从 s 到 t 的汇率
                g[t].append((s, 1 / rate)) # 添加从 t 到 s 的反向汇率
            return g
        
        # 使用 BFS 查找从 start 出发到所有货币的最大兑换值
        def bfs(g, start):
            mx = defaultdict(float)  # 存储每种货币的最大兑换值
            mx[start] = 1.0          # 初始货币的数量为 1.0
            q = deque([start])       # BFS 队列初始化为 start
            
            while q:
                cur = q.popleft()    # 当前处理的货币
                for nxt, rate in g[cur]:
                    val = mx[cur] * rate  # 计算通过当前路径到达 nxt 的数量
                    if val > mx[nxt]:     # 如果新值更大，则更新并加入队列
                        mx[nxt] = val
                        q.append(nxt)
            
            return mx

        # 第 1 天的汇率图
        g1 = build(p1, r1)
        # 第 2 天的汇率图
        g2 = build(p2, r2)
        
        # 第 1 天的兑换结果
        res1 = bfs(g1, c)
        
        res = 0
        # 遍历第 1 天的所有可能结果，作为第 2 天的初始状态
        for cur, amt in res1.items():
            # 第 2 天的兑换结果
            res2 = bfs(g2, cur)
            # 如果可以回到初始货币，则计算总兑换值
            if c in res2:
                res = max(res, amt * res2[c])
        
        return res
```

## T3 [3388. 统计数组中的美丽分割](https://leetcode.cn/problems/count-beautiful-splits-in-an-array/)

### 1. 题目大意

给定一个整数数组 `nums`，要求找出所有满足以下条件的 **美丽分割** 的数量：

1. 将数组分为三个非空子数组 `nums1`、`nums2` 和 `nums3`，它们按顺序拼接可以还原为原数组 `nums`。
2. 子数组 `nums1` 是子数组 `nums2` 的前缀，或者 `nums2` 是子数组 `nums3` 的前缀。

子数组是指数组中连续的一段元素，前缀是从数组头部开始的连续子数组。

------

### 2. 实现思路

1. **前缀哈希**：
    为了快速判断子数组是否相等，采用前缀哈希计算子数组的哈希值。哈希值的计算公式为：

   $h[i]=(h[i−1]⋅base+nums[i−1])%modh[i] = (h[i-1] \cdot \text{base} + \text{nums}[i-1]) \% \text{mod}$

   使用哈希可以在 O(1)O(1) 时间内比较任意两个子数组是否相等。

2. **双层遍历**：

   - 第一层遍历确定分割点 `i`，将数组分为 `nums1` 和 `nums2 + nums3`。
   - 第二层遍历确定分割点 `j`，将 `nums2 + nums3` 再分为 `nums2` 和 `nums3`。
   - 在每次分割中判断：
     - `nums1` 是否是 `nums2` 的前缀，或者
     - `nums2` 是否是 `nums3` 的前缀。

3. **判断前缀**：
    使用哈希值比较两个子数组是否相等，同时保证长度关系满足题目要求。

4. **复杂度分析**：

   - 构建前缀哈希的时间复杂度为 $O(n)$。
   - 双层遍历的时间复杂度为$O(n^2)$。
   - 总时间复杂度为$O(n^2)$，可以满足题目要求。

------

### 3. 写满注释的代码

#### 字符串哈希

```python
from typing import List

class Solution:
    def beautifulSplits(self, nums: List[int]) -> int:
        n = len(nums)
        # 前缀哈希数组
        h = [0] * (n + 1)
        # 幂次数组，用于快速计算子数组的哈希值
        p = [1] * (n + 1)
        base = 131
        mod = int(1e9) + 7 # 优化二 适合的质数

        # 预计算前缀哈希值和幂次值
        for i in range(1, n + 1):
            h[i] = (h[i - 1] * base + nums[i - 1]) % mod
            p[i] = (p[i - 1] * base) % mod

        # 子函数：计算 [l, r] 的哈希值
        def get_hash(l, r):
            return (h[r + 1] - h[l] * p[r - l + 1] % mod + mod) % mod

        res = 0
        # 枚举分割点 i，nums1 和 nums2+nums3 的分界点
        for i in range(1, n - 1):
            l1 = i  # nums1 的长度
            # nums1 的哈希值
            s1 = get_hash(0, l1 - 1)
            # 枚举分割点 j，nums2 和 nums3 的分界点
            for j in range(i + 1, n):
                l2 = j - i  # nums2 的长度

                # 检查 nums1 是否是 nums2 的前缀
                if l1 <= l2 and s1 == get_hash(i, i + l1 - 1):
                    # nums3 长度为 n - j，直接统计
                    res += (n - j) # ###优化一
                    break  # 后续分割不再符合条件

                # 检查 nums2 是否是 nums3 的前缀
                l3 = n - j  # nums3 的长度
                s2 = get_hash(i, i + l2 - 1)
                if l2 <= l3 and s2 == get_hash(j, j + l2 - 1):
                    res += 1

        return res
```

#### Z函数

```py
class Solution:
    def beautifulSplits(self, nums: List[int]) -> int:
        def z_function(s):
            n = len(s)
            z = [0] * n
            l, r = 0, 0
            for i in range(1, n):
                if i <= r and z[i - l] < r - i + 1:
                    z[i] = z[i - l]
                else:
                    z[i] = max(0, r - i + 1)
                    while i + z[i] < n and s[z[i]] == s[i + z[i]]:
                        z[i] += 1
                if i + z[i] - 1 > r:
                    l = i
                    r = i + z[i] - 1
            return z
            
        n = len(nums)
        res = 0
        z0 = z_function(nums)

        # 遍历分割点
        for i in range(1, n - 1):  # i 是 nums1 和 nums2 的分割点
            z1 = z_function(nums[i:])
            for j in range(i + 1, n):
                l1, l2, l3 = i, j - i, n - j
                if l1 <= l2 and z0[l1] >= l1:
                    res += (n - j)
                    break

                if l2 <= l3 and z1[l2] >= l2:
                    res += 1

        return res

```

#### LCP

```py
class Solution:
    def beautifulSplits(self, nums: List[int]) -> int:
        n = len(nums)

        lcp = [[0] * (n + 1) for _ in range(n + 1)]

        for i in range(n - 1, -1, -1):
            for j in range(n - 1, i - 1, -1):
                if nums[i] == nums[j]:
                    lcp[i][j] = lcp[i + 1][j + 1] + 1
                    
        res = 0
        for i in range(1, n - 1):
            for j in range(i + 1, n):
                if i <= j - i and lcp[0][i] >= i or \
                   j - i <= n - j and lcp[i][j] >= j - i:
                   res += 1
        
        return res
```

------

## T4 [3389. 使字符频率相等的最少操作次数](https://leetcode.cn/problems/minimum-operations-to-make-character-frequencies-equal/)

### 1、题目大意

给定一个字符串 ss，需要将其转换为一个“好字符串”。“好字符串”的定义是其中所有字符的频率都相等。你可以通过以下操作将 ss 转换为“好字符串”：

- 删除 ss 中的一个字符。
- 向 ss 中添加一个字符。
- 将 ss 中的一个字符变为字母表中的下一个字母（但不能从 'z' 变为 'a'）。

求将 ss 变为“好字符串”所需的最小操作次数。

### 2、实现思路

1. **字符频率统计**： 使用 `Counter` 统计每个字符的频率。
2. **求目标频率**： 设定一个目标频率 `t`，让所有字符的频率尽量接近 `t`。目标频率范围从 0 到字符串中某个字符的最大频率。
3. **计算代价**： 对于每个可能的目标频率 `t`，计算所有字符从当前频率变为 `t` 所需的代价：
       若字符频率高于 `t`，需要通过删除操作减少频率。
       若字符频率低于 `t`，需要通过添加或变换操作增加频率。
4. **优化搜索**： 枚举所有可能的目标频率，记录最小的操作次数。

```py
from collections import Counter

class Solution:
    def makeStringGood(self, s: str) -> int:
        # 统计每个字符的频率，存储为 Counter 对象
        cnt = Counter(s)
        # 将频率值转换为列表，对应字母表顺序 [a, b, ..., z] 的频率
        cnt = [cnt[chr(c)] for c in range(97, 97 + 26)]
        
        # 初始化结果为字符串长度，表示最差情况下所有字符都需替换
        ans = len(s)
        
        # 用于动态规划的辅助数组，表示到当前字母为止的最小操作数
        f = [0] * 27
        
        # 枚举目标频率 target，从 0 到 max(cnt)
        for target in range(max(cnt) + 1):
            # 初始化最后一个字母 z 的操作数，取频率调整为 target 或 0 的最小值
            f[25] = min(cnt[25], abs(cnt[25] - target))
            
            # 从后往前依次计算各字母的最优操作数
            for i in range(24, -1, -1):
                x, y = cnt[i], cnt[i + 1]  # 当前字母和后一个字母的频率
                # 当前字母单独调整到目标频率或 0
                f[i] = f[i + 1] + min(x, abs(x - target))
                # 同时考虑当前字母 x 和后一个字母 y 的调整代价
                if y < target:  # 只有当 y 小于目标频率时，考虑第三种操作
                    t = target if x > target else 0  # x 可能变为 target 或 0
                    f[i] = min(f[i], f[i + 2] + max(x - t, target - y))
            
            # 更新最小操作次数
            ans = min(ans, f[0])
        
        return ans
```

