---
title : 'LeetCode Hot 100 精练🥸(4)'
date : 2024-12-08T10:00:01+08:00
lastmod: 2026-05-11T10:00:01+08:00
description : "LeetCode Hot 100 精练🥸(4)" 
categories : ["LeetCode"]
tags : ["LeetCode Hot 100"]
---

# LeetCode Hot 100 精练🥸(4)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。

## (61)[31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

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

##  (62)[538. 把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

**题目大意**：给定一个二叉搜索树的根节点，需要将其转换为累加树，即每个节点的新值等于原树中大于或等于该节点值的节点值之和。

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

## (63)[23. 合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/) 

**题目大意**：
给定 k 个升序链表，将它们合并为一个新的升序链表并返回。

**解题思路**：
使用最小堆（优先队列）：将每个链表头节点入堆，每次弹出堆顶接到结果链表尾部，若弹出节点有后继则将其入堆，重复直到堆空。

```py
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class pq:
    def __init__(self, val, node):
        self.val = val
        self.node = node

    def __lt__(self, other):
        return self.val < other.val


class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        h = []
        n = len(lists)

        for i in range(n):
            head = lists[i]
            if head:
                heappush(h, pq(head.val, head))
        newHead = ListNode(-1)
        dummy = newHead
        while h:
            top = heappop(h)
            cur = top.node
            dummy.next = cur
            dummy = cur
            
            if cur.next:
                heappush(h, pq(cur.next.val, cur.next))

        return newHead.next

```



##   (64)[560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。

子数组是数组中元素的连续非空序列。

**题目大意**：
统计数组中和为 k 的连续子数组的个数。

**解题思路**：
前缀和 + 哈希表。遍历数组时维护前缀和 pre，检查 pre - k 是否在哈希表中出现过（即存在子数组和为 k），将其出现次数累加到结果中。

```py
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        d = defaultdict(int)
        d[0] += 1
        n, pre, res = len(nums), 0, 0

        for i in range(n):
            pre += nums[i]
            if d[pre - k]:
                res += d[pre - k]
            d[pre] += 1

        return res
```



##   (65)[21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**题目大意**：
合并两个升序链表为一个新的升序链表。

**解题思路**：
双指针 + 虚拟头节点。比较两个链表当前节点值，将较小者接入结果链表尾部，直到其中一个链表遍历完毕，再将剩余部分直接拼接。

```py
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(
        self, l1: Optional[ListNode], l2: Optional[ListNode]
    ) -> Optional[ListNode]:
        dummy = ListNode(-1)
        cur = dummy

        while l1 and l2:
            if l1.val < l2.val:
                cur.next = l1
                cur = cur.next
                l1 = l1.next
            else:
                cur.next = l2
                cur = cur.next
                l2 = l2.next

        while l1:
            cur.next = l1
            cur = cur.next
            l1 = l1.next
        while l2:
            cur.next = l2
            cur = cur.next
            l2 = l2.next

        return dummy.next
```

 

##       

##   (66[20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

**题目大意**：
判断给定括号字符串是否有效（括号类型匹配且顺序正确）。

**解题思路**：
使用栈。遇到左括号入栈，遇到右括号时检查栈顶是否匹配对应左括号，匹配则弹栈，否则返回 false；遍历结束后栈空则有效。

```py
class Solution:
    def isValid(self, s: str) -> bool:
        stk = []
        for i, ch in enumerate(s):
            if ch in "([{":
                stk.append(ch)
                continue

            if not stk:
                return False

            if ch == ")" and stk[-1] == "(":
                stk.pop()
                continue

            if ch == "]" and stk[-1] == "[":
                stk.pop()
                continue

            if ch == "}" and stk[-1] == "{":
                stk.pop()
                continue

            return False

        return len(stk) == 0
```



##          



##   (67)[19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

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

##   (68)[17. 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](https://pic.leetcode.cn/1752723054-mfIHZs-image.png)     

**题目大意**：
给定数字字符串，返回所有可能的字母组合映射。

**解题思路**：
DFS 回溯。递归遍历每个数字，枚举当前数字对应的所有字母，加入路径后继续深入，到达末尾时保存结果并回溯。

