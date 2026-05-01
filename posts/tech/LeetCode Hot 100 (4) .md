---
title : 'LeetCode Hot 100 精练🥸(4)'
date : 2024-12-08T10:00:01+08:00
lastmod: 2025-12-21T10:00:01+08:00
description : "LeetCode Hot 100 精练🥸(4)" 
categories : ["LeetCode"]
tags : ["LeetCode Hot 100"]
---

# LeetCode Hot 100 精练🥸(4)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。

## (41) [309. 买卖股票的最佳时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

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

```py
class Solution:
    def removeInvalidParentheses(self, s: str) -> List[str]:
        res = []

        def check(st):
            cnt = 0
            for c in st:
                if c == "(":
                    cnt += 1
                elif c == ")":
                    if cnt == 0:
                        return False
                    cnt -= 1
            return cnt == 0

        cur = set([s])
        while True:
            for st in cur:
                if check(st):
                    res.append(st)
            if len(res):
                break
            nxt = set()
            for st in cur:
                for i in range(len(st)):
                    if i > 0 and st[i] == st[i - 1]:
                        continue
                    if st[i] == "(" or st[i] == ")":
                        nxt.add(st[:i] + st[i + 1 :])
            cur = nxt

        return res

```

## (43)[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

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

```py
class Codec:
    def serialize(self, root):
        if not root:
            return "#"
        return (
            str(root.val)
            + " "
            + self.serialize(root.left)
            + " "
            + self.serialize(root.right)
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



## (47)[279. 完全平方数](https://leetcode.cn/problems/perfect-squares/)

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



## (49)[240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

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



## (53)[48. 旋转图像](https://leetcode.cn/problems/rotate-image/)



## (54)[46. 全排列](https://leetcode.cn/problems/permutations/)



## (55)[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)



## (56)[39. 组合总和](https://leetcode.cn/problems/combination-sum/)



## (57)[543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)



## (58)[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)



## (59)[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)



## (60)[32. 最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)





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



## [11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

**题目大意**：给定一个长度为n的整数数组`height`，数组中的每个元素代表一条垂直线的高度。找出其中的两条线，使得它们与x轴构成的容器可以容纳最多的水。

**实现思路**：使用双指针法。初始化左指针l指向数组的起始位置，右指针r指向数组的末尾位置。设置变量`ret`用于记录当前最大容量，初始化为`0`。在每一轮循环中，计算当前容器的容量，即`min(height[l], height[r])`乘以r和l之间的距离，更新`ret`。然后根据指针所指向的高度的大小，移动指针，如果`height[l]<height[r]`，则移动左指针l向右一步，否则移动右指针r向左一步。直到左右指针相遇，循环结束，返回ret即可。

- 正确性证明： 对于左右端点 $ l, r$， 两点之间的距离为$len = r-l+1-1$(因为两个点之间算一段，所以长度要减一)，$ ret = (r-l)*min(height[l], height[r])$,假设 $height[l]<height[r]$, 对于左端点，其作为柱子能容纳的最多的水的数量已经为最大值，任意的 $l<x<r$ 作为右端点一定比$r$做端点更差，所以可以排除左端点$l$，计算下一点即$l+1$。从左右端点开始，对于每次排除的点，已经找到这个端点可能的最大值， 所以可以保证结果的正确性。

```py
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l, r = 0, len(height) - 1
        res = 0
        
        while l < r:
            res = max(res, (r - l) * min(height[l], height[r]))
            if height[l] < height[r]:
                l += 1
            elif height[l] > height[r]:
                r -= 1
            else:
                l += 1
                r -= 1

        return res

```

## [15. 三数之和](https://leetcode.cn/problems/3sum/)

**题目大意**：给定一个整数数组 `nums`，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k`，同时还满足 `nums[i] + nums[j] + nums[k] == 0`。返回所有满足条件的不重复的三元组。

### 枚举

**实现思路**：首先对数组 `nums` 进行排序。然后遍历数组，对于每个元素 `nums[i]`，设定两个指针 `l` 和 `r` 分别指向 `i+1` 和数组末尾。在 `l` 和 `r` 之间寻找和为 `0` 的两个数。具体地，如果当前元素与前一个元素相同，跳过；如果当前元素与 `l+1` 处的元素相同，跳过；在 `l` 和 r 之间利用双指针的方式找到满足条件的两个数，如果找到了满足条件的三元组，则添加到结果中。最后返回结果列表 `res`。

