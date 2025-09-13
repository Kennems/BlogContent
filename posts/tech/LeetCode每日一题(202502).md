---
title : 'LeetCode每日一题（202502）'
date : 2025-02-02T15:37:01+08:00
lastmod: 2025-02-02T15:37:01+08:00
description : "每日一题（202502）" 
image : img/cat.jpg
draft : false    
categories : ["LeetCode"]
tags : ["LeetCode笔记"]
---

# 每日一题（202502）

## 0201 [81. 搜索旋转排序数组 II](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/)

### 题目大意

给定一个旋转排序数组 `nums` 和一个目标值 `target`，判断目标值是否存在于数组中。需要尽可能减少操作步骤。

### 解题思路

1. **旋转数组特点**：旋转后数组分为两部分，其中一部分是有序的。
2. 使用二分查找：
   - 判断左半部分是否有序，如果有序，判断目标值是否在这部分。
   - 否则，判断右半部分是否有序，并进行类似操作。
3. **处理重复元素**：如果 `nums[left] == nums[mid] == nums[right]`，无法确定哪部分有序，直接移动指针。

### 代码实现

```py
class Solution:
    def search(self, nums: List[int], target: int) -> bool:
        if not nums:
            return False
        
        n = len(nums)

        if n == 1:
            return nums[0] == target
        
        l, r = 0, n - 1
        while l <= r:
            mid = l + r >> 1
            if nums[mid] == target:
                return True

            if nums[l] == nums[mid] and nums[mid] == nums[r]:
                l += 1
                r -= 1
            elif nums[l] <= nums[mid]:
                if nums[l] <= target < nums[mid]:
                    r = mid - 1
                else:
                    l = mid + 1
            else:
                if nums[mid] < target <= nums[n - 1]:
                    l = mid + 1
                else:
                    r = mid - 1
        
        return False
```



## 0202 MarsCode 每日一题 [红色格子染色方案数计算](https://www.marscode.cn/practice/65621ew1pe38dj?problem_id=7424418560931987500) 

### 1. 题目大意

有一个长度为 `n` 的格子，一部分已染红，剩余格子未染色。通过特定的规则（红色格子可以染色左右邻居），我们需要计算将所有格子染红的不同染色顺序。最终返回结果对 `10^9 + 7` 取模。

### 2. 解题思路

1. **状态表示**：使用一个整数来表示当前状态。每个格子是红色（1）还是未染色（0），用二进制表示。
2. **状态转移**：从每个红色格子出发，尝试向左右邻居染色。生成新状态并更新方案数。
3. **广度优先搜索（BFS）**：从初始状态出发，使用队列逐步遍历所有可能的状态，并计算染色方案数。
4. **动态规划**：通过字典 `f` 记录每个状态的方案数，避免重复计算。

```py
from collections import defaultdict, deque
mod = int(1e9) + 7
def solution(n, s):
    initial = int(s, 2)
    end = (1 << n) - 1
    vis = defaultdict(bool)
    q = deque([initial])
    f = defaultdict(int)
    f[initial] = 1

    while q:
        cur = q.popleft()
        if vis[cur]:
            continue 
        vis[cur] = True

        for i in range(n):
            if (cur >> i) & 1 == 0:
                if (i - 1 >= 0 and (( cur >> (i - 1) ) & 1)) or (i + 1 <= n - 1 and (( cur >> (i + 1) ) & 1)):
                    nx = cur | (1 << i)
                    f[nx] = (f[nx] + f[cur]) % mod
                    q.append(nx)

    return f[end]

# 测试样例
if __name__ == '__main__':
    print(solution(5, "00101") == 3)  # 3种方式
    print(solution(6, "100001") == 8)  # 8种方式
    print(solution(7, "0001000") == 20)  # 20种方式

```