```py
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        # 自救者人能救之
        n = len(digits)
        if not n: 
            return []
        s = []
        res = []

        def dfs(i):
            if i==n:
                res.append(''.join(s))
                return 

            beg = 3 * ( int(digits[i]) - 2) + 1 \
                    + (1 if digits[i]=='8' or digits[i]=='9' else 0) 
            
            lim = 3
            if digits[i]=="7" or digits[i]=="9": lim = 4

            for j in range(beg, beg+lim):
                s.append(chr(j-1 + ord('a')))
                dfs(i+1)
                s.pop()

        dfs(0)
        
        return res
```



##   (69)[15. 三数之和](https://leetcode.cn/problems/3sum/)

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

## (70)[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

**题目大意**：给定一个长度为n的整数数组`height`，数组中的每个元素代表一条垂直线的高度。找出其中的两条线，使得它们与x轴构成的容器可以容纳最多的水。

**实现思路**：使用双指针法。初始化左指针l指向数组的起始位置，右指针r指向数组的末尾位置。设置变量`ret`用于记录当前最大容量，初始化为`0`。在每一轮循环中，计算当前容器的容量，即`min(height[l], height[r])`乘以r和l之间的距离，更新`ret`。然后根据指针所指向的高度的大小，移动指针，如果`height[l]<height[r]`，则移动左指针l向右一步，否则移动右指针r向左一步。直到左右指针相遇，循环结束，返回ret即可。

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



## (71)[10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)

给你一个字符串 `s` 和一个字符规律 `p`，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。

- `'.'` 匹配任意单个字符
- `'*'` 匹配零个或多个前面的那一个元素

返回一个布尔值，表示匹配是否覆盖整个输入字符串（而非部分）。

**题目大意**：
实现支持 '.' 和 '*' 的正则表达式匹配，判断 s 是否能被 p 完整匹配。

**解题思路**：
动态规划。定义 f[i][j] 表示 s 的前 i 个字符与 p 的前 j 个字符是否匹配，分字符匹配、'.' 匹配、'*' 匹配三种情况转移。

```py
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)
        f = [[False] * (n + 1) for _ in range(m + 1)]
        f[0][0] = True
        s, p = "#" + s, "#" + p

        for i in range(1, n + 1):
            if p[i] == "*":
                f[0][i] = f[0][i - 2]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if s[i] == p[j] or p[j] == ".":
                    f[i][j] = f[i - 1][j - 1]
                elif p[j] == "*":
                    if s[i] == p[j - 1] or p[j - 1] == ".":
                        f[i][j] = f[i - 1][j]
                    f[i][j] |= f[i][j - 2]

        return f[m][n]

```

​      





##   (72)[5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)    

给你一个字符串 `s`，找到 `s` 中最长的 回文 子串。

**题目大意**：
找出给定字符串中的最长回文子串。

**解题思路**：
马拉车算法（Manacher）。通过插入分隔符统一处理奇偶长度回文，维护当前最右回文中心 c 和右边界 r，利用对称性加速回文半径计算。

```py
class Solution:
    def longestPalindrome(self, s: str) -> str:
        s = "#" + "#".join(s) + "#"
        n = len(s)
        p = [0] * n
        c, r = 0, 0

        # 马拉车
        for i in range(n):
            if i <= r:
                p[i] = min(r - i, p[c - (i - c)])
            while i + p[i] + 1 < n and s[i + p[i] + 1] == s[i - p[i] - 1]:
                p[i] += 1
            if i + p[i] > r:
                c, r = i, i + p[i]

        mxl = max(p)
        idx = p.index(mxl)

        return s[idx - mxl + 1 : idx + mxl : 2]
```



##   (73)[4. 寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

**题目大意**：
在两个正序数组中找到中位数，要求 O(log(m+n)) 时间复杂度。

**解题思路**：
二分法（基于较小数组划分）。在较小数组上二分查找合适的分割点，使得左半部分全部小于等于右半部分，根据两数组长度和的奇偶性返回中位数。

```py
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        def get(k):
            idx1, idx2 = 0, 0
            while True:
                if idx1 == m:
                    return nums2[idx2 + k - 1]
                if idx2 == n:
                    return nums1[idx1 + k - 1]
                if k == 1:
                    return min(nums1[idx1], nums2[idx2])

                newIdx1, newIdx2 = min(m - 1, idx1 + k // 2 - 1), min(
                    n - 1, idx2 + k // 2 - 1
                )
                v1, v2 = nums1[newIdx1], nums2[newIdx2]
                if v1 <= v2:
                    k -= newIdx1 - idx1 + 1
                    idx1 = newIdx1 + 1
                else:
                    k -= newIdx2 - idx2 + 1
                    idx2 = newIdx2 + 1

        m, n = len(nums1), len(nums2)
        l = m + n

        if l & 1:
            return get(l // 2 + 1)
        else:
            return (get(l // 2) + get(l // 2 + 1)) / 2

```

