---
title : 'LeetCode Hot 100 精练🥸(1)'
date : 2024-04-17T10:00:01+08:00
lastmod: 2025-12-20T10:00:01+08:00
description : "LeetCode Hot 100 精练🥸(1)" 
categories : ["LeetCode"]
tags : ["LeetCode Hot 100"]
---

# LeetCode Hot 100 精练🥸(1)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。

## [题目表](https://leetcode.cn/problem-list/2cktkvj/)

|(序号)|标题|
|---- | ---- |
|(1)|160. 相交链表|
|(2)|236. 二叉树的最近公共祖先|
|(3)|234. 回文链表|
|(4)|739. 每日温度|
|(5)|226. 翻转二叉树|
|(6)|221. 最大正方形|
|(7)|215. 数组中的第K个最大元素|
|(8)|208. 实现 Trie (前缀树)|
|(9)|207. 课程表|
|(10)|206. 反转链表|
|(11)|200. 岛屿数量|
|(12)|198. 打家劫舍|
|(13)|169. 多数元素|
|(14)|238. 除自身以外数组的乘积|
|(15)|155. 最小栈|
|(16)|152. 乘积最大子数组|
|(17)|148. 排序链表|
|(18)|146. LRU 缓存|
|(19)|142. 环形链表 II|
|(20)|141. 环形链表|
|(21)|139. 单词拆分|
|(22)|136. 只出现一次的数字|
|(23)|647. 回文子串|
|(24)|128. 最长连续序列|
|(25)|124. 二叉树中的最大路径和|
|(26)|322. 零钱兑换|
|(27)|494. 目标和|
|(28)|461. 汉明距离|
|(29)|448. 找到所有数组中消失的数字|
|(30)|438. 找到字符串中所有字母异位词|
|(31)|437. 路径总和 III|
|(32)|416. 分割等和子集|
|(33)|406. 根据身高重建队列|
|(34)|399. 除法求值|
|(35)|394. 字符串解码|
|(36)|347. 前 K 个高频元素|
|(37)|338. 比特位计数|
|(38)|337. 打家劫舍 III|
|(39)|121. 买卖股票的最佳时机|
|(40)|312. 戳气球|
|(41)|309. 买卖股票的最佳时机含冷冻期|
|(42)|301. 删除无效的括号|
|(43)|300. 最长递增子序列|
|(44)|297. 二叉树的序列化与反序列化|
|(45)|287. 寻找重复数|
|(46)|283. 移动零|
|(47)|279. 完全平方数|
|(48)|253. 会议室 II|
|(49)|240. 搜索二维矩阵 II|
|(50)|239. 滑动窗口最大值|
|(51)|22. 括号生成|
|(52)|49. 字母异位词分组|
|(53)|48. 旋转图像|
|(54)|46. 全排列|
|(55)|42. 接雨水|
|(56)|39. 组合总和|
|(57)|543. 二叉树的直径|
|(58)|34. 在排序数组中查找元素的第一个和最后一个位置|
|(59)|33. 搜索旋转排序数组|
|(60)|32. 最长有效括号|
|(61)|31. 下一个排列|
|(62)|538. 把二叉搜索树转换为累加树|
|(63)|23. 合并 K 个升序链表|
|(64)|560. 和为 K 的子数组|
|(65)|21. 合并两个有序链表|
|(66)|20. 有效的括号|
|(67)|19. 删除链表的倒数第 N 个结点|
|(68)|17. 电话号码的字母组合|
|(69)|15. 三数之和|
|(70)|11. 盛最多水的容器|
|(71)|10. 正则表达式匹配|
|(72)|5. 最长回文子串|
|(73)|4. 寻找两个正序数组的中位数|
|(74)|3. 无重复字符的最长子串|
|(75)|2. 两数相加|
|(76)|79. 单词搜索|
|(77)|114. 二叉树展开为链表|
|(78)|621. 任务调度器|
|(79)|617. 合并二叉树|
|(80)|105. 从前序与中序遍历序列构造二叉树|
|(81)|104. 二叉树的最大深度|
|(82)|102. 二叉树的层序遍历|
|(83)|101. 对称二叉树|
|(84)|98. 验证二叉搜索树|
|(85)|96. 不同的二叉搜索树|
|(86)|94. 二叉树的中序遍历|
|(87)|85. 最大矩形|
|(88)|84. 柱状图中最大的矩形|
|(89)|1. 两数之和|
|(90)|78. 子集|
|(91)|76. 最小覆盖子串|
|(92)|75. 颜色分类|
|(93)|72. 编辑距离|
|(94)|70. 爬楼梯|
|(95)|581. 最短无序连续子数组|
|(96)|64. 最小路径和|
|(97)|62. 不同路径|
|(98)|56. 合并区间|
|(99)|55. 跳跃游戏|
|(100)|53. 最大子数组和|

## (1)[160. 相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

**题目大意**：

给定两个单链表的头节点 `headA` 和 `headB`，要求找出并返回这两个链表相交的第一个节点。如果两个链表不相交，返回 `null`