```py
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        nums.sort()
        res = []
        for i in range(n):
            l = i 
            r = n-1
            if i and nums[i]==nums[i-1]:
                continue
            for m in range(l+1, n):
                if m>l+1 and nums[m]==nums[m-1]:
                    continue
                while r>m and nums[r]+nums[m]+nums[l]>0:
                    r-=1
                if m==r: break
                if nums[r]+nums[m]+nums[l]==0:
                    res.append([nums[l], nums[m], nums[r]])
        return res
```

### 计数排序双指针

**实现思路**：首先，利用 `Counter` 函数统计每个数出现的次数，并检查是否有 0 出现至少三次，如果是则将 `[0, 0, 0]` 添加到结果中。然后，对不重复的数进行排序。遍历排序后的数，对于每个数 num，如果 num 不等于 0 且 num 出现次数大于 1 且 `-num*2` 也在 Counter 中，则将 `[num, num, -num*2]` 添加到结果中。然后，在负数部分，利用双指针的方式寻找满足条件的两个数。最后返回结果列表 `res`。

```py
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        res = []
        c = Counter(nums)
        if 0 in c and c[0] >= 3:
            res.append([0,0,0])
        no_repeat_nums = sorted(c.keys())
        
        for i,num in enumerate(no_repeat_nums):
            if num != 0 and c[num] > 1 and -num*2 in c:
                res.append([num,num,-num*2])
            if num < 0:
                for num3 in no_repeat_nums[ bisect_left(no_repeat_nums,((-num+1)/2)) : bisect_right(no_repeat_nums,-num*2-1) ]:
                    num2 = -num-num3
                    if num2 in c:
                        res.append([num,num2,num3])
        return res
```

## [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

**题目大意**：给定一个链表，要求删除倒数第n个节点，并返回链表的头结点。

**实现思路**：使用双指针，首先让第一个指针从头节点开始向后移动n步，然后同时移动第一个指针和第二个指针，直到第一个指针到达链表末尾。这样第二个指针所指的位置就是倒数第n个节点的前一个节点，然后进行删除操作即可。需要注意的是要考虑边界情况，比如链表长度为1，删除头节点等情况。

```py
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        if not head.next:
            return None

        p1 = head
        n -= 1
        while n:
            p1 = p1.next
            n -= 1

        pre = p2 = head
        while p1.next:
            p1 = p1.next
            pre = p2
            p2 = p2.next

        if p2 == head:
            head = head.next
        else:
            pre.next = pre.next.next

        return head

```

## [31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

**题目大意**：给定一个整数数组，要求找出这个数组的下一个排列，即比当前排列大的下一个排列，如果不存在则返回字典序最小的排列。

**实现思路**：要找到下一个排列，可以遵循以下步骤：

1. 从数组末尾开始，找到第一个相邻的两个数，满足 `nums[i]` < `nums[i+1]`。
2. 如果找到了这样的一对数，说明当前排列还不是最大的排列，可以进行下一步操作。
3. 在从右往左找到的第一个位置记为 i，再从数组末尾开始，找到第一个大于 `nums[i]` 的数，记为 `j`。
4. 交换 `nums[i]` 和 `nums[j]`。
5. 将从 `i+1` 位置开始到数组末尾的数逆序排列，以得到字典序最小的排列。
6. 如果步骤1中没有找到相邻的两个数，则说明当前排列已经是最大的排列，直接将整个数组逆序排列即可。

```py
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        n = len(nums)
        i = n - 2
        while i >= 0 and nums[i] >= nums[i + 1]:
            i -= 1

        if i >= 0:
            j = n - 1
            while j >= i and nums[j] <= nums[i]:
                j -= 1
            nums[i], nums[j] = nums[j], nums[i]

        nums[i + 1 :] = reversed(nums[i + 1 :])
```

## [33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

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


## [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

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







## [301. 删除无效的括号](https://leetcode.cn/problems/remove-invalid-parentheses/)

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
        ret = []
        
        def check(st):
            cnt=0
            for c in st:
                if c=='(':
                    cnt+=1
                elif c==')':
                    if cnt==0:
                        return False
                    cnt-=1
            return cnt==0
        
        cur = set([s])
        
        while True:
            for st in cur:
                if check(st):
                    ret.append(st)
            if len(ret):
                break
            nxt = set()
            for st in cur:
                for i in range(len(st)):
                    if i>0 and st[i]==st[i-1]:
                        continue
                    if st[i]=='(' or st[i]==')':
                        nxt.add(st[:i]+st[i+1:])
            cur = nxt
        return ret
