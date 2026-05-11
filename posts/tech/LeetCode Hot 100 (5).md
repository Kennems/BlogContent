---
title : 'LeetCode Hot 100 精练🥸(5)'
date : 2024-12-08T10:00:01+08:00
lastmod: 2025-12-21T10:00:01+08:00
description : "LeetCode Hot 100 精练🥸(5)" 
categories : ["LeetCode"]
tags : ["LeetCode Hot 100"]
---

# LeetCode Hot 100 精练🥸(5)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。



##   (81)[104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

给定一个二叉树 `root` ，返回其最大深度。

二叉树的 **最大深度** 是指从根节点到最远叶子节点的最长路径上的节点数。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
            
        return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1

```



##   (82)[102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        q = deque()
        q.append((root, 0))
        res = []
        
        while q:
            f, l = q.popleft()  # front, level
            if not f:
                continue
            if l >= len(res):
                res.append([])
            res[l].append(f.val)
            if f.left:
                q.append((f.left, l + 1))
            if f.right:
                q.append((f.right, l + 1))

        return res
```



##   (83)[101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

给你一个二叉树的根节点 `root` ， 检查它是否轴对称。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        def check(r1, r2):
            if not r1 and not r2:
                return True
            if not r1 or not r2 or r1.val != r2.val:
                return False
            return check(r1.left, r2.right) and check(r1.right, r2.left)

        return check(root.left, root.right)

```



## (84)[98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

给你一个二叉树的根节点 `root` ，判断其是否是一个有效的二叉搜索树。

**有效** 二叉搜索树定义如下：

- 节点的左子树只包含 **严格小于** 当前节点的数。
- 节点的右子树只包含 **严格大于** 当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    pre = -inf

    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if not root:
            return True
        if not self.isValidBST(root.left) or root.val <= self.pre:
            return False
        self.pre = root.val

        return self.isValidBST(root.right)

```



## (85)[96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

### 题目大意：

给定一个整数 `n`，要求计算由 `n` 个节点组成的二叉搜索树（BST）的数量，节点的值从 1 到 `n` 且互不相同。返回满足条件的二叉搜索树的种数。

### 解题思路：

1. **二叉搜索树的性质**：
   - 对于二叉搜索树，任意节点的左子树中的值都小于该节点的值，右子树中的值都大于该节点的值。
   - 给定一个根节点 `i`（范围从 1 到 `n`），其左子树的节点值来自 `[1, i-1]`，右子树的节点值来自 `[i+1, n]`。
   - 通过递归计算左子树和右子树的不同构造方式，可以得到当前根节点 `i` 时，整个二叉搜索树的数量。
2. **动态规划**：
   - 用 `f[i]` 表示 `i` 个节点可以组成的不同二叉搜索树的数量。
   - 初始条件：`f[0] = 1`，`f[1] = 1`，这分别表示0个节点和1个节点的二叉搜索树只有1种（空树和单个节点）。
     - 左子树的节点数为 `j-1`，右子树的节点数为 `i-j`。
     - 其中，`f[j-1]` 表示左子树有 `j-1` 个节点时的二叉搜索树种数，`f[i-j]` 表示右子树有 `i-j` 个节点时的二叉搜索树种数。
3. **算法步骤**：
   - 使用动态规划数组 `f` 来存储从 `0` 到 `n` 个节点的不同二叉搜索树的数量。
   - 通过递推关系来填充数组 `f`。
   - 最终 `f[n]` 就是所求的结果。

- `f[i] += f[j - 1] * f[i - j]` 表示当前根节点 `j` 时，左子树和右子树的种数乘积，累加到 `f[i]`。

```py
class Solution:
    def numTrees(self, n: int) -> int:
        f = [0]*(n+1)
        f[0] = 1; f[1] = 1
        for i in range(2, n+1):
            for j in range(1, i+1):
                f[i] += f[i-j]*f[j-1]
        return f[n]
```


## 

## (86)94. 二叉树的中序遍历

## (87)[85. 最大矩形](https://leetcode.cn/problems/maximal-rectangle/)

