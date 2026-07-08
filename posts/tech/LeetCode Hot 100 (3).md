---
title : 'LeetCode Hot 100 精练🥸(3)'
date : 2024-12-08T10:00:01+08:00
lastmod: 2026-05-11T10:00:01+08:00
description : "LeetCode Hot 100 精练🥸(3)" 
categories : ["LeetCode"]
tags : ["LeetCode Hot 100"]
---

# LeetCode Hot 100 精练🥸(3)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。

## (41) [309. 买卖股票的最佳时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

**题目大意**：
给定一个股票价格数组，可以多次买卖，但卖出后必须冷冻一天才能再次买入，求最大利润。

**解题思路**：
动态规划，定义三个状态：第 i 天持有股票、第 i 天刚卖出（冷冻期）、第 i 天不持有且非冷冻。持有状态可由前一天持有或今天买入（从非冷冻状态）转移；卖出状态由前一天持有加今天价格转移；非冷冻状态由前一天冷冻或前一天非冷冻转移。最终答案为 max(卖出状态, 非冷冻状态)。

给定一个整数数组`prices`，其中第 `prices[i]` 表示第 `*i*` 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        prices = [0] + prices
        f = [[-inf] * 3 for _ in range(n + 2)]  # f[i][j] 第i天 距离上次卖出后第j天
        f[1][2] = 0  # 入口，合法的情况

        for i in range(1, n + 1):
            f[i][0] = max(f[i - 1][0], f[i][2] - prices[i])  # 第i天持有
            f[i][1] = f[i - 1][0] + prices[i]  # 今天买入，进入冷冻期
            f[i + 1][2] = max(f[i - 1][1], f[i][2])  # 明天能达到非冷冻期的状态

        return max(f[n][1], f[n + 1][2])  # 买股票后第一天，买股票后第大于等于2天