```

## [297. 二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)

不止第一次遇到了。

- **题目大意**：序列化是将一个数据结构或对象转换为连续的比特位的操作，从而可以存储在文件或内存中，并且通过网络传输到另一个计算机环境中。本题要求设计一个算法来实现二叉树的序列化和反序列化，即将二叉树转换为字符串并将字符串转换回原始的二叉树结构。

- **实现思路**：

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

## [84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

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

## [85. 最大矩形](https://leetcode.cn/problems/maximal-rectangle/)

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

## [301. 删除无效的括号](https://leetcode.cn/problems/remove-invalid-parentheses/)

- **题目大意**：给定一个由字母和括号组成的字符串s，要求删除最小数量的括号，使得字符串成为有效的括号组合，并返回所有可能的结果。


- **实现思路**：
  1. 首先定义一个辅助函数`check(st)`，用于检查字符串st是否为有效的括号组合。遍历字符串中的每个字符，维护一个计数器cnt，遇到左括号增加计数，遇到右括号减少计数，若出现cnt为负数，或者遍历结束后cnt不为0，则说明括号不匹配，返回False，否则返回True。

  2. 初始化一个空列表ret，用于存储结果。

  3. 初始化一个集合cur，初始时将输入字符串s作为唯一元素加入其中。

  4. 使用while循环，直到找到符合条件的结果为止：

     - 遍历集合cur中的每个字符串，检查其是否为有效括号组合，若是则将其加入结果列表ret。

     - 若结果列表ret不为空，则说明已找到符合条件的结果，结束循环。

     - 否则，初始化一个空集合nxt，用于存储下一轮迭代的候选字符串集合。

     - 遍历集合cur中的每个字符串，对于每个字符串，尝试删除一个字符（括号），生成新的字符串，并将其加入nxt中。

     - 更新cur为nxt，继续下一轮迭代。
  5. 返回结果列表ret。

```py
class Solution:
    def removeInvalidParentheses(self, s: str) -> List[str]:
        ret = []
        def check(st):
            cnt=0
            for c in st:
                if c=='(':
                    cnt+=1
                elif c==')':
                    if cnt==0:
                        return False
                    cnt-=1
            return cnt==0
        cur = set([s])
        while True:
            for st in cur:
                if check(st):
                    ret.append(st)
            if len(ret):
                break
            nxt = set()
            for st in cur:
                for i in range(len(st)):
                    if i>0 and st[i]==st[i-1]:
                        continue
                    if st[i]=='(' or st[i]==')':
                        nxt.add(st[:i]+st[i+1:])
            cur = nxt
        return ret
```


## [647. 回文子串](https://leetcode.cn/problems/palindromic-substrings/)

题目大意：给定一个字符串s，统计并返回该字符串中回文子串的数目。回文字符串是指正着读和倒过来读一样的字符串。子字符串是字符串中的由连续字符组成的一个序列。即使是由相同字符组成的不同开始位置或结束位置的子串，也会被视作不同的子串。

实现思路：遍历字符串s的所有可能的中心位置，对于每个中心位置，向两边扩展，判断是否是回文串。在扩展的过程中，每当发现一个回文子串，就将计数器加1。最终返回计数器的值即可。

```py
class Solution:
    def countSubstrings(self, s: str) -> int:
        n = len(s)
        ans = 0
        for i in range(2*n - 1):
            l, r = i//2, i//2 + (i&1)
            while l>=0 and r<n and s[l]==s[r]:
                l-=1; r+=1
                ans+=1
        return ans
```

## [93. 复原 IP 地址](https://leetcode.cn/problems/restore-ip-addresses/)

**题目大意**：给定一个只包含数字的字符串s，表示一个IP地址，要求返回所有可能的有效IP地址，即每个IP地址由四个整数组成（每个整数位于`0`到`255`之间，且不能含有前导0），整数之间用`'.'`分隔。不能重新排序或删除s中的任何数字，可以按任何顺序返回答案。

**实现思路**：使用深度优先搜索`（DFS）`算法，递归地搜索所有可能的IP地址组合。在搜索过程中，首先确定每个整数的范围，然后遍历可能的数字组合，逐步构建`IP`地址。递归的终止条件是已经找到了四个整数并且已经遍历完了整个字符串s。

```py
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        n = len(s)
        res, addr = [], ['0']*4

        def dfs(i, start):
            if i==4 or start==n:
                if i==4 and start==n:
                    res.append('.'.join(addr))
                return 
            
            if s[start]=='0':
                addr[i] = '0'
                dfs(i+1, start+1)
                return 
            
            num = 0
            for end in range(start, n):
                num = num*10 + int(s[end])
                if num in range(256):
                    addr[i] = str(num)
                    dfs(i+1, end+1)

        dfs(0, 0)           
        return res