- **题目大意**：给定一个仅包含 0 和 1、大小为 rows x cols 的二维二进制矩阵，找出只包含 1 的最大矩形，并返回其面积。

- **实现思路**：

1. 将问题转化为矩形最大面积问题。
2. 定义变量 area 用于记录最大矩形的面积。
3. 创建二维数组 left，用于记录每个位置 (i, j) 左侧连续 1 的个数。
4. 遍历二维矩阵，初始化 left 数组：
   - 若当前位置为 '1'，则 left[i][j] 等于 left[i][j-1] + 1，否则为 0。
5. 遍历矩阵的每一列，对于每一列 j，使用单调栈来计算以当前列为底边的最大矩形的面积：
   - 初始化一个空栈 stk。
   - 定义两个数组 up 和 down，分别记录当前位置上方第一个小于等于其高度的位置和下方第一个小于等于其高度的位置。
   - 遍历矩阵的每一行 i，进行以下操作：
     - 当栈不为空且栈顶位置对应的 left 值大于等于当前位置的 left 值时，弹出栈顶位置，并更新 down 值。
     - 如果栈为空，则当前位置的 up 值为 -1，否则为栈顶位置。
     - 将当前行索引入栈。
   - 在计算当前列的矩形面积时，height 为 down[i] - up[i] - 1，宽度为 left[i][j]，计算当前列的最大面积并更新 area。
6. 返回最大面积 area。

```py
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        # 转化为矩形最大面积问题
        n, m = len(matrix), len(matrix[0])
        area = 0
        left = [[0]*m for _ in range(n)]
        
        for i in range(n):
            for j in range(m):
                if matrix[i][j]=='1':
                    left[i][j] += left[i][j-1]+1 if j else 1
                    
        for j in range(m):
            stk = []
            up, down = [-1]*n, [n]*n
            for i in range(n): #行
                while stk and left[ stk[-1] ][j]>=left[i][j]:
                    down[stk[-1]] = i
                    stk.pop()
                up[i] = stk[-1] if stk else -1
                stk.append(i)

            for i in range(n):
                height = down[i]-up[i]-1
                area = max(area, height*left[i][j])
        return area
```


## 

## (88)[84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

- **题目大意**：给定 n 个非负整数，表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1。求在该柱状图中，能够勾勒出的矩形的最大面积。

- **实现思路**：

1. 使用单调栈解决问题。创建两个数组 l 和 r，分别记录每个柱子向左和向右第一个比其高度小的柱子的索引位置。
2. 初始化一个空栈 stk。
3. 遍历柱子的高度列表 heights：
   - 若栈不为空且当前柱子的高度小于栈顶柱子的高度，则将栈顶元素弹出，并更新栈顶元素对应的 r 值为当前柱子的索引。
   - 若栈为空或者当前柱子的高度大于栈顶柱子的高度，则将当前柱子的索引入栈。
   - 在更新 r 值的同时，若栈不为空，则更新当前柱子的 l 值为栈顶元素的索引。
4. 遍历完成后，对于每个柱子 i，计算以该柱子为高度的矩形面积为 (r[i] - l[i] - 1) * heights[i]，取最大值即为所求的最大矩形面积。
5. 若柱状图为空，则返回 0。

```py
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        # 单调栈，找到每个高度能组成的最大矩形
        # 往左右两面找到第一个小于 自身高度的柱子，那么这两个柱子之间的柱子一定可以达到自身的高度，所以是自身高度可以组成的最大面积
        n = len(heights)
        l, r = [-1]*n, [n]*n 
        res = 0

        stk = []
        for i in range(n):
            h = heights[i]
            while stk and heights[stk[-1]] >= h:
                r[stk[-1]] = i
                stk.pop()
            if stk:
                l[i] = stk[-1]
            stk.append(i)

        # stk = []
        # for i in range(n-1, -1, -1):
        #     h = heights[i]
        #     while stk and heights[stk[-1]] >= h:
        #         stk.pop()
        #     if stk:
        #         r[i] = stk[-1]
        #     stk.append(i)
        
        for i in range(n):
            if heights[i] * (r[i]-l[i]-1) > res:
                res = heights[i] * (r[i]-l[i]-1)
        
        return res
```