**解题思路**:

初始化两个指针 `l1` 和 `l2`，分别指向 `headA` 和 `headB`。

让两个指针交替遍历两个链表：当一个指针到达链表尾部时，重新指向另一个链表的头部。这样，两个指针会同步遍历剩余部分，且相交的节点会在相同的步数内被找到。

如果两个链表相交，最终两个指针会相遇在相交的节点。如果没有交点，两个指针都会变为 `null`。

```py
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
        l1, l2 = headA, headB
        while l1 != l2:
            # 当 l1 到达尾部时，重新指向 headB；当 l2 到达尾部时，重新指向 headA
            l1 = headB if not l1 else l1.next
            l2 = headA if not l2 else l2.next
        return l1
```

```go
func getIntersectionNode(headA, headB *ListNode) *ListNode {
    l1, l2 := headA, headB
    for l1 != l2 {
        if l1 == nil {
            l1 = headB
        } else {
            l1 = l1.Next
        }

        if l2 == nil {
            l2 = headA
        } else {
            l2 = l2.Next
        }
    }
    return l1
}
```

## (2)[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

1. **题目大意**
    给定一个二叉树，找到该树中两个指定节点的最近公共祖先。
    最近公共祖先的定义为：对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。
2. **实现思路**
    采用递归方法来实现。基本思路如下：
   - 如果当前节点为空，或者当前节点就是 p 或 q，返回当前节点。
   - 递归遍历左子树和右子树，找到 p 和 q 的公共祖先。
   - 如果左子树和右子树都找到节点，说明当前节点就是公共祖先。
   - 如果只有左子树或右子树找到节点，说明公共祖先在该子树中，返回找到的节点。
3. **实现代码**

```py
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root or root == q or root == p:
            return root
        
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)

        if left and right:
            return root
        
        if left or right:
            return left if left else right
```

```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
	if root == nil || root == p || root == q {
		return root
	}

	left := lowestCommonAncestor(root.Left, p, q)
	right := lowestCommonAncestor(root.Right, p, q)

	if left != nil && right != nil {
		return root
	}

	if left != nil {
		return left
	}
	return right
}
```

## (3)[234. 回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

1、**题目大意**
 给定一个单链表的头节点 `head`，判断该链表是否是回文链表。如果是回文链表，返回 `true`，否则返回 `false`。

2、**实现思路**

- 使用递归来检查链表是否为回文。在递归过程中，我们从链表的末尾开始向前遍历，并同时检查链表的前半部分。
- 通过非局部变量 `front` 来跟踪链表的头部，每次递归返回时，检查当前节点值是否与 `front` 节点值相同。
- 当递归到链表末尾时，`front` 会逐渐移动到链表的下一个节点。

3、**实现代码**

```py
class Solution:
    def isPalindrome(self, head: Optional[ListNode]) -> bool:
        front = head

        def recursive(cur):
            nonlocal front
            if cur:
                if not recursive(cur.next):
                    return False
                if front.val != cur.val:
                    return False
                front = front.next
            return True

        return recursive(head)
```

```go
func isPalindrome(head *ListNode) bool {
    front := head

    var recursive func(cur *ListNode) bool
    recursive = func(cur *ListNode) bool {
        if cur == nil {
            return true
        }
        if !recursive(cur.Next) {
            return false
        }
        if front.Val != cur.Val {
            return false
        }
        front = front.Next
        return true
    }

    return recursive(head)
}
```

## (4)[739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

**1、题目大意**
 给定一个每日气温数组 `temperatures`，求每一天距离下一个更高温度的天数。如果之后没有更高温度，则为 0。

**2、实现思路**

- 使用单调递减栈存储下标，从右往左遍历数组。
- 对于当前温度 `temperatures[i]`，栈顶小于等于它的元素弹出，确保栈保持严格递减。
- 栈不为空时，栈顶下标减当前下标即为答案；否则为 0。
- 最后将当前下标压入栈中。

**3、实现代码**

```py
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        stk = []
        n = len(temperatures)
        res = [0] * n

        for i in range(n - 1, -1, -1):
            while stk and temperatures[stk[-1]] <= temperatures[i]:
                stk.pop()

            if stk:
                res[i] = stk[-1] - i

            stk.append(i)

        return res
```

```go
func dailyTemperatures(temperatures []int) []int {
    n := len(temperatures)
    stk := make([]int, 0)
    res := make([]int, n)

    for i := n - 1; i >= 0; i -= 1 {
        for len(stk) > 0 && temperatures[stk[len(stk) - 1]] <= temperatures[i] {
            stk = stk[:len(stk) - 1]
        }
        if len(stk) > 0 {
            res[i] = stk[len(stk) - 1] - i 
        }
        stk = append(stk, i)
    }
    
    return res
}
```

## (5)[226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

**1、题目大意**
 给定一棵二叉树，翻转它的左右子树，返回翻转后的根节点。

**2、实现思路**

