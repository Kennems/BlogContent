---
title : 'LeetCode周赛240414'
date : 2024-04-16T15:37:01+08:00
lastmod: 2024-04-16T15:37:01+08:00
description : "LeetCode 周赛 393(240414)" 
image : img/cat.jpg
draft : false    
categories : ["LeetCode"]
tags : ["LeetCode周赛"]
# password : leetcode
---

# LeetCode第 393 场周赛

## 第三题[3116. 单面值组合的第 K 小金额](https://leetcode.cn/problems/kth-smallest-amount-with-single-denomination-combination/)

题目大意：
给定一个整数数组coins表示不同面额的硬币，另给定一个整数k。你有无限量的每种面额的硬币，但是，你不能组合使用不同面额的硬币。要求返回使用这些硬币能制造的第kth小金额。

实现思路：
- 首先，对于给定的硬币数组，我们需要求出它们的所有可能的组合方式。
- 使用位运算来枚举所有的组合方式，对于数组中的每个硬币，分别考虑选取和不选取两种情况，通过位运算将这两种情况枚举出来。
- 对于每种组合方式，计算其对应的最小公倍数（LCM）作为该组合的金额。
- 根据组合的奇偶性，将金额的正负号记录下来，并存储在列表ls中。
- 利用二分查找来求解第kth小的金额，通过不断调整左右边界，直到找到满足条件的金额。
- 返回最终找到的金额作为结果。

这种方法利用了位运算和二分查找的思想，可以在较短的时间内求解出结果。

```py
class Solution:
    def findKthSmallest(self, coins: List[int], k: int) -> int:
        n = len(coins)
        ls = []
        for i in range(1, 1<<n):
            Lcm = 1
            for j, x in enumerate(coins):
                if i>>j & 1:
                    Lcm = lcm(Lcm, x)
            ls.append((1, Lcm)) if i.bit_count()&1 else ls.append((-1, Lcm))
        l, r = 1, int(5e10)
        while l<r:
            mid = l+r>>1
            cnt = 0
            for sign, val in ls:
                cnt += sign*(mid//val)
            if cnt>=k:
                r = mid
            else:
                l = mid+1
        return l
```



## 第四题[3117. 划分数组得到最小的值之和](https://leetcode.cn/problems/minimum-sum-of-values-by-dividing-array/)

题目大意：
给定两个数组nums和andValues，长度分别为n和m。数组的值等于该数组的最后一个元素。需要将nums划分为m个不相交的连续子数组，对于第ith个子数组[li, ri]，子数组元素的按位AND运算结果等于andValues[i]。返回将nums划分为m个子数组所能得到的可能的最小子数组值之和。如果无法完成这样的划分，则返回-1。

实现思路：
- 使用动态规划来解决此问题，具体来说，可以采用递归加记忆化搜索的方法。
- 定义dfs函数用于递归求解，其中i表示当前处理到nums的第i个元素，j表示当前处理到andValues的第j个元素，k表示当前子数组的按位AND运算结果。
- 在dfs函数中，首先进行边界条件的判断，如果i等于n，表示nums已经处理完毕，则返回0（如果j等于m）或者正无穷（如果j不等于m）。
- 然后，对当前nums[i]进行按位AND运算，并更新k的值。如果更新后的k小于andValues[j]，则返回正无穷。
- 如果更新后的k等于andValues[j]，则递归调用dfs函数继续处理下一个元素，同时更新i和j，并将k重置为-1。递归调用的结果加上nums[i]的值即为当前划分情况的子数组值之和。
- 最后，返回所有划分情况中的最小值作为答案，如果答案为正无穷，则返回-1。

这种方法利用了递归和记忆化搜索的思想，避免了重复计算，提高了效率。

```py
class Solution:
    def minimumValueSum(self, nums: List[int], andValues: List[int]) -> int:
        n = len(nums)
        m = len(andValues)

        @cache
        def dfs(i, j, k):
            if i==n:
                return 0 if j==m else inf
            if j==m:
                return inf
            k&=nums[i]
            if k<andValues[j]:
                return inf
            res = dfs(i+1, j, k)
            if k==andValues[j]:
                res = min(res, dfs(i+1, j+1, -1)+nums[i])
            return res
        ans = dfs(0, 0, -1)
        return ans if ans<inf else -1
```

