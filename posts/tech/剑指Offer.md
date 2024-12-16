---
title : '剑指Offer'
date : 2024-04-17T15:37:01+08:00
lastmod: 2024-04-22T15:37:01+08:00
description : "LeetCode笔记(hot & 每日一题)" 
image : img/cat.jpg
draft : false    
categories : ["LeetCode"]
tags : ["LeetCode笔记"]
# password : leetcode
---


# 剑指Offer

## 数据流中的中位数

用两个堆模拟， 左边大顶堆，右边小顶堆，则两个堆顶是最中间的数字。

添加数字时：

- 如果输入的是第奇数个（比如第一个，则`N`初始化为`0`，插入后`N`为`1`，代表一共有一个数字），则先插入大顶堆，然后把堆顶插入小顶堆。**保证小顶堆的任意一个值都比大顶堆大**。
- 同样，如果输入的是第偶数个（第二个），则先插入小顶堆，然后将小顶堆最小值插入大顶堆。同样是**保证小顶堆的任意一个值都比大顶堆大**。

至于先插入哪一个堆是没有关系的，只要保证交替插入不同的堆即可，并且要确保边界问题（比如在奇数个元素的的中位数， 只有一个数据时应返回先插入的堆顶值， 否则堆为空会报错）。

```py
import heapq as hp
class Solution:
    heapl = [] # 大根堆
    heapr = [] # 小根堆
    N = 0
    def insert(self, num):
        """
        :type num: int
        :rtype: void
        """
        if self.N % 2 ==0:
            hp.heappush(self.heapl, -num)
            hp.heappush(self.heapr, -hp.heappop(self.heapl))
        else:
            hp.heappush(self.heapr, num)
            hp.heappush(self.heapl, -hp.heappop(self.heapr))
        self.N += 1
        
    def getMedian(self):
        """
        :rtype: float
        """
        if self.N %2==0:
            return (-self.heapl[0]+self.heapr[0])/2.0
        else:
            return self.heapr[0]*1.0
        
```

## 字符流中第一个只出现一次的字符

```py
import collections
class Solution:  
    d = collections.defaultdict(int)
    q = collections.deque()
    def firstAppearingOnce(self):
        """
        :rtype: str
        """
        return '#' if not self.q else self.q[0]
        
    def insert(self, char):
        """
        :type char: str
        :rtype: void
        """
        self.d[char]+=1
        self.q.append(char)
        while self.q and self.d[self.q[0]]>1:
            self.q.popleft()
```

## 最长不含重复字符的子字符串

```py
class Solution:
    def longestSubstringWithoutDuplication(self, s):
        """
        :type s: str
        :rtype: int
        """
        char_index = {}  # 存储字符的最近出现位置
        ans = 0
        start = 0
        for i, char in enumerate(s):
            if char in char_index and char_index[char] >= start:
                start = char_index[char] + 1
            char_index[char] = i
            ans = max(ans, i - start + 1)
        return ans
```

## 骰子的点数

线性DP ： 

注意点： 算法先从最简单的方法想起，DP先用二维想，实现完之后再优化。

因为每次计算状态时会修改$ j-1, j-2, j-3, j-4, j-5, j-6$, 而$j+1$会计算 $j+1-1=j, j+1-2=j-1, ... j-1$会计算$j-1-1=j-2,j-1-2=j-3,...$没有单调性，所以不能优化第一维空间

```py
class Solution(object):
    def numberOfDice(self, n):
        """
        :type n: int
        :rtype: List[int]
        """
        N = 6*n+10
        f = [[0]*N for _ in range(N)]
        f[0][0]=1
        for i in range(1,n+1):
            for j in range(1,n*6+1):
                for k in range(1,min(j, 6)+1):
                    f[i][j] += f[i-1][j-k]
        return f[n][1*n:6*n+1]
```

## 数组中数值和下标相等的元素

```py
class Solution(object):
    def getNumberSameAsIndex(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        l, r = 0, len(nums)-1
        while l<r:
            mid = l+r+1>>1
            if nums[mid]>mid:
                r=mid-1
            elif nums[mid]<mid:
                l=mid
            else:
                return mid
                break
        return l if nums[l]==l else -1
```