- 使用递归：如果当前节点为空，直接返回。
- 交换当前节点的左右子节点。
- 递归翻转左子树和右子树。
- 最终返回根节点。

**3、实现代码**

```py
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return root
        
        root.left, root.right = root.right, root.left

        self.invertTree(root.left)
        self.invertTree(root.right)

        return root
```

```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return root
    }

    root.Left, root.Right = root.Right, root.Left

    invertTree(root.Left)
    invertTree(root.Right)

    return root
}
```

## (6) [221. 最大正方形](https://leetcode.cn/problems/maximal-square/)

**1、题目大意**
 给定一个由 `'0'` 和 `'1'` 组成的二维矩阵，找到只包含 `'1'` 的最大正方形，并返回其面积。

**2、实现思路**

- 使用动态规划，定义 `f[i][j]` 为以 `(i-1, j-1)` 为右下角的最大正方形边长。
- 状态转移：如果 `matrix[i][j] == '1'`，则 `f[i+1][j+1] = min(f[i][j+1], f[i+1][j], f[i][j]) + 1`，否则为 0。
- 遍历矩阵同时维护最大边长 `res`，最终返回 `res * res`。

**3、实现代码**

```

```

```py
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        m, n = len(matrix), len(matrix[0])

        f = [[0] * (n + 1) for _ in range(m + 1)]
        res = 0

        for i in range(m):
            for j in range(n):
                if matrix[i][j] == "1":
                    f[i + 1][j + 1] = min(f[i][j + 1], f[i + 1][j], f[i][j]) + 1
                    if f[i + 1][j + 1] > res:
                        res = f[i + 1][j + 1]

        return res * res

```

```go
func maximalSquare(matrix [][]byte) int {
	m, n := len(matrix), len(matrix[0])

	f := make([][]int, m+1)
	for i := 0; i <= m; i += 1 {
		f[i] = make([]int, n+1)
	}

	res := 0

	for i := 0; i < m; i += 1 {
		for j := 0; j < n; j += 1 {
			if matrix[i][j] == '1' {
				f[i+1][j+1] = min(f[i][j+1], f[i+1][j], f[i][j]) + 1
				if f[i+1][j+1] > res {
					res = f[i+1][j+1]
				}
			}
		}
	}

	return res * res
}

func min(a, b, c int) int {
	if a < b {
		if a < c {
			return a
		}
	}
	if b < c {
		return b
	}
	return c
}
```

## (7)[215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

### 题目大意：

给定一个整数数组 `nums` 和一个整数 `k`，要求返回数组中排序后的第 `k` 个最大的元素。你必须设计一个时间复杂度为 O(n) 的算法来解决此问题。

### 思路分析：

1. **快速选择算法**：
   - 该题的核心思想是利用快速排序的思想，通过**分治**的方式来找到第 `k` 个最大元素。传统的快速排序时间复杂度是 O(n log n)，但通过改造快速排序成**快速选择算法**，我们可以将时间复杂度降低到 O(n)，从而满足题目的要求。
2. **算法步骤**：
   - **选择枢纽元素**：选择数组中的一个元素作为枢纽（通常是数组的中间元素）。
   - **分区**：将数组分成两个部分：一部分所有元素都大于等于枢纽，另一部分所有元素都小于枢纽。
   - **递归查找**：如果 `k` 比较接近右半部分的元素，则在右半部分继续查找；如果 `k` 比较接近左半部分的元素，则在左半部分继续查找。
3. **实现步骤**：
   - 在每次划分后，通过比较 `k` 的位置来决定继续处理哪一部分。
   - 当找到的元素刚好是第 `k` 个最大的元素时，返回该元素。

```py
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        def qs(q, l, r, k):
            if l >= r:
                return 

            i, j, x = l - 1, r + 1, q[l + r >> 1]
            while i < j:
                i += 1
                while q[i] < x:
                    i += 1
                
                j -= 1
                while q[j] > x:
                    j -= 1
                
                if i < j:
                    q[i], q[j] = q[j], q[i]
            
            if j < k:
                qs(q, j + 1, r, k)
            else:
                qs(q, l, j, k)

            # qs(q, i, j, k)
            # qs(q, j + 1, r, k)
        
        n = len(nums)
        k = n - k # n - 1 - k + 1
        qs(nums, 0, n - 1, k)
        
        return nums[k]
```



## (8)[208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

### 题目大意：

实现一个 **Trie（前缀树）** 数据结构，它支持以下三种操作：

1. `insert(String word)`：向前缀树中插入一个字符串 `word`。
2. `search(String word)`：检查字符串 `word` 是否已经存在于前缀树中。
3. `startsWith(String prefix)`：检查是否有已插入的字符串以 `prefix` 作为前缀。

### 思路分析：

1. **数据结构选择**：
   - 使用字典（`dict`）来表示前缀树的节点。每个节点的键是字符，值是下一个节点或者一个特殊标记（如 `'#'`）来表示一个字符串的结束。