```

## [538. 把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

**题目大意**：给定一个二叉搜索树的根节点，需要将其转换为累加树，即每个节点的新值等于原树中大于或等于该节点值的节点值之和。

**实现思路**：

### 递归解法

```py
class Solution:
    def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        s = 0
        def dfs(r):
            nonlocal s
            if not r:
                return 0
            dfs(r.right)
            s += r.val
            r.val = s 
            dfs(r.left)
        dfs(root)
        return root
```

### **(Morris 遍历)**

从根节点开始，采用反向中序遍历（右-根-左）的方式进行遍历。利用一个变量 s 记录累加和，初始值为 0。对于每个节点，首先判断其是否存在右子节点，如果不存在，则将其值加上累加和并更新累加和，然后将当前节点指向其左子节点；如果存在右子节点，则找到其中序遍历的后继节点，即右子树中最左边的节点。如果后继节点的左子节点为空，说明还未处理过该节点，则将后继节点的左子节点指向当前节点，并将当前节点指向其右子节点；如果后继节点的左子节点为当前节点，则说明已经处理过该节点，则将后继节点的左子节点置为空，将当前节点的值加上累加和并更新累加和，并将当前节点指向其左子节点。最后返回根节点。

```py
class Solution:
    def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        def getSucc(node):
            succ = node.right
            while succ.left and succ.left!=node:
                succ = succ.left
            return succ

        newRoot = root
        s = 0
        while root:
            if not root.right:
                s += root.val
                root.val = s 
                root = root.left
            else:
                succ = getSucc(root)
                if not succ.left:
                    succ.left = root
                    root = root.right
                else:
                    succ.left = None
                    s += root.val
                    root.val = s 
                    root = root.left
        return newRoot
```

## [416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

**题目大意**：给定一个非空数组 `nums`，数组中只包含正整数。要求判断是否能将该数组分割成两个子集，使得这两个子集的元素和相等。

**实现思路**：

1. 首先计算数组 `nums` 的总和 s。
2. 如果总和 `s` 为奇数，那么无法分割成两个和相等的子集，直接返回 `False`。
3. 如果数组中的最大值大于总和的一半，则无法分割成两个和相等的子集，直接返回 `False`。
4. 初始化一个大小为总和一半加一的布尔数组 `f`，`f[i]` 表示是否存在子集的和为 `i`。
5. 将 `f[0]` 初始化为 `True`，表示子集的和为 `0`。
6. 遍历数组 `nums`，对于每个正整数 `num`，从总和一半开始向前遍历，更新数组 `f`，如果 `f[j-num]` 为 `True`，则说明存在一个子集的和为 `j-num`，加上当前的 `num` 后，和为 `j`，因此 `f[j]` 也为 `True`。
7. 最终返回 `f[s//2]`，表示是否存在一个子集的和为总和一半，即是否能分割成两个和相等的子集。

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





## [72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

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

## [96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

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


## [148. 排序链表](https://leetcode.cn/problems/sort-list/)

**题目大意**：给定一个链表的头结点`head`，要求将链表按升序排列，并返回排序后的链表。

**实现思路**：

1. 使用归并排序的思想对链表进行排序。
2. 编写递归函数`sortFun(head, tail)`，其中`head`表示当前待排序的子链表的头结点，`tail`表示当前待排序的子链表的尾结点的下一个结点（即尾结点的后继结点）。
3. 在`sortFun`函数中，使用快慢指针找到当前待排序子链表的中间结点`mid`，并将链表分为两部分，左边部分由`head`到`mid-1`，右边部分由`mid`到`tail-1`。
4. 递归调用`sortFun`函数对左右两部分进行排序，直至排序完成。
5. 编写`merge`函数，将已经排好序的左右两部分链表进行合并，合并过程中按照结点的值大小进行比较，将较小的结点连接到结果链表中。
6. 返回合并后的链表。

```py
class Solution:
    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def sortFun(head, tail):
            if not head:
                return head
            if head.next == tail:
                head.next = None
                return head
            slow = fast = head

            while fast != tail:
                fast = fast.next
                slow = slow.next
                if fast != tail:
                    fast = fast.next
            
            mid = slow
            return merge(sortFun(head, mid), sortFun(mid, tail))
        def merge(h1, h2):
            dummy = ListNode(-1)
            cur = dummy
            while h1 and h2:
                if h1.val<=h2.val:
                    cur.next = h1
                    h1 = h1.next
                else:
                    cur.next = h2
                    h2 = h2.next
                cur = cur.next
            if h1:
                cur.next = h1
            if h2:
                cur.next = h2
            
            return dummy.next
        return sortFun(head, None)
```

## [581. 最短无序连续子数组](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/)

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

## [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

### **动态规划法**（O(n) 时间复杂度）：

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

