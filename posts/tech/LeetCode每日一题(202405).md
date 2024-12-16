---
title : 'LeetCode每日一题(202405)'
date : 2024-05-01T15:37:01+08:00
lastmod: 2024-05-07T15:37:01+08:00
description : "每日一题（202405）" 
image : img/cat.jpg
draft : false    
categories : ["LeetCode"]
tags : ["LeetCode笔记"]
# password : leetcode
---


# LeetCode每日一题(2405)

## [1235. 规划兼职工作](https://leetcode.cn/problems/maximum-profit-in-job-scheduling/)

**题目大意**：给定`n`份兼职工作，每份工作都有开始时间、结束时间和报酬。任务是选择一些工作，使得在不重叠的情况下能够获得最大报酬。

**实现思路**：首先对工作按照结束时间进行排序，然后使用动态规划来求解最大报酬。在动态规划的过程中，维护一个数组`f`，其中`f[i]`表示在考虑前`i`个工作时可以获得的最大报酬。遍历每个工作，对于第`i`个工作，找到在其开始时间之前且结束时间最接近的工作`j`，然后更新f[i]为`f[j] + 第i个工作的报酬`。最终返回`f[n]`即为所求的最大报酬。

```py
class Solution:
    def jobScheduling(self, startTime: List[int], endTime: List[int], profit: List[int]) -> int:
        # 兵贵神速，现在能领先一秒，以后就能按照复利一直领先
        n = len(startTime)
        job = [(0, 0, 0)] + sorted(zip(endTime, startTime, profit))
        f = [0]*(n+1)
        for i, (_, st, p) in enumerate(job[1:], start = 1):
            # l, r = 0, i-1
            # while l<r:
            #     mid = (l+r+1)>>1
            #     if job[mid][0] > st:
            #         r = mid-1
            #     else:
            #         l = mid
            # f[i] = max(f[i-1], f[l] + p)
            l = bisect_left(job, (st+1, ), hi = i, lo = 0) # 内置库只能查找 严格 > < 的值
            # 无法找到 >= <=的值，但是可以在找到最大的 <目标值的 i, i-1即为<=目标值的下标
            f[i] = max(f[i-1], f[l-1] + p)
        
        return f[n]

```




## [741. 摘樱桃](https://leetcode.cn/problems/cherry-pickup/)

**题目大意**：给定一个网格，其中包含空格、樱桃和荆棘，要求从左上角到右下角，然后返回左上角，按照规定的走法，最多能摘到的樱桃数量。

### 记忆化搜索

**实现思路**：这是一个动态规划问题。定义一个递归函数`dfs(t, j, l)`，表示在时刻`t`，第一个人位于`(j, t-j)`，第二个人位于`(l, t-l)`时，能够得到的最大樱桃数量。递归终止条件是当其中一个人走到了边界之外或者碰到了荆棘时，返回负无穷。然后递归地计算四种可能的移动方向：向下走、向右走、向下和向右走、向左走，同时更新樱桃数量。最后返回经过路径能够摘到的最多樱桃数量。

```py
class Solution:
    def cherryPickup(self, g: List[List[int]]) -> int:
        @cache
        def dfs(t, j, l):
            if j<0 or l<0 or t-j<0 or t-l<0 or g[t-j][j]==-1 or g[t-l][l]==-1:
                return -inf
            if t==0:
                return g[0][0]
            return max(dfs(t-1, j, l), dfs(t-1, j-1, l), dfs(t-1, j, l-1), dfs(t-1, j-1, l-1)) \
                     + g[t-j][j] + (g[t-l][l] if j!=l else 0)
        
        n = len(g)
        return max(0, dfs(2*n-2, n-1, n-1))
```

### 递推

**实现思路**：

1. 使用动态规划来解决。定义一个三维数组 `f`，其中 `f[t][i][j]` 表示在 t 步中，第一个人位于 `(i-1, t-i)`、第二个人位于 `(j-1, t-j)` 时所能摘到的最大樱桃数。
2. 初始条件为 `f[0][1][1] = grid[0][0]`，表示开始时第一个人和第二个人都在左上角，第一个格子上的樱桃数。
3. 然后进行状态转移，对于每一步 `t`，遍历两个人可能的位置 `(i, t-i)，(j, t-j)`：
   - 如果该位置不是荆棘，则计算能从上一步到达当前位置的最大樱桃数。状态转移方程为：`f[t][i][j] = max(f[t-1][i][j], f[t-1][i-1][j], f[t-1][i][j-1], f[t-1][i-1][j-1]) + 当前格子上的樱桃数`。