2. **插入操作 (`insert`)**：
   - 从根节点开始，逐个字符检查该字符是否存在于当前节点的字典中。如果不存在，则创建新的节点。
   - 当字符串遍历完毕时，设置一个特殊的标记（例如 `'#'`）来标记该节点为一个完整单词的结尾。
3. **查找操作 (`search`)**：
   - 从根节点开始，逐个字符查找，若任意字符不存在，则返回 `False`。
   - 如果查找到字符串末尾，并且该节点有 `'#'` 标记，则返回 `True`，表示该字符串已经插入。
4. **前缀查找操作 (`startsWith`)**：
   - 类似 `search`，但不需要判断是否到达字符串末尾，只要能够遍历完前缀即可。

```py
class Trie:
    def __init__(self):
        self.trie = {}

    def insert(self, word: str) -> None:
        cur = self.trie
        for ch in word:
            if ch not in cur:
                cur[ch] = {}
            cur = cur[ch]
        cur['#'] = True

    def search(self, word: str) -> bool:
        cur = self.trie
        for ch in word:
            if ch not in cur:
                return False
            cur = cur[ch]
        return cur['#'] if '#' in cur else False

    def startsWith(self, prefix: str) -> bool:
        cur = self.trie
        for ch in prefix:
            if ch not in cur:
                return False
            cur = cur[ch]
        return True
```

## (9)[207. 课程表](https://leetcode.cn/problems/course-schedule/)

1、**题目大意**
 给定 `numCourses` 门课程，每门课程的编号为 `0` 到 `numCourses-1`。某些课程有先修课程，给定一个数组 `prerequisites`，其中 `prerequisites[i] = [ai, bi]`，表示想要学习课程 `ai`，必须先学习课程 `bi`。判断是否能够完成所有课程的学习。如果能完成，返回 `true`；否则返回 `false`。

2、**实现思路**
 这是一个典型的拓扑排序问题。我们可以通过以下步骤来判断是否能完成所有课程的学习：

- 使用图的邻接表表示课程间的先修关系。
- 计算每个课程的入度，入度表示在学习当前课程之前需要学习多少门课程。
- 使用广度优先搜索（BFS）来处理图中的节点。首先，将所有入度为 0 的节点（即没有先修课程的课程）加入队列。然后依次处理这些节点，将它们依赖的课程的入度减 1。若某个课程的入度减为 0，表示可以学习该课程，将其加入队列。
- 如果能够处理所有课程，则返回 `true`；否则，说明有课程形成了环，无法完成学习，返回 `false`。

3、**实现程序**

```py
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        g = defaultdict(list)
        ind = defaultdict(int)

        for f, t in prerequisites:
            g[f].append(t)
            ind[t] += 1

        cnt = 0

        q = deque()
        for node in range(numCourses):
            if ind[node] == 0:
                q.append(node)
                cnt += 1

        while q:
            f = q.popleft()
            for nx in g[f]:
                ind[nx] -= 1
                if ind[nx] == 0:
                    cnt += 1
                    q.append(nx)

        return cnt == numCourses
```

```go
func canFinish(numCourses int, prerequisites [][]int) bool {
	g := make(map[int][]int)
	ind := make(map[int]int)

	for _, pre := range prerequisites {
		f, t := pre[0], pre[1]
		g[f] = append(g[f], t)
		ind[t]++
	}

	cnt := 0
	q := list.New()

	for node := 0; node < numCourses; node++ {
		if ind[node] == 0 {
			q.PushBack(node)
			cnt++
		}
	}

	for q.Len() > 0 {
		f := q.Remove(q.Front()).(int)
		for _, nx := range g[f] {
			ind[nx]--
			if ind[nx] == 0 {
				cnt++
				q.PushBack(nx)
			}
		}
	}

	return cnt == numCourses
}
```

## (10)[206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/)

1、**题目大意**
 给定单链表的头节点 `head`，要求反转链表并返回反转后的链表。

2、**实现思路**

- **递归方法**：
  - 递归的核心思想是通过将链表分成两部分：`head` 和 `head.next`。我们首先递归地反转 `head.next` 后的部分，然后将 `head` 插入到反转后的链表末尾。
  - 基本步骤是：当 `head` 或 `head.next` 为 `None` 时，递归停止，返回当前节点。然后在回溯时反转节点指向，最终返回反转后的链表头。
- **迭代方法**（进阶要求）：
  - 通过遍历链表，逐个调整每个节点的指向。使用三个指针：`prev`，`curr` 和 `next`。
  - 迭代过程中，`curr` 指向当前处理的节点，`prev` 指向已反转部分的尾部，`next` 是下一个待处理节点。逐步改变 `curr` 的指向，将其指向前一个节点，直到遍历完整个链表。

3、**实现代码**

- **递归方法**：

```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 递归基：如果 head 为空或只有一个节点，直接返回 head
        if not head or not head.next:
            return head
            
        # 递归反转后续链表
        newHead = self.reverseList(head.next)
        
        # 反转当前节点
        head.next.next = head  # 将 head 节点连接到反转后的链表末尾
        head.next = None       # 断开原来的链表连接

        return newHead  # 返回反转后的链表头
```