## 复杂链表的复刻

```py
class Solution(object):
    def copyRandomList(self, head):
        hash = {}
        hash[None] = None
        cloneHead = ListNode(-1)
        cur = cloneHead
        while head:
            if head not in hash:
                hash[head] = ListNode(head.val)
            if head.random not in hash:
                hash[head.random] = ListNode(head.random.val)
            cur.next = hash[head]
            cur = cur.next
            cur.random = hash[head.random]
            head = head.next
        return cloneHead.next
```

## 矩阵中的路径

```py
class Solution(object):
    def hasPath(self, matrix, string):
        if not matrix or not(matrix[0]):
            return False
        dx = [1, 0, -1, 0]
        dy = [0, 1, 0, -1]
        
        def dfs(x, y, idx):
            if matrix[x][y]!=string[idx]:
                return False
            if idx==len(string)-1:
                return True
            for h,v in zip(dx, dy):
                xx, yy = x+h, y+v
                if xx<0 or xx>=len(matrix) or yy<0 or yy>=len(matrix[0]):
                    continue
                memo = matrix[x][y]
                matrix[x][y] = '#'
                if dfs(xx,yy,idx+1):
                    return True
                matrix[x][y] = memo
            return False
        for i in range(len(matrix)):
            for j in range(len(matrix[0])):
                if matrix[i][j]==string[0]:
                    if dfs(i,j,0):
                        return True
        return False
            
```

## 正则表达式匹配  

DP:

```py
class Solution(object):
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        n, m = len(s), len(p)
        s, p = ' '+s, ' '+p
        f = [[False]*(m+1) for _ in range(n+1)]
        f[0][0] = True
        for i in range(2,m+1):
            if p[i]=='*':
                f[0][i] = f[0][i-2]
        for i in range(1,n+1):
            for j in range(1,m+1):
                if s[i]==p[j] or p[j]=='.':
                    f[i][j] = f[i-1][j-1]
                elif p[j]=='*':
                    if s[i]==p[j-1] or p[j-1]=='.':
                        f[i][j] = f[i-1][j]
                    f[i][j] |= f[i][j-2]
        return f[n][m]
```

## [LCR 168. 丑数](https://leetcode.cn/problems/chou-shu-lcof/)

**题目大意**：给定一个整数 $n$，找出并返回第 $n$ 个丑数。丑数是只包含质因数$ 2$、$3$ 和/或 5 的正整数，$1$ 是丑数。

**实现思路**：

1. 初始化一个数组 $f$，用于存储前$ n$ 个丑数，初始化 $f[1] = 1$。
2. 初始化三个指针 $i2$、$i3$、$i5$，分别表示下一个丑数是通过乘以$2、3、5$得到的。
3. 初始化三个变量 $n2、n3、n5$，分别表示通过指针 $i2、i3、i5$ 得到的下一个丑数的候选值。
4. 从第$ 2 $个丑数开始遍历到第 $n $个丑数：
   - 计算下一个丑数的候选值：$n2=f[i2]*2$，$n3=f[i3]*3$，$n5=f[i5]*5$。
   - 将下一个丑数更新为三个候选值中的最小值：$f[i]=min(n2,n3,n5)$。
   - 如果最小值等于 $n2$，则指针 $i2$ 往后移动一位；如果最小值等于 $n3$，则指针 $i3$ 往后移动一位；如果最小值等于 $n5$，则指针 i5 往后移动一位。
5. 遍历结束后，返回第 $n$ 个丑数$ f[n]$。

```py
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        if n<=6:
            return n
        f=[0 for _ in range(n+1)]
        f[1]=1
        i2,i3,i5,n2,n3,n5=1,1,1,0,0,0
        for i in range(2,n+1):
            n2,n3,n5=f[i2]*2,f[i3]*3,f[i5]*5
            f[i]=min(n2,n3,n5)
            if f[i]==n2:
                i2+=1
            if f[i]==n3:
                i3+=1
            if f[i]==n5:
                i5+=1
        return f[n]
```