4. 最后返回 `f[2*n-2][n][n]`，表示在返回过程中最多能摘到的樱桃数。

```py
class Solution:
    def cherryPickup(self, grid: List[List[int]]) -> int:
        n = len(grid)
        f = [[[-inf]*(n+1) for _ in range(n+1)] for _ in range(2*n-1)]
        f[0][1][1] = grid[0][0]
        for t in range(1, 2*n-1):
            for j in range(max(t-n+1, 0), min(n, t+1)):
                if grid[t-j][j]==-1: continue
                for k in range(j, min(n, t+1)):
                    if grid[t-k][k]==-1: continue
                    f[t][j+1][k+1] = max(f[t-1][j+1][k+1], f[t-1][j][k+1], f[t-1][j+1][k], f[t-1][j][k]) \
                                             + grid[t-j][j] + (grid[t-k][k] if j!=k else 0)
        
        return max(0, f[2*n-2][n][n])
```



## [463. 摘樱桃 II](https://leetcode.cn/problems/cherry-pickup-ii/)

**题目大意**：给定一个矩阵表示樱桃地，两个机器人分别从左上角和右上角出发，每个机器人只能向下一行的左下、下方和右下方移动，当机器人到达一个格子时，会摘取该格子内所有的樱桃并将其置空。两个机器人不能同时摘取同一个格子的樱桃，求两个机器人能够摘取的最大樱桃总数。

### 记忆化搜索

**实现思路**：可以使用动态规划来解决。定义一个递归函数 `dfs(i, j, k)` 表示机器人1位于第i行第j列，机器人2位于第i行第k列时，能够摘取的最大樱桃总数。递归过程中，对于每个机器人，都有三种移动方式，即向下一行的左下、下方和右下方移动。递归终止条件为机器人到达最后一行。使用缓存装饰器 @cache 可以避免重复计算。最后，返回`dfs(0, 0, m-1)`，其中m为矩阵的列数。

```py
class Solution:
    def cherryPickup(self, grid: List[List[int]]) -> int:
        n, m = len(grid), len(grid[0])
        @cache
        def dfs(i, j, k):
            if i==n or j<0 or j>=m or k<0 or k>=m:
                return 0
            return max(dfs(i+1, nj, nk) for nj in range(j-1, j+2) for nk in range(k-1, k+2)) \
                    + grid[i][j] + (grid[i][k] if j!=k else 0)
        return dfs(0, 0, m-1)
```

### 递推

**实现思路**：使用动态规划进行解决。定义一个三维数组 `f`，其中 `f[i][j+1][k+1]` 表示机器人1位于第i行第j列，机器人`2`位于第i行第k列时，能够摘取的最大樱桃总数。通过三重循环，依次遍历每一行和每一列，更新 `f[i][j+1][k+1]` 的值，其中 `i` 表示当前行，`j` 表示机器人`1`的列数，`k` 表示机器人`2`的列数。在更新 `f[i][j+1][k+1]` 的过程中，通过遍历机器人1和机器人2的位置，计算能够摘取的最大樱桃总数。最后返回 `f[0][1][m]`，其中 `m` 为矩阵的列数。

```py
class Solution:
    def cherryPickup(self, grid: List[List[int]]) -> int:
        n, m = len(grid), len(grid[0])
        f = [[[0]*(m+2) for _ in range(m+2)] for _ in range(n+1)]
        for i in range(n-1, -1, -1):
            for j in range(min(i+1, m)):
                for k in range(max(j+1, m-1-i), m):
                    t = 0
                    for nj in range(j, j+3):
                        for nk in range(k, k+3):
                            t = max(t, f[i+1][nj][nk])
                    f[i][j+1][k+1] = t + grid[i][j] + grid[i][k]
        return f[0][1][m]
```