- **迭代方法**（进阶）：

```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev = None
        curr = head
        while curr:
            next_node = curr.next   # 暂存下一个节点
            curr.next = prev        # 将当前节点指向前一个节点
            prev = curr             # 更新 prev 为当前节点
            curr = next_node        # 更新 curr 为下一个节点
        
        return prev  # prev 是新的头节点
```

```go
func reverseList(head *ListNode) *ListNode {
	if head == nil || head.Next == nil {
		return head
	}
	newHead := reverseList(head.Next)
	head.Next.Next = head
	head.Next = nil
	return newHead
}
```

## (11)[200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/)

1、**题目大意**
 给定一个由 `'1'`（陆地）和 `'0'`（水）组成的二维网格，计算网格中岛屿的数量。岛屿是由陆地相连的部分，陆地相邻仅能通过水平方向或竖直方向相连。每座岛屿由连续的 `'1'` 组成，并且会被水包围。四条边界都被水包围。

2、**实现思路**
 这道题可以使用深度优先搜索（DFS）来解决。遍历整个网格，遇到一个 `'1'` 时，我们开始搜索该岛屿的所有陆地部分，将其标记为已访问。每次遇到一个新的 `'1'` 并开始 DFS 时，说明发现了一个新的岛屿，岛屿数量加一。

- **DFS**：从当前的 `'1'` 开始，向上下左右四个方向进行搜索，所有相邻的 `'1'` 都属于同一岛屿，递归标记为已访问。
- 需要一个 `visited` 数组来标记已访问的格子，防止重复访问。

3、**实现代码**

```py
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def dfs(i, j):
            vis[i][j] = True
            for k in range(4):
                x, y = i + d[k][0], j + d[k][1]
                if x < 0 or x >= n or y < 0 or y >= m or vis[x][y] or grid[x][y] == "0":
                    continue
                dfs(x, y)

        n, m = len(grid), len(grid[0])
        d = [(-1, 0), (0, 1), (1, 0), (0, -1)]
        vis = [[False] * m for _ in range(n)]
        res = 0

        for i in range(n):
            for j in range(m):
                if not vis[i][j] and grid[i][j] == "1":
                    dfs(i, j)
                    res += 1
                    
        return res
```

```go
func numIslands(grid [][]byte) int {
	n, m := len(grid), len(grid[0])
	d := [][2]int{{-1, 0}, {0, 1}, {1, 0}, {0, -1}}
	vis := make([][]bool, n)
	for i := range vis {
		vis[i] = make([]bool, m)
	}

	var dfs func(i, j int)
	dfs = func(i, j int) {
		vis[i][j] = true
		for _, dir := range d {
			x, y := i+dir[0], j+dir[1]
			if x < 0 || x >= n || y < 0 || y >= m || vis[x][y] || grid[x][y] == '0' {
				continue
			}
			dfs(x, y)
		}
	}

	res := 0
	for i := 0; i < n; i++ {
		for j := 0; j < m; j++ {
			if !vis[i][j] && grid[i][j] == '1' {
				dfs(i, j)
				res++
			}
		}
	}

	return res
}
```

## (12)[198. 打家劫舍](https://leetcode.cn/problems/house-robber/)

**1、题目大意**
 给定一个非负整数数组 `nums`，表示一排房屋中每间房存放的金额。相邻房屋不能同时偷，求在不触发警报的前提下可获得的最大金额。

**2、实现思路**

- 使用动态规划的状态压缩写法。
- 定义：
  - `f` 表示偷到前一间房（不含当前）的最大金额
  - `s` 表示偷到当前房为止的最大金额
- 转移时，对于当前房屋 `i`：
  - 要么不偷：金额为 `s`
  - 要么偷：金额为 `f + nums[i]`
  - 取两者最大值更新 `s`，并同步更新 `f`。
- 最终 `s` 即为答案，时间 O(n)，空间 O(1)。

**3、实现代码**

```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        n = len(nums)

        f, s = 0, nums[0]
        for i in range(1, n):
            f, s = s, max(f + nums[i], s)

        return s
```

```go
func rob(nums []int) int {
    f, s := 0, nums[0]

    for _, cur := range(nums[1:]) {
        f, s = s, max(s, f + cur)
    }

    return s
}
```

## (13)[169. 多数元素](https://leetcode.cn/problems/majority-element/)

**1、题目大意**
 给定一个非空数组 `nums`，其中必然存在一个出现次数大于 ⌊n/2⌋ 的元素，找出并返回该多数元素。

**2、实现思路**

- 使用 **Boyer–Moore 投票算法**。
- 维护一个候选元素 `num` 和计数器 `cnt`：
  - 当 `cnt == 0` 时，将当前元素设为候选。
  - 当前元素等于候选，`cnt +1`；否则 `cnt -1`。
