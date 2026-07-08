---
title : 'LeetCode Hot 100 精练🥸(2)'
date : 2024-12-08T10:00:01+08:00
lastmod: 2026-07-05T10:00:01+08:00
description : "LeetCode Hot 100 精练🥸(2)" 
categories : ["LeetCode"]
tags : ["LeetCode Hot 100"]
---

# LeetCode Hot 100 精练🥸(2)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。

## (21)[139. 单词拆分](https://leetcode.cn/problems/word-break/)

**1、题目大意**
 给定字符串 `s` 和单词字典 `wordDict`，判断是否能用字典中的单词（可重复使用、无需全部使用）按顺序拼接得到 `s`。

**2、实现思路（详细）**
 采用动态规划。
 设 `f[i]` 表示 `s` 的前 `i` 个字符是否可以被成功拆分。
 初始化：`f[0]=True`，表示空串可拆分。
 状态转移：

- 枚举位置 `i`（1 到 n）
- 枚举字典中的单词 `wd`，长度为 `l`
- 若 `i>=l` 且 `s[i-l+1..i] == wd`，则可以从状态 `f[i-l]` 转移
- 更新 `f[i] |= f[i-l]`
   最终返回 `f[n]`。
   通过遍历所有可能的结尾单词，判断是否存在一种合法拆分路径覆盖整个字符串。

**3、实现代码**

```py
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        n = len(s)
        s = "#" + s
        f = [True] + [False] * n

        for i in range(1, n + 1):
            for wd in wordDict:
                l = len(wd)
                if i < l:
                    continue
                if s[i - l + 1 : i + 1] == wd:
                    f[i] |= f[i - l]

        return f[n]
```

```go
func wordBreak(s string, wordDict []string) bool {
	n := len(s)
    s = "#" + s
	f := make([]bool, n+1)
	f[0] = true
	for i := 1; i <= n; i += 1 {
		f[i] = false
	}

	for i := 1; i <= n; i += 1 {
		for _, wd := range wordDict {
			l := len(wd)
			if i < l {
				continue
			}
			if s[i-l+1:i+1] == wd {
				f[i] = f[i-l] || f[i]
			}
		}
	}

	return f[n]
}
```

## (22)[136. 只出现一次的数字](https://leetcode.cn/problems/single-number/)

**1、题目大意**
 给定一个非空整数数组，除一个元素只出现一次外，其余元素均出现两次，要求在线性时间、常量空间内找出这个只出现一次的元素。

**2、实现思路（详细）**
 利用异或运算的性质：

- `a ^ a = 0`
- `a ^ 0 = a`
- 异或满足交换律和结合律

将数组中所有元素依次异或：

- 成对出现的数字相互抵消为 `0`
- 最终剩下的就是只出现一次的元素

整个过程只需一次遍历，额外空间为一个变量，满足题目要求。

**3、实现代码（你的 AC 代码）**

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        x = 0
        for i in nums:
            x ^= i
        return x
```

```go
func singleNumber(nums []int) int {
    res := 0
    for _, num := range(nums) {
        res ^= num
    }
    return res
}
```

## (23)[647. 回文子串](https://leetcode.cn/problems/palindromic-substrings/)

**1、题目大意**
 给定字符串 `s`，统计其中所有回文子串的数量。子串必须连续，回文要求正反读一致。

**2、实现思路（详细）**
 采用「中心扩展法」。
 回文串的中心分两类：

- 单个字符为中心（奇数长度）
- 两个字符之间为中心（偶数长度）

将这两种情况统一处理：

- 枚举所有可能的中心，共 `2*n-1` 个
- 对于每个中心，向左右同时扩展
- 只要左右字符相等，就形成一个回文子串并计数
- 扩展直到越界或字符不相等

所有中心扩展完成后，得到回文子串总数。

**3、实现代码**

```py
class Solution:
    def countSubstrings(self, s: str) -> int:
        n = len(s)
        res = 0

        for i in range(2 * n - 1):
            l, r = i // 2, i // 2 + (i & 1)
            while l >= 0 and r < n and s[l] == s[r]:
                res += 1
                l -= 1
                r += 1

        return res