```py
class Solution:
    def findMedianSortedArrays(self, nums1, nums2):
        """
        Finds the median of two sorted arrays.

        Args:
            nums1 (list[int]): The first sorted array.
            nums2 (list[int]): The second sorted array.

        Returns:
            float: The median of the two sorted arrays.
        """
        # Ensure nums1 is the smaller array to optimize binary search.
        # This allows us to minimize the number of elements we process.
        if len(nums1) > len(nums2):
            nums1, nums2 = nums2, nums1

        # Lengths of the two arrays
        x, y = len(nums1), len(nums2)

        # Initialize binary search boundaries
        low, high = 0, x

        while low <= high:
            # Partition the smaller array
            partitionX = (low + high) // 2
            # Partition the larger array to complement the smaller array
            partitionY = (x + y + 1) // 2 - partitionX

            # Handle edge cases for the left and right partitions of nums1
            maxX = float("-inf") if partitionX == 0 else nums1[partitionX - 1]
            minX = float("inf") if partitionX == x else nums1[partitionX]

            # Handle edge cases for the left and right partitions of nums2
            maxY = float("-inf") if partitionY == 0 else nums2[partitionY - 1]
            minY = float("inf") if partitionY == y else nums2[partitionY]

            # Check if the partitions are valid
            if maxX <= minY and maxY <= minX:
                # If the total number of elements is even, the median is the average
                # of the maximum of left partitions and the minimum of right partitions.
                if (x + y) % 2 == 0:
                    return (max(maxX, maxY) + min(minX, minY)) / 2.0
                else:
                    # If odd, the median is the maximum of the left partitions.
                    return float(max(maxX, maxY))
            elif maxX > minY:
                # If maxX is too large, move the partition in nums1 to the left.
                high = partitionX - 1
            else:
                # If maxY is too large, move the partition in nums1 to the right.
                low = partitionX + 1

        # If the input arrays are not sorted or invalid, raise an error.
        raise None
```



##   (74)[3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长 子串** 的长度。

**题目大意**：
找出不含重复字符的最长子串的长度。

**解题思路**：
滑动窗口 + 哈希表。双指针维护窗口 [j, i]，哈希表统计窗口内字符频率，当出现重复时移动左边界 j 直到没有重复，每步更新最大长度。

```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        d = defaultdict(int)
        n = len(s)
        j = 0

        res = 0
        for i, ch in enumerate(s):
            d[ch] += 1
            while d[ch] > 1:
                d[s[j]] -= 1
                j += 1
            res = max(res, i - j + 1)

        return res

```



##   (75)[2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**题目大意**：
两个逆序链表表示的非负整数相加，返回和链表。

**解题思路**：
遍历两个链表，逐位相加并处理进位。使用虚拟头节点简化链表构建，每次计算 val1 + val2 + carry，生成新节点接到结果链表尾部。

```py
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        dummy = ListNode()
        curr = dummy
        carry = 0

        while l1 or l2:
            val1 = l1.val if l1 else 0
            val2 = l2.val if l2 else 0

            total = val1 + val2 + carry
            carry = total // 10
            curr.next = ListNode(total % 10)
            curr = curr.next

            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None

        if carry:
            curr.next = ListNode(carry)

        return dummy.next
```

​       



##   (76)[79. 单词搜索](https://leetcode.cn/problems/word-search/)

给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中”相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。  

**题目大意**：
判断给定单词是否存在于二维字符网格中（相邻匹配且不重复使用）。

**解题思路**：
DFS 回溯。遍历网格每个位置作为起点，深度优先搜索匹配单词的每个字符，用 visited 数组标记已访问单元格，搜索完毕恢复状态（回溯）。