- 多数元素出现次数超过一半，经过“相互抵消”后必然成为最终候选。
- 时间复杂度 O(n)，空间复杂度 O(1)。

**3、实现代码**

```py
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        cnt = 0
        num = None 

        for nm in nums:
            if not cnt:
                num = nm 
            cnt += 1 if nm == num else -1
        
        return num
```

```go
func majorityElement(nums []int) int {
    cnt, num := 0, 0
    for _, nm := range(nums) {
        if cnt == 0 {
            num = nm 
        }
        if nm == num {
            cnt += 1
        } else {
            cnt -= 1
        }
    }
    return num
}
```

## (14) [238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)

**1、题目大意**
 给定整数数组 `nums`，对每个位置 `i`，计算除 `nums[i]` 以外其余所有元素的乘积，不能使用除法，要求时间复杂度 O(n)。

**2、实现思路**

- 分别计算前缀乘积和后缀乘积：
  - `pre[i]` 表示 `i` 左侧所有元素的乘积。
  - `suf[i]` 表示 `i` 右侧所有元素的乘积。
- 对于位置 `i`，答案为 `pre[i] * suf[i]`。
- 该方法天然支持数组中包含 0 的情况。
- 时间复杂度 O(n)，空间复杂度 O(n)（可进一步压缩到 O(1)）。

**3、实现代码**

```py
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        pre, suf, res = [1] * n, [1] * n, [0] * n 

        for i in range(1, n):
            pre[i] = nums[i - 1] * pre[i - 1]
        for i in range(n - 2, -1, -1):
            suf[i] = nums[i + 1] * suf[i + 1]
        
        for i in range(n):
            res[i] = pre[i] * suf[i]
        
        return res
```

```go
func productExceptSelf(nums []int) []int {
	n := len(nums)
	pre := make([]int, n)
	suf := make([]int, n)
	res := make([]int, n)

	for i := 0; i < n; i++ {
		pre[i] = 1
		suf[i] = 1
	}

	for i := 1; i < n; i++ {
		pre[i] = nums[i-1] * pre[i-1]
	}
	for i := n - 2; i >= 0; i-- {
		suf[i] = nums[i+1] * suf[i+1]
	}

	for i := 0; i < n; i++ {
		res[i] = pre[i] * suf[i]
	}

	return res
}
```

## (15)[155. 最小栈](https://leetcode.cn/problems/min-stack/)

**1、题目大意**
 设计一个栈，支持 `push`、`pop`、`top` 操作，并能在 **O(1)** 时间内获取当前栈中的最小元素。

**2、实现思路**

- 使用两个栈：
  - 主栈 `stk` 存储所有元素。
  - 辅助栈 `mstk` 同步存储当前位置对应的最小值。
- `push` 时，将当前值与 `mstk` 栈顶最小值比较，把较小者压入 `mstk`。
- `pop` 时两个栈同时弹出。
- 栈顶元素即为当前值，辅助栈栈顶即为当前最小值。
- 所有操作时间复杂度均为 O(1)。

**3、实现代码**

```py
class MinStack:
    def __init__(self):
        self.stk = []
        self.mstk = []

    def push(self, val: int) -> None:
        self.stk.append(val)
        self.mstk.append(min(val, self.mstk[-1] if self.mstk else val))

    def pop(self) -> None:
        self.stk.pop()
        self.mstk.pop()

    def top(self) -> int:
        return self.stk[-1]

    def getMin(self) -> int:
        return self.mstk[-1]

```

```go
type MinStack struct {
	stk  []int
	mstk []int
}

func Constructor() MinStack {
	return MinStack{
		stk:  []int{},
		mstk: []int{},
	}
}

func (this *MinStack) Push(val int) {
	this.stk = append(this.stk, val)
	if len(this.mstk) == 0 {
		this.mstk = append(this.mstk, val)
	} else {
		minVal := this.mstk[len(this.mstk)-1]
		if val < minVal {
			minVal = val
		}
		this.mstk = append(this.mstk, minVal)
	}
}

func (this *MinStack) Pop() {
	this.stk = this.stk[:len(this.stk)-1]
	this.mstk = this.mstk[:len(this.mstk)-1]
}

func (this *MinStack) Top() int {
	return this.stk[len(this.stk)-1]
}

func (this *MinStack) GetMin() int {
	return this.mstk[len(this.mstk)-1]
}

```

## (16)[152. 乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)

**1、题目大意**
 给定一个整数数组 `nums`，求乘积最大的非空连续子数组，并返回该最大乘积。

**2、实现思路**

- 动态规划同时维护两个状态：
  - `mx`：以当前位置结尾的最大乘积
  - `mn`：以当前位置结尾的最小乘积
- 因为负数会使最大最小互换，所以每一步都需要同时更新两者：
  - `mx = max(nm, mx * nm, mn * nm)`
  - `mn = min(nm, mx * nm, mn * nm)`
- 每一步用 `mx` 更新全局答案 `res`。
- 时间复杂度 O(n)，空间复杂度 O(1)。