```

```go
func countSubstrings(s string) int {
    n := len(s)
    res := 0

    for i := 0; i < 2 * n - 1; i += 1 {
        l, r := i / 2, (i + 1) / 2
        for l >= 0 && r < n && s[l] == s[r] {
            res += 1
            l -= 1; r += 1
        }
    }

    return res 
}
```

## (24)[128. 最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

**1、题目大意**
 给定一个未排序整数数组，找出数值连续（相邻差为 1）的最长序列长度，要求时间复杂度为 O(n)。

**2、实现思路（详细）**
 使用哈希表动态维护连续区间的长度。
 核心思想：每个数只在“加入集合”的那一刻参与一次合并。

处理流程：

- 用字典 `d` 记录某个连续区间**端点**对应的区间长度
- 遍历每个数 `nm`，若已出现则跳过
- 查询 `nm-1` 结尾的区间长度 `left`，以及 `nm+1` 开头的区间长度 `right`
- 合并形成新区间，长度 `cur = left + right + 1`
- 更新最大长度
- 只在新区间的左右端点写入长度：
  - 左端点 `nm-left`
  - 右端点 `nm+right`
- 中间元素不存长度，避免重复计算

每个元素最多处理一次，整体为线性时间。

**3、实现代码**

```py
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        d = dict()
        res = 0

        for nm in nums:
            if nm not in d:
                left = d.get(nm - 1, 0)
                right = d.get(nm + 1, 0)

                cur = left + right + 1
                if cur > res:
                    res = cur

                d[nm] = ""
                d[nm - left] = cur
                d[nm + right] = cur

        return res
```

```go
func longestConsecutive(nums []int) int {
	d := make(map[int]int)
	res := 0

	for _, nm := range nums {
		if _, exists := d[nm]; !exists {
			left := d[nm-1]
			right := d[nm+1]

			cur := left + right + 1
			if cur > res {
				res = cur
			}

			d[nm] = 1

			d[nm-left] = cur
			d[nm+right] = cur
		}
	}

	return res
}
```

## (25)[124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

### 题目大意：

给定一棵二叉树，要求找出其中路径的最大和。路径可以经过任意节点，但每个节点最多只能被使用一次。路径的定义是从一个节点到另一个节点的连续路径，路径和是路径中所有节点值的和。

### 实现思路：

1. **DFS遍历：** 采用深度优先搜索 (DFS) 来遍历二叉树，计算以每个节点为起点的路径和。
2. **递归的返回值：** 对每个节点，我们需要计算其包含左子树和右子树的最大贡献值。为了计算最大路径和，我们需要考虑以下两种情况：
   - **路径和**：节点自身的值加上其左或右子树的最大路径和。
   - **路径通过当前节点**：考虑从当前节点出发，左子树、右子树都参与的路径。
3. **最大路径和的更新：** 递归过程中维护一个全局变量 `res`，记录当前最大的路径和。对于每个节点，计算其通过左子树和右子树的路径和，然后更新 `res`。
4. **递归返回：** 每次递归返回的是“当前节点及其左右子树路径的最大和”，即一个节点到其父节点的路径和。

```py
class Solution:
    def maxPathSum(self, root: Optional[TreeNode]) -> int:
        res = -inf
        def dfs(r):
            if not r:
                return 0 
            nonlocal res 
            d1 = dfs(r.left)
            d2 = dfs(r.right)

            cur = max(r.val, r.val + max(d1, d2))
            res = max(res, cur, r.val + d1 + d2)

            return cur
        
        dfs(root)

        return res