```

```go
func maxProfit(prices []int) int {
	n := len(prices)
	f := make([][]int, n+2)
	inf := 0x3f3f3f3f
	for i := 0; i <= n+1; i += 1 {
		f[i] = make([]int, 3)
		for j := 0; j < 3; j += 1 {
			f[i][j] = -inf
		}
	}

	f[1][2] = 0

	for i, p := range prices {
		f[i+1][0] = max(f[i][0], f[i+1][2]-p)
		f[i+1][1] = f[i][0] + p
		f[i+2][2] = max(f[i+1][2], f[i][1])
	}

	return max(f[n][1], f[n+1][2])
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

## (42)[301. 删除无效的括号](https://leetcode.cn/problems/remove-invalid-parentheses/)

**题目大意：**

给你一个由若干括号和字母组成的字符串 `s` ，删除最小数量的无效括号，使得输入的字符串有效。

返回所有可能的结果。答案可以按 **任意顺序** 返回。

#### 回溯

**实现思路：**

1. **统计无效括号数量：**
   - 首先遍历字符串s，统计左括号和右括号的数量l和r，其中l表示未匹配的左括号数量，r表示需要删除的右括号数量。

2. **回溯算法：**
   - 编写回溯算法来尝试删除不同位置的括号，使得字符串有效。
   - 回溯过程中需要注意以下情况：
     - 已经匹配的括号数量ln和rn，以及当前位置i。
     - 遍历字符串s的每个字符，当遇到重复的字符时跳过，以避免重复结果。
     - 如果当前字符为左括号并且ln不为0，则递归调用删除当前左括号的情况，ln减一，否则继续。
     - 如果当前字符为右括号并且rn不为0，则递归调用删除当前右括号的情况，rn减一，否则继续。

3. **检查字符串有效性：**
   - 编写辅助函数`check`来检查字符串是否有效，即左右括号数量是否匹配。

4. **返回结果：**
   - 将所有有效的字符串结果添加到结果列表ret中，并返回。

```py
class Solution:
    def removeInvalidParentheses(self, s: str) -> List[str]:
        l, r = 0, 0
        ret = []
        n = len(s)

        for c in s:
            if c=='(':
                l+=1
            elif c==')':
                if l==0:
                    r+=1
                else:
                    l-=1

        def check(st):
            cnt=0
            for c in st:
                if c=='(':
                    cnt+=1
                elif c==')':
                    cnt-=1
                    if cnt<0:
                        return False
            return (cnt==0)
        
        def backtrace(st, start, ln, rn):
            if ln==0 and rn==0 and check(st):
                ret.append(st[:])
                return 
            for i in range(start, len(st)):
                if i>start and st[i]==st[i-1]:
                    continue
                if ln+rn>n-1-i+1:
                    break
                if ln and st[i]=='(':
                    backtrace(st[:i]+st[i+1:], i, ln-1, rn)
                elif rn and st[i]==')':
                    backtrace(st[:i]+st[i+1:], i, ln, rn-1)
                
        backtrace(s, 0, l, r)
        return ret
```

#### 广搜

这个实现使用了广度优先搜索（BFS）的思路来解决问题。

1. **BFS搜索：**
   - 使用一个集合`cur`来存储当前层的所有字符串，初始时将输入的字符串`s`加入集合`cur`中。
   - 在每一轮循环中，遍历当前集合`cur`中的所有字符串，如果其中有字符串是有效的，则将其加入结果列表`ret`中。
   - 如果结果列表`ret`不为空，则跳出循环，表示已经找到了所有有效的字符串。
   - 如果结果列表`ret`为空，则需要继续进行下一轮搜索，将当前集合`cur`中的每个字符串，通过删除一个括号的方式，生成所有可能的下一层字符串，加入到集合`nxt`中。
   - 更新当前集合`cur`为`nxt`，继续下一轮搜索。

2. **检查字符串有效性：**
   - 定义一个辅助函数`check`，用于检查字符串是否有效。
   - 遍历字符串中的每个字符，遇到左括号时增加计数器`cnt`，遇到右括号时如果`cnt`为0则返回False（表示右括号没有匹配的左括号），否则减少`cnt`。
   - 最终判断`cnt`是否为0，如果为0表示字符串有效。

3. **返回结果：**
   - 返回结果列表`ret`，其中存储了所有有效的字符串。

```py
class Solution:
    def removeInvalidParentheses(self, s: str) -> List[str]:
        l = r = 0
        res = []

        for c in s:
            if c == "(":
                l += 1
            elif c == ")":
                if l:
                    l -= 1
                else:
                    r += 1

        def check(s):
            cnt = 0
            for c in s:
                if c == "(":
                    cnt += 1
                elif c == ")":
                    if cnt == 0:
                        return False
                    cnt -= 1
            return cnt == 0

        def dfs(s, start, l, r):
            if l == 0 and r == 0:
                if check(s):
                    res.append(s)
                return

            for i in range(start, len(s)):
                if i > start and s[i] == s[i - 1]:
                    continue
                if l + r > len(s) - i:
                    return

                if l > 0 and s[i] == "(":
                    dfs(s[:i] + s[i + 1:], i, l - 1, r)

                if r > 0 and s[i] == ")":
                    dfs(s[:i] + s[i + 1:], i, l, r - 1)

        dfs(s, 0, l, r)
        return res
```

## (43)[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

**题目大意**：
给定一个整数数组，找出其中最长严格递增子序列的长度（子序列不必连续）。

**解题思路**：
动态规划，定义 f[i] 为以第 i 个元素结尾的最长递增子序列长度。对于每个位置 i，枚举之前的所有位置 j（j < i），若 nums[j] < nums[i]，则 f[i] = max(f[i], f[j] + 1)。最终答案为 max(f[0..n-1])。时间复杂度 O(n^2)。

 给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

**子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

```py
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n = len(nums)
        f = [1] * (n + 1)

        for i in range(n):
            for j in range(i, n):
                if nums[j] > nums[i]:
                    f[j] = max(f[j], f[i] + 1)
        
        return max(f)
```



## (44)[297. 二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)

不止第一次遇到了。

**题目大意**：序列化是将一个数据结构或对象转换为连续的比特位的操作，从而可以存储在文件或内存中，并且通过网络传输到另一个计算机环境中。本题要求设计一个算法来实现二叉树的序列化和反序列化，即将二叉树转换为字符串并将字符串转换回原始的二叉树结构。

**实现思路**：

1. 序列化：使用递归将二叉树转换为字符串，根节点值与左右子树序列化结果之间使用空格分隔，空节点用 '#' 表示。
2. 反序列化：使用递归将字符串转换为二叉树。首先定义一个辅助函数 DerWork()，用于递归构建二叉树。在该函数中，按照前序遍历的顺序，依次提取字符串中的节点值，并根据节点值构建二叉树节点。如果节点值为 '#'，表示空节点，返回 None。否则，创建节点并递归构建其左右子树。
3. 在反序列化过程中，使用一个全局变量 self.s 来记录当前待处理的字符串，每次递归处理后，将字符串中已经处理过的部分剔除，继续处理剩余部分。
4. 最终返回根节点即可。

```py
class Codec:
    def serialize(self, root):
        if not root:
            return "#"
        return (
            str(root.val) + " " + self.serialize(root.left) + " " + self.serialize(root.right)
        )

    def deserialize(self, data):
        self.s = data
        return self.DerWork()

    def DerWork(self):
        if len(self.s) == 0:
            return None

        try:
            idx = self.s.index(" ")
        except:
            idx = -1

        node = self.s if idx == -1 else self.s[:idx]
        self.s = "" if idx == -1 else self.s[idx + 1 :]
        
        if node == "#":
            return None

        t = TreeNode(int(node))
        t.left = self.DerWork()
        t.right = self.DerWork()
        return t
```

## (45)[287. 寻找重复数](https://leetcode.cn/problems/find-the-duplicate-number/)

**题目大意**：
给定一个包含 n+1 个整数的数组，数字都在 [1, n] 范围内，其中只有一个数重复出现，找出该重复数。要求不修改数组且只能使用 O(1) 额外空间。

**解题思路**：
二分查找值域 [1, n]。对于每个中间值 mid，统计数组中在 (mid, r] 范围内的元素个数。若该个数大于 r - mid，说明重复数在右半区间，否则在左半区间。不断缩小区间直到 l == r，此时 l 即为重复数。时间复杂度 O(n log n)。

给定一个包含 `n + 1` 个整数的数组 `nums` ，其数字都在 `[1, n]` 范围内（包括 `1` 和 `n`），可知至少存在一个重复的整数。

假设 `nums` 只有 **一个重复的整数** ，返回 **这个重复的数** 。

你设计的解决方案必须 **不修改** 数组 `nums` 且只用常量级 `O(1)` 的额外空间。

```py
class Solution:
    def findDuplicate(self, nums: List[int]) -> int:
        l, r = 0, len(nums) - 1
        while l < r:
            mid = l + r >> 1
            s = 0
            for num in nums:
                if num > mid and num <= r:
                    s += 1
            if s > r - mid:
                l = mid + 1
            else:
                r = mid
        return l
```



## (46)[283. 移动零](https://leetcode.cn/problems/move-zeroes/)

**题目大意**：
给定一个数组，将所有 0 移动到数组末尾，同时保持非零元素的相对顺序。要求原地操作，不能复制数组。

**解题思路**：
双指针法，i 指向下一个非零元素应放置的位置，j 遍历整个数组。当 nums[j] 非零时，将其与 nums[i] 交换（若 i != j），然后 i 右移。最终所有非零元素被移到前面，零自然被挤到末尾。时间复杂度 O(n)。

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。

```py
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        i, j = 0, 0
        while j < len(nums):
            if nums[j]:
                if i != j:
                    nums[i], nums[j] = nums[j], nums[i]
                i += 1
            j += 1

```

```go
func moveZeroes(nums []int) {
    i, j := 0, 0
    for j < len(nums) {
        if nums[j] != 0 {
            if i != j {
                nums[i], nums[j] = nums[j], nums[i]
            }
            i++
        }
        j++
    }
}

```



## (47)[279. 完全平方数](https://leetcode.cn/problems/perfect-squares/)

**题目大意**：
给定一个整数 n，求最少需要多少个完全平方数（如 1, 4, 9, ...）相加得到 n。

**解题思路**：
完全背包问题（DP）。预处理出所有不超过 n 的完全平方数作为可选物品，每个物品可无限次使用。定义 f[j] 为凑出和 j 所需的最少个数，转移方程 f[j] = min(f[j], f[j - square[i]] + 1)。初始化 f[0] = 0，其余为 inf。时间复杂度 O(n * sqrt(n))。

给你一个整数 `n` ，返回 *和为 `n` 的完全平方数的最少数量* 。

**完全平方数** 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，`1`、`4`、`9` 和 `16` 都是完全平方数，而 `3` 和 `11` 不是。

```py
class Solution:
    def numSquares(self, n: int) -> int:
        ls = []
        i = 1
        while True:
            if i**2 > n:
                break
            ls.append(i**2)
            i += 1

        l = len(ls)
        f = [0] + [inf] * n
        for i in range(l):
            for j in range(ls[i], n + 1):
                f[j] = min(f[j], f[j - ls[i]] + 1)

        return f[n]

```

## (48)[会议室 II](https://www.lintcode.com/problem/919/description)

**题目大意**：
给定一系列会议的时间区间，求最少需要多少个会议室才能安排所有会议。

**解题思路**：
差分数组/扫描线法。将每个区间的起始时间和结束时间分别视为+1和-1的事件，按时间排序后扫描，累加当前同时进行的会议数，记录最大值即为所需最少会议室数。时间复杂度 O(n log n)，由排序决定。

描述

给定一系列的会议时间间隔intervals，包括起始和结束时间`[[s1,e1],[s2,e2],...] (si < ei)`，找到所需的最小的会议室数量。

```py
class Solution:
    """
    @param intervals: an array of meeting time intervals
    @return: the minimum number of conference rooms required
    """
    def min_meeting_rooms(self, intervals: List[Interval]) -> int:
        # Write your code here
        d = defaultdict(int) #diff差分数组

        for inter in intervals:
            s, e = inter.start, inter.end
            d[s] += 1
            d[e] -= 1

        s, res = 0, 0
        for k, v in sorted(d.items()):
            s += v 
            res = max(res, s)

        return res
```

```go
func MinMeetingRooms(intervals []*Interval) int {
    // 差分数组：key 是时间点，value 是变化量
    diff := make(map[int]int)

    for _, inter := range intervals {
        diff[inter.Start]++
        diff[inter.End]--
    }

    // 把时间点取出来排序
    times := make([]int, 0, len(diff))
    for t := range diff {
        times = append(times, t)
    }
    sort.Ints(times)

    cur, res := 0, 0
    for _, t := range times {
        cur += diff[t]
        if cur > res {
            res = cur
        }
    }

    return res
}
```

## (49)[240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

**题目大意**：
在一个每行递增、每列也递增的 m x n 矩阵中，高效搜索目标值 target。

**解题思路**：
从矩阵右上角开始搜索，利用行列单调性逐步缩小范围。若当前元素等于 target 则返回 true；若大于 target，则该列下方所有元素都更大，左移一列；若小于 target，则该行左侧所有元素都更小，下移一行。时间复杂度 O(m + n)。

编写一个高效的算法来搜索 `*m* x *n*` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

```py
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        m, n = len(matrix), len(matrix[0])
        i, j = 0, n - 1
        while i < m and j >= 0:
            if matrix[i][j] == target:
                return True
            if matrix[i][j] > target:
                j -= 1
            elif matrix[i][j] < target:
                i += 1

        return False
```



## (50)[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

**题目大意**：
给定一个整数数组和一个大小为 k 的滑动窗口，窗口从数组最左侧移动到最右侧，每次右移一位。返回每个窗口内的最大值。

**解题思路**：
使用单调递减双端队列维护窗口内元素下标。遍历数组时，先移除队首超出窗口范围的元素，再将队尾所有小于当前值的元素出队（因为它们不可能是之后窗口的最大值），然后将当前下标入队。当窗口大小达到 k 时，队首元素即为当前窗口最大值。时间复杂度 O(n)。

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回 *滑动窗口中的最大值* 。

```py
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        q = deque()
        res = []

        for i in range(n):
            while q and i - q[0] + 1 > k:
                q.popleft()
            while q and nums[q[-1]] <= nums[i]:
                q.pop()

            q.append(i)

            if i + 1 >= k:
                res.append(nums[q[0]])

        return res
```



## (51)[22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)

**题目大意**：
给定数字 n 表示括号对数，生成所有可能且有效的括号组合。

**解题思路**：
DFS 回溯。维护当前已放置的左括号数 l 和右括号数 r。每次可以选择放置左括号（要求 l < n）或放置右括号（要求 r < l，确保右括号不会超过左括号）。当 l == r == n 时得到一个有效组合。时间复杂度近似 Catalan 数 O(4^n / sqrt(n))。

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```py
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        res = []
        ls = []
        n *= 2

        def dfs(i, l, r):
            if l > n // 2 or r > n // 2:
                return

            if i == n and l == r:
                res.append("".join(ls))
                return

            ls.append("(")
            dfs(i + 1, l + 1, r)
            ls.pop()

            if r < l:
                ls.append(")")
                dfs(i + 1, l, r + 1)
                ls.pop()

        dfs(0, 0, 0)
        return res

```



## (52)[49. 字母异位词分组](https://leetcode.cn/problems/group-anagrams/)

**题目大意**：
给定一个字符串数组，将字母异位词（由相同字母重排列而成的单词）分组在一起，可以按任意顺序返回结果列表。

**解题思路**：
哈希表法。对每个字符串，将其排序后的结果作为键，原字符串添加到对应值的列表中。因为字母异位词排序后会得到相同的字符串，所以它们会被分到同一组。时间复杂度 O(n * k log k)，其中 n 是字符串数量，k 是最大字符串长度。

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。

```py
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        d = defaultdict(list)

        for s in strs:
            sort_s = sorted(list(s))
            d["".join(sort_s)].append(s)
        
        return list(d.values())
```



## (53)[48. 旋转图像](https://leetcode.cn/problems/rotate-image/)

**题目大意**：
给定一个 n x n 的二维矩阵表示图像，要求原地将图像顺时针旋转 90 度。

**解题思路**：
先沿主对角线转置矩阵（matrix[i][j] 与 matrix[j][i] 交换），然后反转每一行即可得到顺时针旋转 90 度的结果。两步均为原地操作，时间复杂度 O(n^2)，空间复杂度 O(1)。

给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在**[ 原地](https://baike.baidu.com/item/原地算法)** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

```py
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        n = len(matrix)
        newmat = [[0] * n for _ in range(n)]
        for i in range(n):
            for j in range(n):
                newmat[i][j] = matrix[n - 1 - j][i]

        matrix[:] = newmat

```



## (54)[46. 全排列](https://leetcode.cn/problems/permutations/)

**题目大意**：
给定一个不含重复数字的数组，返回其所有可能的全排列。

**解题思路**：
DFS 回溯法。维护一个 visited 数组标记已被使用的元素，递归时依次尝试每个未使用的数字加入当前路径，递归完成后回溯（撤销选择并标记未使用）。当路径长度等于数组长度时，将当前路径加入结果集。时间复杂度 O(n * n!)。

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        vis = [False] * (n)
        res, ls = [], []

        def dfs(i):
            if len(ls) == n:
                res.append(ls[:])
                return

            for j in range(n):
                if not vis[j]:
                    vis[j] = True

                    ls.append(nums[j])
                    dfs(j + 1)
                    ls.pop()

                    vis[j] = False

        dfs(1)

        return res

```



## (55)[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

**题目大意**：
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算下雨后能接到的雨水总量。

**解题思路**：
单调递减栈法。遍历每个柱子时，若当前高度大于栈顶高度，说明栈顶柱子形成了低洼处可以接水。弹出栈顶作为底部，取当前柱子和新栈顶中较矮的一个作为水面高度，计算 "宽度 * (水面高度 - 底部高度)" 累加。栈中存储的是下标，保证宽度的正确计算。时间复杂度 O(n)。

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

```py
class Solution:
    def trap(self, height: List[int]) -> int:
        res = 0
        stk = []

        for i, h in enumerate(height):
            while stk and height[stk[-1]] < h:
                cur = stk.pop()

                if not stk:
                    break

                left = stk[-1]
                curW = i - 1 - left
                curH = min(h, height[left]) - height[cur]
                res += curH * curW

            stk.append(i)

        return res

```





## (56)[39. 组合总和](https://leetcode.cn/problems/combination-sum/)

**题目大意**：
给定一个无重复元素的整数数组和一个目标值 target，找出所有和为 target 的组合，同一个数字可以无限次被选取。

**解题思路**：
DFS 回溯法。先对数组排序以便剪枝。递归时维护当前起始索引 i 和当前和 s，从 i 开始枚举（允许重复选取同一元素），将当前元素加入路径并递归（仍从同一索引开始）。当 s >= target 时终止：若 s == target 则记录结果，否则直接返回。排序后可提前 break 避免无效分支。时间复杂度取决于解的数量。

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

```py
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        n = len(candidates)
        candidates.sort()
        res, ls, = [], []

        def dfs(i, s):
            if s >= target:
                if s == target:
                    res.append(ls[:])
                return 
            
            for j in range(i, n):
                ls.append(candidates[j])

                dfs(j, s + candidates[j])

                ls.pop()

        dfs(0, 0)

        return res


```



## (57)[543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)

**题目大意**：
给定一棵二叉树，返回其直径——即任意两个节点之间最长路径的边数。该路径可能经过也可能不经过根节点。

**解题思路**：
DFS 递归。在每个节点计算其左右子树的最大深度（边数），以该节点为"拐点"的路径长度为 left + right，更新全局最大值 res。返回值为以该节点为起点的最大深度 max(left, right) + 1。时间复杂度 O(n)。

给你一棵二叉树的根节点，返回该树的 **直径** 。

二叉树的 **直径** 是指树中任意两个节点之间最长路径的 **长度** 。这条路径可能经过也可能不经过根节点 `root` 。

两节点之间路径的 **长度** 由它们之间边数表示。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        res = 0

        def dfs(root):
            nonlocal res
            if not root:
                return 0
            ls = dfs(root.left)
            rs = dfs(root.right)
            res = max(res, ls + rs)
            return max(ls, rs) + 1

        dfs(root)
        return res
```



## (58)[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

**题目大意**：
给定一个非递减排序的整数数组和一个目标值，找出目标值在数组中的开始位置和结束位置。要求 O(log n) 时间复杂度。

**解题思路**：
两次二分查找。第一次查找第一个 >= target 的位置（左边界），第二次查找第一个 >= target+1 的位置，然后减一得到右边界。若左边界处值不等于 target，说明 target 不存在，返回 [-1, -1]。时间复杂度 O(log n)。

 给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

```py
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        def bs(nums, target):
            l, r = 0, len(nums) - 1
            while l < r:
                mid = l + r >> 1
                if nums[mid] >= target:
                    r = mid
                else:
                    l = mid + 1
            return l

        if not nums:
            return [-1, -1]

        left = bs(nums, target)
        if nums[left] != target:
            return [-1, -1]

        right = bs(nums, target + 1)
        if nums[right] != target:
            right -= 1

        return [left, right]
```



## (59)[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

**题目大意**：
给定一个按升序排列的整数数组 `nums`，数组中的值互不相同。该数组经过未知的某个下标旋转，即原本排在数组开头的一部分元素被移动到数组末尾。给定一个目标值 `target`，如果该目标值存在于旋转后的数组中，则返回其下标，否则返回 -1。要求设计一个时间复杂度为 O(log n) 的算法解决此问题。

**实现思路**：

1. 使用二分查找算法来解决此问题，以满足 O(log n) 的时间复杂度要求。
2. 初始化左右指针 `l` 和 `r` 分别指向数组的首尾元素。
3. 在循环中，计算中间位置 `mid`，判断 `nums[mid]` 是否等于目标值 `target`，若是则直接返回 `mid`。
4. 若 `nums[0] <= nums[mid]`，说明左半段是有序的，此时判断目标值是否在左半段范围内，若是则将右指针移到 `mid-1`，否则将左指针移到 `mid+1`。
5. 若 `nums[0] > nums[mid]`，说明右半段是有序的，此时判断目标值是否在右半段范围内，若是则将左指针移到 `mid+1`，否则将右指针移到 `mid-1`。
6. 若循环结束仍未找到目标值，则返回 -1。

```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        if not nums:
            return -1
        n = len(nums)
        l, r = 0, n - 1
        while l <= r:
            mid = l + r >> 1
            if nums[mid] == target:
                return mid
            if nums[0] <= nums[mid]:
                if nums[0] <= target < nums[mid]:
                    r = mid - 1
                else:
                    l = mid + 1
            else:
                if nums[mid] < target <= nums[n - 1]:
                    l = mid + 1
                else:
                    r = mid - 1
        return -1
```

## (60)[32. 最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

**题目大意**：
给定一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

**解题思路**：
使用栈存储下标，初始化栈底为 -1（作为有效子串起点的前一个位置）。遍历字符串，遇到 '(' 将下标入栈；遇到 ')' 则弹出栈顶。若弹出后栈为空，说明当前右括号无法匹配，将当前下标入栈作为新的"最后一个无效位置"；否则用当前下标减去栈顶下标得到当前有效子串长度，更新最大值。时间复杂度 O(n)。

给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号 子串 的长度。

左右括号匹配，即每个左括号都有对应的右括号将其闭合的字符串是格式正确的，比如 `"(()())"`。

```py
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        n = len(s)
        stk = [-1]
        res = 0

        for i in range(n):
            if s[i] == "(":
                stk.append(i)
            else:
                stk.pop()
                if stk:
                    res = max(res, i - stk[-1])
                else:
                    stk.append(i)

        return res
```