**3、实现代码**

```py
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        mx, mn = 1, 1
        res = -inf

        for i, nm in enumerate(nums):
            mxv = max(nm, mx * nm, mn * nm)
            mnv = min(nm, mx * nm, mn * nm)
            mx, mn = mxv, mnv
            res = max(res, mx)
        
        return res
```

```go
func maxProduct(nums []int) int {
	mx, mn := 1, 1
	res := nums[0]

	for _, nm := range nums {
		mxv := max(nm, mx*nm, mn*nm)
		mnv := min(nm, mx*nm, mn*nm)
		mx, mn = mxv, mnv
		if mx > res {
			res = mx
		}
	}

	return res
}

func max(a, b, c int) int {
	if a < b {
		a = b
	}
	if a < c {
		a = c
	}
	return a
}

func min(a, b, c int) int {
	if a > b {
		a = b
	}
	if a > c {
		a = c
	}
	return a
}

```

## (17)[148. 排序链表](https://leetcode.cn/problems/sort-list/)

**1、题目大意**
 给定链表头节点 `head`，将链表按升序排序，返回排序后的链表。要求时间复杂度 O(n log n)。

**2、实现思路**

- 使用 **归并排序** 适合链表：
  - 通过快慢指针找到链表中点，将链表分为左右两部分。
  - 递归排序左右两部分。
  - 使用 `merge` 函数合并两个有序链表。
- 基于链表的归并排序时间复杂度 O(n log n)，空间复杂度 O(log n) 递归栈。

**3、实现代码**

```py
class Solution:
    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 归并排序
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

```go
// 合并两个有序链表
func merge(h1, h2 *ListNode) *ListNode {
	dummy := &ListNode{-1, nil}
	cur := dummy
	for h1 != nil && h2 != nil {
		if h1.Val <= h2.Val {
			cur.Next = h1
			h1 = h1.Next
		} else {
			cur.Next = h2
			h2 = h2.Next
		}
		cur = cur.Next
	}
	if h1 != nil {
		cur.Next = h1
	}
	if h2 != nil {
		cur.Next = h2
	}
	return dummy.Next
}

func sortList(head *ListNode) *ListNode {
	// 归并排序递归函数，tail 是右边界（不包含）
	var sortFun func(head, tail *ListNode) *ListNode
	sortFun = func(head, tail *ListNode) *ListNode {
		if head == nil {
			return head
		}
		if head.Next == tail {
			head.Next = nil
			return head
		}

		slow, fast := head, head
		for fast != tail {
			fast = fast.Next
			slow = slow.Next
			if fast != tail {
				fast = fast.Next
			}
		}
		mid := slow
		return merge(sortFun(head, mid), sortFun(mid, tail))
	}

	return sortFun(head, nil)
}
```

## (18)[146. LRU 缓存](https://leetcode.cn/problems/lru-cache/)

### 题目大意：

设计并实现一个支持 LRU（最近最少使用）缓存的数据结构，该缓存具有以下功能：

- `get(key)`：如果关键字 `key` 存在于缓存中，则返回其对应的值；否则，返回 -1。
- `put(key, value)`：将 `key` 与 `value` 存入缓存。如果缓存容量已满，则删除最近最少使用的元素。

要求：

- `get` 和 `put` 操作都必须在 **O(1)** 的平均时间复杂度内完成。

### 思路分析：

LRU 缓存要求最近访问的元素保持在缓存中，而最久未访问的元素被淘汰。为了实现这个功能，我们可以使用一个 **双向链表** 和一个 **哈希表**。

#### 数据结构设计：

1. **双向链表**：
   - 双向链表的节点包含 `key` 和 `value`。
   - 维护两个虚拟节点，`head`（头部）和 `tail`（尾部）。`head` 的下一个节点是最先访问的节点，而 `tail` 的前一个节点是最近访问的节点。
   - 当一个节点被访问或插入时，它会被移动到链表的头部；当缓存满时，我们会移除尾部的节点（最久未使用）。
2. **哈希表**：
   - 哈希表用于存储缓存的 `key` 和对应的节点。这样我们可以通过 `key` 快速找到对应的节点，并进行 O(1) 的操作。

#### 主要操作：

1. **get(key)**：
   - 查找哈希表中是否存在该 `key`。如果存在，将对应的节点移动到链表的头部，并返回其值。如果不存在，返回 -1。
2. **put(key, value)**：
   - 如果 `key` 已存在，则更新节点的值并将其移到链表头部。
   - 如果 `key` 不存在，则创建一个新节点并插入链表头部。如果缓存已满，移除尾部节点。

```py
class DoubleLinkedList:
    def __init__(self, key = 0, value = 0):
        self.key = key
        self.value = value

        self.pre = None
        self.next = None