```

```go
func maxPathSum(root *TreeNode) int {
	res := math.MinInt64

	var dfs func(*TreeNode) int
	dfs = func(r *TreeNode) int {
		if r == nil {
			return 0
		}

		d1 := dfs(r.Left)
		d2 := dfs(r.Right)

		cur := max(r.Val, r.Val+max(d1, d2))
		res = max(res, max(cur, r.Val+d1+d2))

		return cur
	}

	dfs(root)
	return res
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

## (26)[322. 零钱兑换](https://leetcode.cn/problems/coin-change/)

**1、题目大意**
 给定若干种面额的硬币和目标金额 `amount`，每种硬币可无限使用，求凑成目标金额所需的最少硬币数，若无法凑成返回 `-1`。

**2、实现思路**
 这是典型的完全背包动态规划问题。

定义状态：

- `f[j]` 表示凑成金额 `j` 所需的最少硬币数

初始化：

- `f[0] = 0`，金额为 0 不需要硬币
- 其余初始化为无穷大，表示暂时不可达

状态转移：

- 依次遍历每种硬币 `coins[i]`
- 对金额 `j` 从 `coins[i]` 到 `amount` 正向遍历
- 若能从 `j - coins[i]` 转移，则
   `f[j] = min(f[j], f[j - coins[i]] + 1)`

完全背包使用正向遍历金额，保证同一硬币可重复使用。
 最终检查 `f[amount]` 是否仍为无穷大，决定返回结果。

**3、实现代码**

```py
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        n = len(coins)
        f = [0] + [inf] * amount

        for i in range(n):
            for j in range(coins[i], amount + 1):
                f[j] = min(f[j], f[j - coins[i]] + 1)

        return f[amount] if f[amount] != inf else -1
```

```go
func coinChange(coins []int, amount int) int {
    n := len(coins)
    f := make([]int, amount + 1)
    inf := 0x3f3f3f3f
    f[0] = 0
    for i := 1; i <= amount; i += 1 {
        f[i] = inf
    }

    for i := 1; i <= amount; i += 1 {
        for j := 0; j < n; j += 1 {
            if i >= coins[j] {
                f[i] = min(f[i], f[i - coins[j]] + 1)
            }
        }
    }

    if f[amount] < inf {
        return f[amount]
    } else {
        return -1
    }
}
```

## (27) [494. 目标和](https://leetcode.cn/problems/target-sum/)

**题目大意**：给定一个非负整数数组 `nums` 和一个目标整数 `target`，通过给数组中的每个整数前添加 '+' 或 '-'，然后串联起来构造表达式，返回可以构造的表达式数目，使其运算结果等于目标数。

### 记忆化搜索

**实现思路**：这个问题可以转化为一个背包问题。我们可以将问题转化为在数组中选取一些数，使得它们的和等于 `target`。定义一个递归函数 `dfs(i, s)`，表示在数组 `nums` 中考虑第 i 个数，使得目前的和为 `s` 的表达式数目。递归的终止条件是遍历完所有数，如果 `s` 等于 `0`，表示找到了一种构造方式，返回 `1`，否则返回 `0`。递归过程中，如果 `s` 小于当前数 `nums[i]`，说明无法选取当前数，直接跳过；否则，递归考虑选取当前数和不选取当前数两种情况。利用缓存装饰器 `@cache` 可以将重复计算的结果进行缓存，提高计算效率。最后返回 `dfs(0, target)` 即可得到结果。

```py
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        target += sum(nums)
        if target<0 or target&1:
            return 0
        target//=2
        @cache
        def dfs(i, s):
            if i==len(nums):
                return 1 if s==0 else 0
            if s<nums[i]:
                return dfs(i+1, s)
            else:
                return dfs(i+1, s) + dfs(i+1, s-nums[i])
        return dfs(0, target)
```

### 动态规划

**实现思路**：

- **问题转化**：假设数组 `nums` 的元素和为 `s`，我们希望找到一种方式使得加正号和负号的和等于 `target`。通过推导，得到目标问题转化为：$p + n = s$， $p - n = target$ 从数组中选出和为 `(s + target) / 2` 的子集。如果 `s + target` 为负数或奇数，直接返回 0。
- **背包问题**：将问题转化为一个经典的 0-1 背包问题。我们需要求解从 `nums` 中选取一些元素，使得它们的和恰好为 `(s + target) / 2`。
- **动态规划**：定义 `dp[j]` 为和为 `j` 的子集数目。初始化 `dp[0] = 1`，表示和为 0 的方案有 1 种。然后遍历每个元素，并更新 `dp` 数组。

```py
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        target += sum(nums)
        if target < 0 or target % 2:
            return 0
        target //= 2

        n = len(nums)
        f = [[0] * (target + 1) for _ in range(n + 1)]
        f[0][0] = 1
        
        for i in range(1, n + 1):
            for j in range(target + 1):
                if j < nums[i - 1]:
                    f[i][j] = f[i - 1][j]
                else:
                    f[i][j] = f[i - 1][j] + f[i - 1][j - nums[i - 1]]

        return f[n][target]

```

## (28)[461. 汉明距离](https://leetcode.cn/problems/hamming-distance/)

**1、题目大意**
 给定两个非负整数 `x` 和 `y`，计算它们对应二进制位上不同的位置数量，即汉明距离。

**2、实现思路（详细）**
 逐位比较两个数的二进制表示。

做法：

- 对 0～31 位逐位遍历
- 通过右移并与 `1` 取位值
- 若某一位上 `x` 与 `y` 不相同，则计数加一

由于整数范围固定，遍历 32 位即可，时间复杂度为常数级。

**3、实现代码**

```py
class Solution:
    def hammingDistance(self, x: int, y: int) -> int:
        cnt = 0
        for i in range(32):
            if (x >> i & 1) != (y >> i & 1):
                cnt += 1
        return cnt
```

```go
func hammingDistance(x int, y int) int {
    res := 0
    
    for i := 0; i < 32; i += 1 {
        if (x >> i & 1) != (y >> i & 1) {
            res += 1
        }
    }

    return res
}
```

## (29)[448. 找到所有数组中消失的数字](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/)

**1、题目大意**
 给定长度为 `n` 的数组 `nums`，元素取值在 `[1, n]` 内，找出所有在该范围内但未出现在数组中的数字，要求时间 O(n)，不使用额外空间。

**2、实现思路（详细）**
 利用原数组作为标记空间。

关键观察：

- 数字 `x` 对应索引 `x-1`
- 通过对对应位置加上 `n`，可以标记该数字出现过

处理步骤：

- 遍历数组中的每个元素 `num`
- 取其原始值 `(num-1)%n`，防止被多次加 `n` 后影响索引
- 对索引位置加 `n`，表示该数字出现
- 第二次遍历数组，若某位置的值仍 `<= n`，说明该位置对应的数字从未被标记，即未出现

整个过程只修改原数组，满足常量额外空间要求。

**3、实现代码**

```py
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        n = len(nums)
        for num in nums:
            nums[(num - 1) % n] += n
        return [i + 1 for i, num in enumerate(nums) if num <= n]
```

```go
func findDisappearedNumbers(nums []int) []int {
    n := len(nums)
    res := make([]int, 0)

    for _, num := range(nums) {
        nums[(num - 1) % n] += n 
    }

    for i := 0; i < n ; i += 1 {
        if nums[i] <= n {
            res = append(res, i + 1)
        }
    }

    return res
}
```

## (30)[438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

**1、题目大意**
 给定字符串 `s` 和 `p`，在 `s` 中找出所有长度等于 `p` 且与 `p` 互为字母异位词的子串起始下标。

**2、实现思路**
 使用滑动窗口配合计数哈希。

核心定义：

- 用 `d` 统计 `p` 中每个字符所需的次数
- 用 `cnt` 表示当前窗口中还缺多少个字符才能构成一个异位词

窗口维护：

- 右指针 `i` 向右扩展窗口
  - 若 `s[i]` 在 `d` 中，且该字符仍有需求，则 `cnt--`
  - 对应字符计数减一
- 当窗口长度超过 `p` 长度时，移动左指针 `j` 缩小窗口
  - 若移出的字符在 `d` 中，恢复其计数
  - 若恢复后计数为正，说明缺失字符增加，`cnt++`

判定条件：

- 当 `cnt == 0` 时，说明当前窗口恰好匹配 `p` 的字符组成
- 此时记录左指针 `j` 作为起始索引

整个过程每个指针只移动一次，时间复杂度 O(n)。

**3、实现代码**

```py
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        n, m = len(s), len(p)
        cnt = m
        res = []

        d = Counter(p)
        j = 0
        for i in range(n):
            while i - j + 1 > m:
                if s[j] in d:
                    d[s[j]] += 1
                    if d[s[j]]:
                        cnt += 1
                j += 1

            if s[i] in d:
                if d[s[i]]:
                    cnt -= 1
                d[s[i]] -= 1

            if cnt == 0:
                res.append(j)

        return res
```

```go
func findAnagrams(s string, p string) []int {
	n, m := len(s), len(p)
	cnt := m
	res := []int{}

	d := make(map[byte]int)
	for i := 0; i < m; i++ {
		d[p[i]]++
	}

	j := 0
	for i := 0; i < n; i++ {
		for i-j+1 > m {
			if v, ok := d[s[j]]; ok {
				d[s[j]]++
				if v+1 > 0 {
					cnt++
				}
			}
			j++
		}

		if v, ok := d[s[i]]; ok {
			if v > 0 {
				cnt--
			}
			d[s[i]]--
		}

		if cnt == 0 {
			res = append(res, j)
		}
	}

	return res
}
```

## (31) [437. 路径总和 III](https://leetcode.cn/problems/path-sum-iii/)

**1、题目大意**
 给定一棵二叉树和目标和 `targetSum`，统计所有**向下**的路径中，节点值之和等于 `targetSum` 的路径数量。路径可从任意节点开始、在任意节点结束。

**2、实现思路（详细）**
 使用「前缀和 + DFS 回溯」。

核心定义：

- `cur` 表示从根到当前节点的路径和
- `ps[x]` 表示前缀和为 `x` 出现的次数

关键转化：

- 若存在某个祖先前缀和为 `cur - targetSum`
- 则从该祖先之后到当前节点的路径和等于 `targetSum`

递归过程：

- 进入节点时更新当前前缀和 `cur += r.val`
- 累加 `ps[cur - targetSum]` 到答案
- 将当前前缀和加入哈希表
- 递归左右子树
- 回溯时撤销当前前缀和计数，保证不影响其他路径

初始化 `ps[0] = 1`，用于处理从某节点直接开始的合法路径。
 整体时间复杂度 O(n)。

**3、实现代码**

```py
class Solution:
    def pathSum(self, root: Optional[TreeNode], targetSum: int) -> int:
        ps = defaultdict(int)
        ps[0] += 1

        def dfs(r, cur):
            if not r:
                return 0

            res = 0
            cur += r.val
            res += ps[cur - targetSum]
            ps[cur] += 1
            res += dfs(r.left, cur)
            res += dfs(r.right, cur)
            ps[cur] -= 1

            return res

        return dfs(root, 0)
```

```go
func pathSum(root *TreeNode, targetSum int) int {
	ps := make(map[int]int)
	ps[0] = 1

	var dfs func(*TreeNode, int) int
	dfs = func(r *TreeNode, cur int) int {
		if r == nil {
			return 0
		}

		res := 0
		cur += r.Val
		res += ps[cur-targetSum]

		ps[cur]++
		res += dfs(r.Left, cur)
		res += dfs(r.Right, cur)
		ps[cur]--

		return res
	}

	return dfs(root, 0)
}
```

## (32)[416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

**1、题目大意**
 给定一个只包含正整数的数组，判断是否能将其分成两个子集，使两个子集的元素和相等。

**2、实现思路**
 将问题转化为 0/1 背包判定问题。

关键判断：

- 若数组总和为奇数，必然无法平分
- 设目标和 `t = sum(nums) / 2`
- 若最大元素大于 `t`，也不可能成立

状态定义：

- `f[j]` 表示是否可以选取若干元素，使其和恰好为 `j`

初始化：

- `f[0] = True`，表示不选任何数即可凑成 0

状态转移：

- 依次遍历每个数 `num`
- 金额 `j` 从 `t` 递减到 `num`
- 若 `f[j-num]` 为真，则 `f[j]` 可达

使用倒序遍历确保每个元素只使用一次。
 最终判断 `f[t]` 是否为真。

**3、实现代码**

```py
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        s = sum(nums)
        if s & 1:
            return False
        mx = max(nums)
        t = s // 2
        if mx > t:
            return False

        n = len(nums)
        f = [False] * (t + 1)
        f[0] = True
        for i, num in enumerate(nums):
            for j in range(t, num - 1, -1):
                f[j] |= f[j - num]

        return f[t]

```

```go
func canPartition(nums []int) bool {
	s := 0
	mx := 0
	for _, v := range nums {
		s += v
		if v > mx {
			mx = v
		}
	}

	if s&1 == 1 {
		return false
	}

	t := s / 2
	if mx > t {
		return false
	}

	f := make([]bool, t+1)
	f[0] = true

	for _, num := range nums {
		for j := t; j >= num; j-- {
			if f[j-num] {
				f[j] = true
			}
		}
	}

	return f[t]
}

```

## (33)[406. 根据身高重建队列](https://leetcode.cn/problems/queue-reconstruction-by-height/)

**1、题目大意**
 给定若干人的身高 `hi` 和其前面身高不低于自己的人数 `ki`，要求重建一个队列，使每个人都满足对应的 `(hi, ki)` 约束。

**2、实现思路**
 采用「排序 + 插入」的贪心策略。

核心思路：

- 先处理身高高的人，因为矮个子不会影响高个子的 `ki` 统计
- 身高相同的人，`ki` 小的应排在前面

具体步骤：

- 按身高 `hi` 降序排序，`ki` 升序排序
- 依次遍历排序后的数组
- 将当前人直接插入结果数组下标为 `ki` 的位置
  - 因为此时结果数组中只包含身高 ≥ 当前人的人
  - 插入位置正好保证前面有 `ki` 个身高不低于自己的人

排序保证顺序正确，插入保证局部约束成立，整体约束自然满足。

**3、实现代码**

```py
class Solution:
    def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        people.sort(key=lambda p: (-p[0], p[1]))

        res = []
        for p in people:
            res.insert(p[1], p)

        return res
```

```go
func reconstructQueue(people [][]int) [][]int {
	sort.Slice(people, func(i, j int) bool {
		if people[i][0] != people[j][0] {
			return people[i][0] > people[j][0]
		}
		return people[i][1] < people[j][1]
	})

	res := [][]int{}
	for _, p := range people {
		k := p[1]
		res = append(res, nil)
		copy(res[k+1:], res[k:])
		res[k] = p
	}

	return res
}

```

## (34)[399. 除法求值](https://leetcode.cn/problems/evaluate-division/)

**1、题目大意**
 给定若干除法等式 `Ai / Bi = values[i]`，回答查询 `Cj / Dj` 的结果；若无法由已知等式推出，返回 `-1.0`。

**2、实现思路**
 将等式看作一张**带权有向图**。

建图方式：

- 对于 `a / b = v`，建立
  - 边 `a -> b`，权值 `v`
  - 边 `b -> a`，权值 `1 / v`

查询处理：

- 对每个查询 `(x, y)`，从 `x` 出发进行 DFS
- 在搜索过程中累计路径乘积 `ans`
- 若在搜索中到达 `y`，当前乘积即为答案
- 使用 `vis` 防止重复访问，避免死循环

特殊情况：

- 若 `x` 或 `y` 不在图中，或无法从 `x` 到达 `y`，结果为 `-1.0`

由于变量和等式数量较小，DFS 搜索即可满足要求。

**3、实现代码**

```py
class Solution:
    def calcEquation(
        self, equations: List[List[str]], values: List[float], queries: List[List[str]]
    ) -> List[float]:
        def dfs(cur):
            nonlocal ans, flag
            if cur not in g:
                return
            if cur == y:
                flag = ans
                return
            vis[cur] = True
            for nx, v in g[cur]:
                if not vis[nx]:
                    tmp = ans
                    ans *= v
                    dfs(nx)
                    ans = tmp

        g = defaultdict(list)
        res = []
        for equ, v in zip(equations, values):
            a, b = equ
            g[a].append((b, v))
            g[b].append((a, 1 / v))

        for x, y in queries:
            cur = x
            vis = defaultdict(bool)
            flag = -1.0
            ans = 1.0
            dfs(cur)
            res.append(flag)

        return res

```

```go
func calcEquation(
	equations [][]string,
	values []float64,
	queries [][]string,
) []float64 {

	type Edge struct {
		to  string
		val float64
	}

	g := make(map[string][]Edge)
	for i, eq := range equations {
		a, b := eq[0], eq[1]
		v := values[i]
		g[a] = append(g[a], Edge{b, v})
		g[b] = append(g[b], Edge{a, 1.0 / v})
	}

	res := make([]float64, 0, len(queries))

	for _, q := range queries {
		x, y := q[0], q[1]

		vis := make(map[string]bool)
		ans := 1.0
		flag := -1.0

		var dfs func(string)
		dfs = func(cur string) {
			if _, ok := g[cur]; !ok {
				return
			}
			if cur == y {
				flag = ans
				return
			}
			vis[cur] = true
			for _, e := range g[cur] {
				if !vis[e.to] {
					tmp := ans
					ans *= e.val
					dfs(e.to)
					ans = tmp
				}
			}
		}

		dfs(x)
		res = append(res, flag)
	}

	return res
}
```

## (35)[394. 字符串解码](https://leetcode.cn/problems/decode-string/)

**题目大意**：给定一个经过编码的字符串，其中编码规则为`k[encoded_string]`，表示`encoded_string`中的内容重复k次。要求解码该字符串。

**实现思路**：使用递归来解码字符串。遍历输入字符串，根据不同情况进行处理：

1. 若遇到数字，则累加数字直至遇到非数字字符。
2. 若遇到字母，则直接加入当前解码的字符串中。
3. 若遇到左括号'['，则递归调用解码函数，处理括号内的内容，直至遇到右括号']'。将括号内的解码结果乘以前面累积的数字，并加入当前解码的字符串中。
4. 若遇到右括号']'，则返回当前解码的字符串和当前索引。
5. 返回最终解码结果。

```py
class Solution:
    def decodeString(self, s: str) -> str:
        def decode(i, n):
            cur = ""
            num = 0
            while i < len(s):
                if s[i].isdigit():
                    num = num * 10 + int(s[i])
                elif s[i].isalpha():
                    cur += s[i]
                elif s[i] == "[":
                    ns, ni = decode(i + 1, num)  # next str, next index
                    cur += ns
                    i = ni
                    num = 0
                elif s[i] == "]":
                    return cur * n, i
                i += 1
            return cur * n, i

        res, _ = decode(0, 1)

        return res
```

```go
func decodeString(s string) string {
	var decode func(int, int) (string, int)

	decode = func(i int, n int) (string, int) {
		cur := ""
		num := 0

		for i < len(s) {
			c := s[i]
			if c >= '0' && c <= '9' {
				num = num*10 + int(c-'0')
			} else if c >= 'a' && c <= 'z' || c >= 'A' && c <= 'Z' {
				cur += string(c)
			} else if c == '[' {
				ns, ni := decode(i+1, num)
				cur += ns
				i = ni
				num = 0
			} else if c == ']' {
				res := ""
				for k := 0; k < n; k++ {
					res += cur
				}
				return res, i
			}
			i++
		}

		res := ""
		for k := 0; k < n; k++ {
			res += cur
		}
		return res, i
	}

	res, _ := decode(0, 1)
	return res
}

```

## (36)[347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

**1、题目大意**

统计数组中各元素出现次数，返回出现频率最高的前 k 个不同元素，返回顺序不限，答案唯一。

时间复杂度必须优于 $O(nlogn)$

**2、实现思路**

1. **频率统计**
    使用哈希表统计每个数字在数组中出现的次数，键为数字，值为出现频率。
2. **按频率排序**
    将哈希表转为 `(元素, 频率)` 的键值对列表，根据频率从小到大排序。
3. **截取结果**
    排序后，频率最高的 k 个元素必然位于列表末尾，直接取最后 k 个键作为答案。
4. **返回元素值**
    只保留元素本身，忽略频率。

**3、实现代码**

```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        return [
            k for (k, _) in sorted(list(Counter(nums).items()), key=lambda e: e[1])[-k:]
        ]
```

```go
func topKFrequent(nums []int, k int) []int {
	cnt := make(map[int]int)
	for _, v := range nums {
		cnt[v]++
	}

	type Pair struct {
		val  int
		freq int
	}

	arr := make([]Pair, 0, len(cnt))
	for v, f := range cnt {
		arr = append(arr, Pair{v, f})
	}

	sort.Slice(arr, func(i, j int) bool {
		return arr[i].freq < arr[j].freq
	})

	res := make([]int, 0, k)
	for i := len(arr) - k; i < len(arr); i++ {
		res = append(res, arr[i].val)
	}

	return res
}
```

### 堆

```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        c = Counter(nums)
        h = []

        for key, val in c.items():
            if len(h) < k:
                heappush(h, (val, key))
            else:
                heappush(h, (val, key))
                heappop(h)
        
        res = [key for (_, key) in h]

        return res
```

### 基于快排

```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        c = Counter(nums)
        arr = [(key, val) for key, val in c.items()]

        def qs(l, r, k):
            if l >= r:
                return

            r_idx = random.randint(l, r)
            arr[l], arr[r_idx] = arr[r_idx], arr[l]

            x = arr[l][1]
            p = l

            for i in range(l + 1, r + 1):
                if arr[i][1] >= x:
                    p += 1
                    arr[p], arr[i] = arr[i], arr[p]

            arr[l], arr[p] = arr[p], arr[l]

            cnt = p - l + 1
            if k < cnt:
                qs(l, p - 1, k)
            elif k > cnt:
                qs(p + 1, r, k - cnt)

        qs(0, len(arr) - 1, k)

        return [key for key, _ in arr[:k]]
```

## (37)[338. 比特位计数](https://leetcode.cn/problems/counting-bits/)

**1、题目大意**

对区间 `[0, n]` 内的每个整数，计算其二进制表示中 `1` 的个数，返回长度为 `n+1` 的结果数组。

**2、实现思路**

1. **位运算核心性质**
    对任意正整数 `i`，表达式 `i & (i - 1)` 会将 `i` 的二进制表示中**最低位的 1 消除**。
2. **状态转移关系**
    若 `i` 的最低位 1 被消除后得到 `j = i & (i - 1)`，
    则 `i` 中 `1` 的个数 = `j` 中 `1` 的个数 + 1。
3. **动态规划构建结果**
   - 定义 `res[i]` 表示整数 `i` 的二进制中 `1` 的数量
   - 初始状态：`res[0] = 0`
   - 递推公式：`res[i] = res[i & (i - 1)] + 1`
4. **线性扫描**
    从 `1` 遍历到 `n`，利用已计算的小状态直接得到当前结果，整体时间复杂度 `O(n)`，空间复杂度 `O(n)`。

**3、实现代码**

```py
class Solution:
    def countBits(self, n: int) -> List[int]:
        res = [0]
        for i in range(1, n + 1):
            res.append(res[i & (i - 1)] + 1)
        return res
```

```go
func countBits(n int) []int {
	res := make([]int, n+1)
	for i := 1; i <= n; i++ {
		res[i] = res[i&(i-1)] + 1
	}
	return res
}
```

## (38)[337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)

**1、题目大意**

给定一棵二叉树，每个节点表示一间房屋及其金额。相邻（父子）节点不能同时被选择，求在不触发警报的情况下可盗取的最大金额。

**2、实现思路**

1. **树形动态规划建模**
    对每个节点定义两种状态：
   - `f[node]`：选择当前节点时，子树能获得的最大金额
   - `s[node]`：不选择当前节点时，子树能获得的最大金额
2. **状态转移**
   - 若选择当前节点，则其左右子节点都不能选：
      `f[node] = node.val + s[left] + s[right]`
   - 若不选择当前节点，则左右子节点可选可不选，取最大值：
      `s[node] = max(f[left], s[left]) + max(f[right], s[right])`
3. **后序遍历计算**
    使用 DFS 后序遍历，确保子节点状态已计算，再计算当前节点状态。
4. **最终答案**
    根节点可选可不选，返回 `max(f[root], s[root])`。

时间复杂度 `O(n)`，空间复杂度 `O(n)`。

3、实现代码

```py
class Solution:
    def rob(self, root: Optional[TreeNode]) -> int:
        f = defaultdict(int)
        s = defaultdict(int)

        def dfs(node):
            if not node:
                return
            dfs(node.left)
            dfs(node.right)
            f[node] = node.val + s[node.left] + s[node.right]
            s[node] = max(f[node.left], s[node.left]) + max(f[node.right], s[node.right])

        dfs(root)
        return max(f[root], s[root])

```

```go
func rob(root *TreeNode) int {
	f := make(map[*TreeNode]int)
	s := make(map[*TreeNode]int)

	var dfs func(*TreeNode)
	dfs = func(node *TreeNode) {
		if node == nil {
			return
		}
		dfs(node.Left)
		dfs(node.Right)
		f[node] = node.Val + s[node.Left] + s[node.Right]
		s[node] = max(f[node.Left], s[node.Left]) + max(f[node.Right], s[node.Right])
	}

	dfs(root)
	return max(f[root], s[root])
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

```

## (39)[121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

**1、题目大意**

给定股票每日价格数组，只允许一次买入和一次卖出（先买后卖），求能获得的最大利润；若无利润则返回 0。

**2、实现思路**

1. **一次遍历建模**
    在遍历价格数组时，同时维护两个量：
   - `mn`：到当前天为止的最低买入价格
   - `res`：当前能获得的最大利润
2. **核心逻辑**
    对于当前价格 `p`：
   - 若今天卖出，利润为 `p - mn`，用它更新最大利润
   - 更新最低价格 `mn = min(mn, p)`，为后续卖出做准备
3. **正确性保证**
   - 买入一定发生在卖出之前（`mn` 来自历史价格）
   - 遍历过程中已穷尽所有合法买卖组合

时间复杂度 `O(n)`，空间复杂度 `O(1)`。

**3、实现代码**

```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        res, mn = 0, inf

        for p in prices:
            res = max(res, p - mn)
            mn = min(p, mn)

        return res
```

```go
func maxProfit(prices []int) int {
	res := 0
	mn := math.MaxInt64

	for _, p := range prices {
		if p-mn > res {
			res = p - mn
		}
		if p < mn {
			mn = p
		}
	}

	return res
}

```

## (40)[312. 戳气球](https://leetcode.cn/problems/burst-balloons/)

- **题意**：给定n个数字`num[0~n-1]`,每次戳破一个气球可以获得`nums[i-1]*nums[i]*nums[i+1]`个硬币，最两旁默认为1，问最终获得的最大硬币数量。
- **思路**：区间DP，逆向思维, 思考如何放气球能使硬币最大，`f[i][j]`代表**开区间**`i~j`可以获得的最大硬币， 先枚举小区间，再枚举大区间，枚举区间内每个数字，状态转移有：`f[i][j] = max(f[i][j], f[i][k]+f[k][j]+nums[i]*nums[k]*nums[j])`。最终输出`f[0][n+1]`

```py
class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        n = len(nums)
        nums = [1] + nums + [1]
        f = [[0] * (n + 5) for _ in range(n + 5)]

        # 开区间 (left, right) 区间内能获得的最大硬币
        for l in range(2, n + 2):  # len:2~n+1
            for i in range(n + 1 - l + 1):  # left:0~n-len+1
                j = i + l  # right = left + len
                for k in range(i + 1, j):  # 开区间(i,j) 即[i+1, j-1]
                    f[i][j] = max(
                        f[i][j], f[i][k] + f[k][j] + nums[i] * nums[k] * nums[j]
                    )

        return f[0][n + 1]

```

```py
class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        nums = [1] + nums + [1]
        n = len(nums)
        f = [[0] * n for _ in range(n)]

        for l in range(2, n):
            for left in range(n - l):
                right = left + l 
                for middle in range(left + 1, right):
                    f[left][right] = max(f[left][right], f[left][middle] + f[middle][right] + nums[left] * nums[middle] * nums[right])
                
        return f[0][n - 1]
```

```go
func maxCoins(nums []int) int {
	n := len(nums)
	arr := make([]int, n+2)
	arr[0], arr[n+1] = 1, 1
	for i := 0; i < n; i++ {
		arr[i+1] = nums[i]
	}

	f := make([][]int, n+5)
	for i := 0; i < n+5; i++ {
		f[i] = make([]int, n+5)
	}

	// 开区间 (i, j) 内能获得的最大硬币
	for l := 2; l <= n+1; l++ { // len:2~n+1
		for i := 0; i <= n+1-l; i++ { // left:0~n+1-len
			j := i + l
			for k := i + 1; k < j; k++ { // 开区间 (i, j)
				f[i][j] = max(f[i][j], f[i][k]+f[k][j]+arr[i]*arr[k]*arr[j])
			}
		}
	}

	return f[0][n+1]
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```