##   (89)[1. 两数之和](https://leetcode.cn/problems/two-sum/)

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案，并且你不能使用两次相同的元素。

你可以按任意顺序返回答案。

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        d = dict()
        for i, nm in enumerate(nums):
            rest = target - nm
            if rest in d:
                return (d[rest], i)
            d[nm] = i

```



## (90)[78. 子集](https://leetcode.cn/problems/subsets/)

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        res = [[]]
        st = []

        def dfs(i):
            if i == n:
                if st:
                    res.append(st[:])
                return
            st.append(nums[i])
            dfs(i + 1)
            st.pop()
            dfs(i + 1)

        dfs(0)
        return res

```





## (91)[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

**题目大意**：给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**思路**：双指针，为了保证对于每一个右端点，都能找到最优的左端点，对所有的已经遍历的字符都要计数`-1`， 这样在每一个符合条件的右端点判断每个字符是否有多余，这样确保左端点最优。

```py
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        m, n = len(s), len(t)
        needCnt = n
        need = Counter(t)

        res = (0, m)

        left = 0
        for right, ch in enumerate(s):
            if need[ch] > 0:
                needCnt -= 1
            need[ch] -= 1 # 关键点

            if needCnt == 0:
                while need[s[left]]!=0: # 保证对于每个符合的右端点，都可以找到最优的左端点
                    need[s[left]] += 1
                    left += 1
                if right - left + 1 < res[1] - res[0] + 1:
                    res = (left, right)
                needCnt += 1
                need[s[left]] += 1
                left += 1
                
        return s[res[0]:res[1]+1] if res[1] < m else ""
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

## (92)[75. 颜色分类](https://leetcode.cn/problems/sort-colors/)

给定一个包含红色、白色和蓝色、共 `n` 个元素的数组 `nums` ，**[原地](https://baike.baidu.com/item/原地算法)** 对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 `0`、 `1` 和 `2` 分别表示红色、白色和蓝色。

必须在不使用库内置的 sort 函数的情况下解决这个问题。

```py
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        def qs(q, l, r):
            if l >= r:
                return
            i, j, k = l - 1, r + 1, q[l + r >> 1]
            while i < j:
                i += 1
                while q[i] < k:
                    i += 1
                j -= 1
                while q[j] > k:
                    j -= 1
                if i < j:
                    q[i], q[j] = q[j], q[i]
            qs(q, l, j)
            qs(q, j + 1, r)

        qs(nums, 0, len(nums) - 1)

