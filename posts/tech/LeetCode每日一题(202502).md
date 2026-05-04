---
title : 'LeetCode每日一题（202502）'
date : 2025-02-23T15:37:01+08:00
lastmod: 2025-02-23T15:37:01+08:00
description : "每日一题（202502）" 
image : img/cat.jpg
draft : false    
categories : ["LeetCode"]
tags : ["LeetCode笔记"]
---

# 每日一题（202502）



## 0223 [1206. 设计跳表](https://leetcode.cn/problems/design-skiplist/)

下面给出一个回答，包括题目大意、实现思路和写满注释的代码，每部分既不啰嗦也不简略：

------

### 1. 题目大意

设计一个跳表，要求在平均 O(log n) 时间内完成搜索、插入和删除操作。跳表由多层有序链表构成，每一层起到加速查找的作用，同时允许存在重复的值。

------

### 2. 实现思路

- **跳表基本结构**
  - 使用多层链表，每个节点保存一个数值和一个指针列表，指向每一层的下一个节点。
  - 最底层链表保存所有元素，上层链表是底层节点的“抽样”，用于快速跳跃搜索。
- **随机层数生成**
  - 插入新节点时，通过随机函数确定其层数，常用的方式是以一定概率（如 P_FACTOR=0.25）决定是否增加一层，最多不超过 MAX_LEVEL。
- **各操作实现**
  - **搜索**：从最高层开始，沿当前层不断向右移动，直到下一个节点大于等于目标值，再降一层继续查找，直到最低层检查是否找到目标值。
  - **插入**：首先在每一层记录最后一个小于待插入值的节点（update 数组），然后生成随机层数，并在相应层中更新指针将新节点插入。
  - **删除**：类似于插入，先记录每层中最后一个小于待删除值的节点，确认目标节点存在后，在各层更新指针跳过目标节点，若删除后最高层为空则降低层数。

------

### 3. 写满注释的代码

```python
from random import random

# 定义最大层数
MAX_LEVEL = 32
# 定义随机提升层数的概率因子
P_FACTOR = 0.25

def random_level() -> int:
    """
    随机生成新节点的层数。
    初始层数为1，每次随机值小于 P_FACTOR 则增加一层，
    但最多不超过 MAX_LEVEL。
    """
    lv = 1
    while lv < MAX_LEVEL and random() < P_FACTOR:
        lv += 1
    return lv

class SkiplistNode:
    """
    跳表节点类，包含节点的值和各层的指针列表（forward）。
    """
    __slots__ = 'val', 'forward'
    def __init__(self, val: int, max_level=MAX_LEVEL):
        # 节点的值
        self.val = val
        # forward[i] 表示该节点在第 i 层的下一个节点，初始化为 None
        self.forward = [None] * max_level

class Skiplist:
    """
    跳表类，包含搜索、插入、删除操作。
    """
    def __init__(self):
        # 创建头节点，头节点值设为 -1，头节点的 forward 列表长度为 MAX_LEVEL
        self.head = SkiplistNode(-1)
        # 当前跳表的有效层数，初始为 0 层
        self.level = 0

    def search(self, target: int) -> bool:
        """
        搜索跳表中是否存在目标值 target。
        从最高层开始，逐层向右遍历，找到每层中最后一个小于 target 的节点，
        最后检查最低层下一个节点是否等于 target。
        """
        curr = self.head
        # 从最高层到第 0 层依次遍历
        for i in range(self.level - 1, -1, -1):
            # 在当前层中向右移动，直到下一个节点的值不小于 target
            while curr.forward[i] and curr.forward[i].val < target:
                curr = curr.forward[i]
        # 到达最低层后，移动到下一个节点
        curr = curr.forward[0]
        # 如果节点存在且值等于 target，则返回 True，否则返回 False
        return curr is not None and curr.val == target

    def add(self, num: int) -> None:
        """
        在跳表中插入一个数值 num。
        先在每层记录最后一个小于 num 的节点，然后生成随机层数，
        并在相应层中更新指针将新节点插入跳表中。
        """
        # update 数组用于记录每一层最后一个小于 num 的节点
        update = [self.head] * MAX_LEVEL
        curr = self.head
        # 从最高层到第 0 层查找插入位置
        for i in range(self.level - 1, -1, -1):
            while curr.forward[i] and curr.forward[i].val < num:
                curr = curr.forward[i]
            update[i] = curr

        # 生成新节点的随机层数
        lv = random_level()
        # 更新当前跳表的有效层数
        self.level = max(self.level, lv)
        # 创建新节点，新节点只有 lv 层有效指针
        new_node = SkiplistNode(num, lv)
        # 在每一层更新指针，将新节点插入到链表中
        for i in range(lv):
            # 新节点的 forward 指向 update[i] 原先的下一个节点
            new_node.forward[i] = update[i].forward[i]
            # 将 update[i] 的 forward 指向新节点
            update[i].forward[i] = new_node

    def erase(self, num: int) -> bool:
        """
        在跳表中删除一个数值 num。如果存在多个相同的数值，仅删除其中一个。
        先在每层记录最后一个小于 num 的节点，然后确认目标节点存在后，
        在各层更新指针跳过目标节点，最后更新跳表的有效层数。
        """
        # update 数组记录每层最后一个小于 num 的节点
        update = [None] * MAX_LEVEL
        curr = self.head
        # 从最高层到第 0 层查找目标节点的前驱节点
        for i in range(self.level - 1, -1, -1):
            while curr.forward[i] and curr.forward[i].val < num:
                curr = curr.forward[i]
            update[i] = curr

        # 移动到第 0 层的下一个节点，即可能的目标节点
        curr = curr.forward[0]
        # 如果目标节点不存在或值不匹配，则返回 False
        if curr is None or curr.val != num:
            return False

        # 在每一层中更新指针，跳过目标节点
        for i in range(self.level):
            if update[i].forward[i] != curr:
                break
            update[i].forward[i] = curr.forward[i]

        # 更新跳表的当前有效层数：如果最高层已空，则降低层数
        while self.level > 1 and self.head.forward[self.level - 1] is None:
            self.level -= 1

        # 删除成功返回 True
        return True
```