```py
class Solution:
    def exist(self, grid: List[List[str]], target: str) -> bool:
        m, n = len(grid), len(grid[0])

        dirs = [(-1, 0), (0, 1), (1, 0), (0, -1)]

        def dfs(x, y, i):
            if i == len(target):
                return True

            vis[x][y] = True

            for dx, dy in dirs:
                xx, yy = x + dx, y + dy
                if 0 <= xx < m and 0 <= yy < n and not vis[xx][yy]:
                    if grid[xx][yy] == target[i]:
                        if dfs(xx, yy, i + 1):
                            vis[x][y] = False
                            return True

            vis[x][y] = False
            return False

        vis = [[False] * n for _ in range(m)]

        for a in range(m):
            for b in range(n):
                if grid[a][b] == target[0]:
                    if dfs(a, b, 1):
                        return True

        return False

```



##   (77)[114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

给你二叉树的根结点 `root` ，请你将它展开为一个单链表：

- 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
- 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

**题目大意**：
将二叉树原地展开为先序遍历顺序的单链表（右指针指向后继，左指针置空）。

**解题思路**：
先序遍历 + 指针重连。遍历二叉树（根-左-右），维护一个 cur 指针指向当前链表末尾，依次将访问到的节点连接到 cur.right 并将 left 置空。

```py
class Solution:
    def flatten(self, root: Optional[TreeNode]) -> None:
        def inorder(r):
            nonlocal cur
            if not r:
                return

            left = r.left
            right = r.right

            cur.right = r
            r.left = None
            cur = cur.right

            inorder(left)
            inorder(right)

        cur = TreeNode(-1)
        inorder(root)
```

​      



##   (78)[621. 任务调度器](https://leetcode.cn/problems/task-scheduler/)

给你一个用字符数组 `tasks` 表示的 CPU 需要执行的任务列表，用字母 A 到 Z 表示，以及一个冷却时间 `n`。每个周期或时间间隔允许完成一项任务。任务可以按任何顺序完成，但有一个限制：两个 **相同种类** 的任务之间必须有长度为 `n` 的冷却时间。

返回完成所有任务所需要的 **最短时间间隔** 。

**题目大意**：
安排任务执行顺序，相同任务间需间隔 n 个时间单位，求完成所有任务的最短时间。

**解题思路**：
贪心模拟。每次选择当前可执行（冷却已结束）且剩余数量最多的任务执行，更新其下次可执行时间和剩余次数，直至所有任务完成。

```py
class Solution:
    def leastInterval(self, tasks: List[str], n: int) -> int:
        c = Counter(tasks)

        m = len(c)
        nxt = [1] * m
        cnt = list(c.values())

        t = 0
        for i in range(len(tasks)):
            t += 1
            minNxt = min(nxt[j] for j in range(m) if cnt[j])
            t = max(t, minNxt)

            nxtTask = -1
            for j in range(m):
                if cnt[j] and nxt[j] <= t:
                    if nxtTask == -1 or cnt[nxtTask] < cnt[j]:
                        nxtTask = j
            nxt[nxtTask] = t + n + 1
            cnt[nxtTask] -= 1

        return t
```

​     

## (79)[617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/)

给你两棵二叉树： `root1` 和 `root2` 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，**不为** null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

**注意:** 合并过程必须从两个树的根节点开始。

**题目大意**：
合并两棵二叉树，重叠节点值相加，非重叠节点保留非空节点。

**解题思路**：
DFS 递归。若一个节点为空则返回另一个，否则将两节点值相加，然后递归合并左右子树。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return root2
        if not root2:
            return root1
        
        root1.val += root2.val
        root1.left = self.mergeTrees(root1.left, root2.left)
        root1.right = self.mergeTrees(root1.right, root2.right)

        return root1
```



##   (80)	[105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

给定两个整数数组 `preorder` 和 `inorder` ，其中 `preorder` 是二叉树的**先序遍历**， `inorder` 是同一棵树的**中序遍历**，请构造二叉树并返回其根节点。

**题目大意**：
根据前序遍历和中序遍历序列构造唯一的二叉树。

**解题思路**：
递归构造。前序首元素为根节点，在中序中找到该元素位置 idx，左边为左子树，右边为右子树，递归构造左右子树并连接。

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder:
            return None

        idx = inorder.index(preorder[0])
        r = TreeNode(preorder[0])
        r.left = self.buildTree(preorder[1 : idx + 1], inorder[:idx])
        r.right = self.buildTree(preorder[idx + 1 :], inorder[idx + 1 :])

        return r

```