class LRUCache:

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.size = 0
        self.cache = dict()

        self.head = DoubleLinkedList()
        self.tail = DoubleLinkedList()
        self.head.next = self.tail
        self.tail.pre = self.head

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        node = self.cache[key]
        self.moveToHead(node)
        return node.value

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self.moveToHead(node)
        else:
            node = DoubleLinkedList(key, value)
            self.cache[key] = node
            if self.size < self.capacity:
                self.size += 1
            else:
                tail = self.removeTail()
                del self.cache[tail.key]
            self.addToHead(node)

        
    def removeNode(self, node):
        node.next.pre = node.pre
        node.pre.next = node.next
    
    def addToHead(self, node):
        node.next = self.head.next
        node.pre = self.head
        self.head.next.pre = node
        self.head.next = node
        
    def moveToHead(self, node):
        self.removeNode(node)
        self.addToHead(node)
    
    def removeTail(self):
        node = self.tail.pre
        self.removeNode(node)
        return node
```

```go
type DoubleLinkedList struct {
	key, value int
	prev, next *DoubleLinkedList
}

type LRUCache struct {
	capacity   int
	size       int
	cache      map[int]*DoubleLinkedList
	head, tail *DoubleLinkedList
}

func Constructor(capacity int) LRUCache {
	head := &DoubleLinkedList{}
	tail := &DoubleLinkedList{}
	head.next = tail
	tail.prev = head

	return LRUCache{
		capacity: capacity,
		size:     0,
		cache:    make(map[int]*DoubleLinkedList),
		head:     head,
		tail:     tail,
	}
}

func (this *LRUCache) Get(key int) int {
	if node, ok := this.cache[key]; ok {
		this.moveToHead(node)
		return node.value
	}
	return -1
}

func (this *LRUCache) Put(key int, value int) {
	if node, ok := this.cache[key]; ok {
		node.value = value
		this.moveToHead(node)
	} else {
		node := &DoubleLinkedList{key: key, value: value}
		this.cache[key] = node
		if this.size < this.capacity {
			this.size++
		} else {
			tail := this.removeTail()
			delete(this.cache, tail.key)
		}
		this.addToHead(node)
	}
}

func (this *LRUCache) removeNode(node *DoubleLinkedList) {
	node.prev.next = node.next
	node.next.prev = node.prev
}

func (this *LRUCache) addToHead(node *DoubleLinkedList) {
	node.next = this.head.next
	node.prev = this.head
	this.head.next.prev = node
	this.head.next = node
}

func (this *LRUCache) moveToHead(node *DoubleLinkedList) {
	this.removeNode(node)
	this.addToHead(node)
}

func (this *LRUCache) removeTail() *DoubleLinkedList {
	node := this.tail.prev
	this.removeNode(node)
	return node
}
```

## (19)[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

**1、题目大意**
 给定链表头节点 `head`，如果链表存在环，返回环的起点节点；如果无环，返回 `None`。

**2、实现思路**

- 使用 **快慢指针（Floyd 判圈算法）**：
  - 初始化 `slow` 和 `fast` 指针指向头节点。
  - `slow` 每次走一步，`fast` 每次走两步，如果两者相遇，则说明存在环。
- 若存在环，重新将 `fast` 指向头节点，同时 `slow` 保持在相遇点，每次都走一步，两者再次相遇的位置即为环的起点。
- 若快指针到达链尾，则无环，返回 `None`。
- 时间复杂度 O(n)，空间复杂度 O(1)。

**3、实现代码**

```py
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:
            return None

        fast, slow = head, head
        while fast.next:
            fast = fast.next
            if fast.next:
                fast = fast.next
            slow = slow.next
            if fast == slow:
                break 
        if not fast.next:
            return None

        fast = head
        while fast != slow:
            fast = fast.next
            slow = slow.next
        
        return fast
```

```go
func detectCycle(head *ListNode) *ListNode {
	if head == nil || head.Next == nil {
		return nil
	}

	slow, fast := head, head
	for fast != nil && fast.Next != nil {
		slow = slow.Next
		fast = fast.Next.Next
		if slow == fast {
			break
		}
	}

	if fast == nil || fast.Next == nil {
		return nil
	}

	fast = head
	for fast != slow {
		fast = fast.Next
		slow = slow.Next
	}

	return fast
}

```

## (20)[141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

**1、题目大意**
 判断给定链表 `head` 是否存在环，存在返回 `true`，不存在返回 `false`。

**2、实现思路**

- 使用 **快慢指针（Floyd 判圈算法）**：
  - 初始化 `slow` 和 `fast` 指向头节点。
  - `slow` 每次走一步，`fast` 每次走两步。
  - 如果 `fast` 或 `fast.Next` 为 `nil`，说明链表无环，返回 `false`。
  - 如果 `slow == fast`，说明链表有环，返回 `true`。
- 时间复杂度 O(n)，空间复杂度 O(1)。

**3、实现代码**

```py
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head:
            return False 
        
        fast = slow = head
        while fast.next:
            fast = fast.next
            if fast.next:
                fast = fast.next
            slow = slow.next
            if fast == slow:
                break 
        
        if not fast.next:
            return False 
        
        return True 
```