```



## (93)[72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

**题目大意**：
给定两个单词 word1 和 word2，求通过插入、删除或替换字符，将 word1 转换成 word2 所需的最少操作数。

#### 记忆化搜索

这段代码使用了递归的方式求解，其中 dfs 函数表示将 s 的前 i 个字符转换为 t 的前 j 个字符所需的最少操作数。递归的基本情况是当 i 小于 0 时，表示 s 已经遍历完，需要插入 t 的前 j+1 个字符；当 j 小于 0 时，表示 t 已经遍历完，需要删除 s 的前 i+1 个字符；当 s[i] 等于 t[j] 时，不需要额外操作，直接递归处理 i-1 和 j-1；当 s[i] 不等于 t[j] 时，可以选择插入、删除或替换操作，选择操作数最小的方案。递归的过程中利用了缓存装饰器 `@cache` 来提高效率。

```py
class Solution:
    def minDistance(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        
        @cache
        def dfs(i, j):
            if i < 0:
                return j + 1
            if j < 0:
                return i + 1
            if s[i] == t[j]:
                return dfs(i - 1, j - 1)
            return min(dfs(i - 1, j), dfs(i, j - 1), dfs(i - 1, j - 1)) + 1

        return dfs(n - 1, m - 1)
```

#### 递推

**实现思路**：
可以使用动态规划来解决这个问题。定义一个二维数组 f，其中 f[i][j] 表示将 word1 的前 i 个字符转换为 word2 的前 j 个字符所需的最少操作数。初始时，f[i][0] 表示将 word1 的前 i 个字符全部删除，需要的操作数为 i，而 `f[0][j]` 表示将 word2 的前 j 个字符全部插入到 word1 中，需要的操作数为 j。
然后，根据动态规划的状态转移方程，逐步计算 f[i][j] 的值。若 word1[i] 等于 word2[j]，则 f[i][j] 等于 f[i-1][j-1]，即不需要额外操作；若不相等，则可以考虑插入、删除或替换操作，选择操作数最小的方案。最终，返回 f[n][m]，其中 n 和 m 分别为 word1 和 word2 的长度。

```py
class Solution:
    def minDistance(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        f = [[0] * (m + 1) for _ in range(n + 1)]
        f[0] = list(range(m + 1))  # f[0][j] = j
        for i in range(n):
            f[i + 1][0] = i + 1
            for j in range(m):
                if s[i] == t[j]:
                    f[i + 1][j + 1] = f[i][j]
                else:
                    f[i + 1][j + 1] = min(f[i][j + 1], f[i + 1][j], f[i][j]) + 1

        return f[n][m]

```

## (94)[70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。

每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

```py
class Solution:
    def climbStairs(self, n: int) -> int:
        f = [0] * (n + 1)
        f[0] = 1
        f[1] = 1

        for i in range(2, n + 1):
            f[i] = f[i - 1] + f[i - 2]
        
        return f[n]
```



##   (95)[581. 最短无序连续子数组](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/)

**题目大意**：给定一个整数数组`nums`，找出一个连续子数组，使得对该子数组进行升序排序后，整个数组都变为升序排序。要求找出符合条件的最短子数组，并输出其长度。

**实现思路**：

1. 首先初始化最大值`mx`为负无穷，最小值`mn`为正无穷，以及最短子数组的左右边界`left`和`right`分别为`-1`。
2. 遍历数组`nums`，从左向右寻找右边界`right`，如果当前元素小于前面的最大值`mx`，则更新`right`为当前索引；否则更新最大值`mx`为当前元素。
3. 同时，从右向左寻找左边界`left`，如果当前元素大于后面的最小值`mn`，则更新`left`为当前索引；否则更新最小值`mn`为当前元素。
4. 最后返回右边界和左边界的差加`1`，即为最短子数组的长度。如果左边界仍为初始值`-1`，则返回`0`表示整个数组已经有序。

```py
class Solution:
    def findUnsortedSubarray(self, nums: List[int]) -> int:
        n = len(nums)
        mx, right = -inf, -1
        mn, left = inf, -1

        for i in range(n):
            if mx > nums[i]:
                right = i 
            else:
                mx = nums[i]
            
            if mn < nums[n-1-i]:
                left = n-1-i 
            else:
                mn = nums[n-1-i]

        return 0 if left == -1 else right-left+1
```

##   (96)[64. 最小路径和](https://leetcode.cn/problems/minimum-path-sum/)

给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

```py
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        f = [[inf] * (n + 1) for _ in range(m + 1)]
        f[1][0] = f[0][1] = 0

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                f[i][j] = min(f[i - 1][j], f[i][j - 1]) + grid[i - 1][j - 1]

        return f[m][n]
```

##  

## (97)[62. 不同路径](https://leetcode.cn/problems/unique-paths/)

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

```py
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        f = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if i == 1 and j == 1:
                    f[i][j] = 1
                else:
                    f[i][j] = f[i][j - 1] + f[i - 1][j]

        return f[m][n]

```



## (98)[56. 合并区间](https://leetcode.cn/problems/merge-intervals/)

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。

```py
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort()
        res = []

        st, ed = -inf, -inf

        for s, e in intervals:
            if s > ed:
                if st != -inf:
                    res.append([st, ed])

                st, ed = s, e
            else:
                ed = max(ed, e)

        if st != -inf:
            res.append([st, ed])

        return res

```



## (99)[55. 跳跃游戏](https://leetcode.cn/problems/jump-game/)

给你一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标，如果可以，返回 `true` ；否则，返回 `false` 。

```py
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        n = len(nums)

        ed = 0
        for i, x in enumerate(nums):
            if ed >= i:
                ed = max(ed, i + x)
            else:
                break

        return ed >= n - 1

```



## (100)[53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

### **动态规划法**O(n) ：

动态规划的思路是利用之前计算出的结果来优化当前的计算，从而避免重复计算。我们通过维护两个变量来完成该问题：

- `cur`: 当前子数组的和，表示包含当前元素的最大子数组和。
- `res`: 当前已知的最大子数组和。

**具体步骤**：

1. 初始化 `res` 为一个非常小的值（比如负无穷），用来记录当前的最大子数组和。

2. 使用变量 `cur` 来存储当前子数组的和。

3. 遍历数组中的每个元素，更新 `cur`

   的值：

   - 如果 `cur + num` 小于 `num`，则说明当前子数组不再有价值，应该从当前 `num` 重新开始计算子数组的和。
   - 更新 `cur` 为 `max(num, cur + num)`。

4. 每次更新 `cur` 后，更新 `res` 为 `max(res, cur)`，确保 `res` 始终记录着最大子数组和。

```py
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        res = -inf
        cur = 0

        for nm in nums:
            cur = max(nm, cur + nm)
            res = max(res, cur)
            
        return res
```

### **分治法**

将数组递归地分为两部分，分别求解左右部分的最大子数组和，并计算跨越中点的最大子数组和。

**状态类 `Status`**：用来存储子数组的信息，包括：

- `lSum`：左侧子数组的最大和。
- `rSum`：右侧子数组的最大和。
- `mSum`：整个子数组的最大和。
- `iSum`：子数组的总和。

**递归**：

- 基本情况：数组只有一个元素时，返回该元素的 `Status`。
- 递归分解：将数组分为左右两部分，递归计算每部分的最大子数组和。

**合并**：使用 `pushUp` 函数合并左右子数组的结果，计算跨越中点的最大子数组和，并返回合并后的结果。

```py
class Solution:
    # 状态类，用来存储子数组的信息
    class Status:
        def __init__(self, lSum, rSum, mSum, iSum):
            # lSum: 左侧部分的最大子数组和
            # rSum: 右侧部分的最大子数组和
            # mSum: 当前子数组的最大子数组和
            # iSum: 当前子数组的总和
            self.lSum = lSum 
            self.rSum = rSum
            self.mSum = mSum
            self.iSum = iSum

    # 主函数：求解整个数组的最大子数组和
    def maxSubArray(self, nums):
        # 调用递归函数，获取整个数组的最大子数组和
        return self.getInfo(nums, 0, len(nums) - 1).mSum

    # 递归函数：计算子数组 [l, r] 的最大子数组和
    def getInfo(self, nums, l, r):
        # 基本情况：当数组只包含一个元素时
        if l == r:
            # 返回这个元素的状态，lSum, rSum, mSum, iSum 都是这个元素的值
            return self.Status(nums[l], nums[l], nums[l], nums[l])
        # 计算中点
        mid = (l + r) // 2
        # 递归计算左半部分的最大子数组和
        lSub = self.getInfo(nums, l, mid)
        # 递归计算右半部分的最大子数组和
        rSub = self.getInfo(nums, mid + 1, r)
        # 合并左半部分和右半部分的结果
        return self.pushUp(lSub, rSub)

    # 合并函数：合并左右子数组的结果，计算跨越中点的最大子数组和
    def pushUp(self, l, r):
        # 计算当前子数组的总和（左半部分的总和 + 右半部分的总和）
        iSum = l.iSum + r.iSum
        # 计算左半部分的最大和，可能跨越右边界，所以选择 lSum 或 l.iSum + r.lSum
        lSum = max(l.lSum, l.iSum + r.lSum)
        # 计算右半部分的最大和，可能跨越左边界，所以选择 rSum 或 r.iSum + l.rSum
        rSum = max(r.rSum, r.iSum + l.rSum)
        # 计算当前子数组的最大和，比较左子数组的最大和，右子数组的最大和，和跨越中点的最大和
        mSum = max(max(l.mSum, r.mSum), l.rSum + r.lSum)
        # 返回合并后的结果，构造一个新的 Status 对象
        return self.Status(lSum, rSum, mSum, iSum)
```
