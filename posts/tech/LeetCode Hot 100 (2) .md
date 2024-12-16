---
title : 'LeetCode Hot 100 精练🥸(2)'
date : 2024-12-08T15:37:01+08:00
lastmod: 2024-12-08T15:37:01+08:00
description : "LeetCode Hot 100 精练🥸(2)" 
categories : ["LeetCode"]
tags : ["LeetCode笔记"]
---

# LeetCode Hot 100 精练🥸(2)

记忆中的东西一定会消退，真正留下的才是学到的，一定要及时回顾。

## [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

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



## [208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

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

## [146. LRU 缓存](https://leetcode.cn/problems/lru-cache/)

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

## [124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

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

