---
title : '算法笔记（一）——基础+杂项（Python实现）'
date : 2024-04-09T15:37:01+08:00
lastmod: 2025-05-05T15:37:01+08:00
description : "算法笔记（一）——基础+杂项（Python实现）" 
image : img/cat.jpg
draft : false    
categories : ["算法笔记"]
tags : ["算法笔记","Python"]
# password : leetcode
---

#  算法笔记（一）——基础+杂项（Python实现）

## 基础+杂项

### 快速排序

```py
def quick_sort(q, l, r):
    if l>=r:
        return 
    i,j,x=l-1,r+1,q[(l+r)>>1]
    while i<j:
        i+=1
        while q[i]<x:
            i+=1
        j-=1
        while q[j]>x:
            j-=1
        if i<j:
            q[i], q[j] = q[j], q[i]
    quick_sort(q, l, j)
    quick_sort(q, j+1, r)
n=int(input())
arr=list(map(int, input().split()))
quick_sort(arr,0,n-1)
print(" ".join(map(str, arr)))
```

#### 随机选择`pivot`

```py
import random
class Solution:
    def sortArray(self, nums: List[int]) -> List[int]:
        def quick_sort(q, l, r):
            if l >= r:
                return
            # 随机选择 pivot
            i, j = l, r
            rand_idx = random.randint(l, r)
            x = q[rand_idx]
            while i <= j:
                while q[i] < x:
                    i += 1
                while q[j] > x:
                    j -= 1
                if i <= j:
                    q[i], q[j] = q[j], q[i]
                    i += 1
                    j -= 1
            
            quick_sort(q, l, j)
            quick_sort(q, i, r)
        
        quick_sort(nums, 0, len(nums) - 1)
        return nums
```

### 归并排序

`j = mid+1 !!!`

```py
from sys import stdin
input = lambda:stdin.readline().strip()

def merge_sort(q, l, r):
    if l>=r:
        return 
    mid = l+r>>1
    merge_sort(q, l, mid); merge_sort(q, mid+1, r)
    i, j = l, mid+1
    tmp = []
    while i<=mid and j<=r:
        if q[i] <= q[j]:
            tmp.append(q[i])
            i += 1
        else:
            tmp.append(q[j])
            j += 1
    if i<=mid:
        tmp.extend(q[i:mid+1])
    if j<=r:
        tmp.extend(q[j:r+1])
    q[l:r+1] = tmp
        
n = int(input())
arr = list(map(int, input().split()))
merge_sort(arr, 0, n-1)
print(*arr)

```

### 二分

```py
def check(x):
    # 检查 x 是否满足某种性质
    pass  # 这里需要根据具体的情况实现

def bsearch_1(l, r):
    while l < r:
        mid = (l + r) // 2
        if check(mid):
            r = mid
        else:
            l = mid + 1
    return l

def bsearch_2(l, r):
    while l < r:
        mid = (l + r + 1) // 2
        if check(mid):
            l = mid
        else:
            r = mid - 1
    return l

# 示例用法
# 首先定义 check 函数来检查性质
# 然后使用 bsearch_1 或 bsearch_2 来进行二分搜索
```

#### [最佳牛围栏](https://www.acwing.com/problem/content/description/104/)

- 题意：n块地，每块地上有奶牛，现在需要用围栏围起一些奶牛， 但至少有f块地，问最终围起的地中牛的数量的平均数的最大值为多少。
- **浮点数二分最大数量**，判断所有数减去当前二分的平均数后，有没有一段长度为f并且是全为正数的，如果有则合法。判断方法：b数组存原数组减去平均数，求前缀和，用前缀和数组求某一段内的和是否为正数即可， 只要长度大于f的所有段都要枚举，但是我们只要求$s[i]-s[0->i-f]$是否有一个大于0即可，所以只用求$min(s[0:i-f])$即可。注意浮点数二分因为误差需要用`eps`判断，并且r会稍微大于答案，l会稍微小于答案，题目问的是向下取整最近的整数，所以取r向下取整。

```py
import sys
input = lambda:sys.stdin.readline().strip()
N = int(1e5+10)
a, b = [0]*N, [0]*N

n, f = map(int , input().split())

for i in range(1, n+1):
    a[i] = int(input())
    
l, r = 0.0, 2e3+10
eps = 1e-6
while r-l > eps:
    mid = (l+r)/2
    s, mi, cur = [0]*N, 1e18, -1e18
    for i in range(1, n+1):
        b[i] = a[i]-mid
    for i in range(1, n+1):
        s[i] = s[i-1]+b[i]
    for i in range(f, n+1):
        mi = min(mi, s[i-f])
        cur = max(cur, s[i]-mi)
    if cur>=0:
        l = mid
    else:
        r = mid
        
print(int(r*1000))
```

### 浮点数二分

```py
def check(x):
    # 检查x是否满足某种性质
    pass  # 这里需要根据具体情况实现check函数

def bsearch_3(l, r):
    eps = 1e-6  # eps 表示精度，取决于题目对精度的要求
    while r - l > eps:
        mid = (l + r) / 2
        if check(mid):
            r = mid
        else:
            l = mid
    return l
```


### 一维前缀和

```py
def prefixSum(arr):
    n = len(arr)
    prefixSum = [0] * n
    prefixSum[0] = arr[0]

    for i in range(1, n):
        prefixSum[i] = prefixSum[i-1] + arr[i]

    for i in range(n):
        print(prefixSum[i], end=" ")

arr = [1, 2, 3, 4, 5]
prefixSum(arr)
```

### 二维前缀和

```py
import sys
# 重定向输入函数为sys.stdin.readline().strip()以去除末尾换行符
input = lambda:sys.stdin.readline().strip()

# 读取n、m、q三个整数
n, m, q = map(int, input().split())

# 创建一个n+1行，m+1列的二维列表g，初始值均为0
g = [[0]*(m+1) for _ in range(n+1)]

# 读取地图的每一行，并将值转换为整数，存储到g中
for i in range(1, n+1):
    g[i] = [0] + list(map(int, input().split()))

# 计算前缀和，g[i][j]表示以(i,j)为右下角的矩形内所有元素之和
for i in range(1, n+1):
    for j in range(1, m+1):
        g[i][j] = (g[i][j] + g[i-1][j] + g[i][j-1] - g[i-1][j-1])

# 针对每个查询，计算给定矩形区域内元素之和
for i in range(q):
    x1, y1, x2, y2 = map(int, input().split())
    # 计算矩形区域内元素之和，利用前缀和的性质
    # g[x2][y2]表示右下角坐标为(x2,y2)的矩形内所有元素之和
    # g[x1-1][y2]表示右上角坐标为(x1-1,y2)的矩形内所有元素之和
    # g[x2][y1-1]表示左下角坐标为(x2,y1-1)的矩形内所有元素之和
    # g[x1-1][y1-1]表示左上角坐标为(x1-1,y1-1)的矩形内所有元素之和
    # 利用以上四个值，可以求得目标矩形区域内元素之和
    print(g[x2][y2] - g[x1-1][y2] - g[x2][y1-1] + g[x1-1][y1-1])
```

### 一维差分

差分和前缀和是逆运算。

需要计算某一段区间$+-$操作时，运用差分操作 `updateRange` 需要先构造差分数组

$s[i] = s[i-1]+a[i]$ 逆运算 $d[i]=a[i]-a[i-1]$ （原数组看作为前缀和数组）

```py
def updateRange(B, l, r, c):
    B[l] += c
    B[r + 1] -= c

def printArray(arr):
    for i in range(len(arr)):
        print(arr[i], end=" ")
    print()

n= 5
B = [0] * (n + 1)

updateRange(B, 1, 3, 2)
updateRange(B, 2, 4, 3)

printArray(B)
```

```py
import sys

# 重定向输入函数为sys.stdin.readline().strip()以去除末尾换行符
input = lambda:sys.stdin.readline().strip()

# 读取n和m两个整数，表示序列长度和操作次数
n, m = map(int, input().split())

# 读取序列a，长度为n，将其转换为整数列表，同时在开头添加0以便处理边界情况
a = [0] + list(map(int, input().split()))

# 初始化差分数组d，长度为n+2，初始值均为0
d = [0]*(n+2)

# 计算原始序列a中相邻元素的差值，存储到差分数组d中
for i in range(1, n+1):
    d[i] = a[i] - a[i-1]

# 执行m次操作
for i in range(m):
    # 读取操作参数l、r、c，表示对[l,r]范围内元素增加c
    l, r, c = map(int, input().split())
    # 在差分数组中更新对应区间[l,r]内元素的增量
    d[l] += c
    d[r+1] -= c
    
# 根据差分数组还原原始序列
for i in range(1, n+1):
    d[i] += d[i-1]

# 输出还原后的原始序列
print(*d[1:n+1])

```

### 二维差分

```py
# 从输入中获取网格的行数、列数和操作数量
n, m, q = map(int, input().split())

# 初始化原始网格数组和操作影响数组
g = [[0]*(m+1) for _ in range(n+1)]
d = [[0]*(m+2) for _ in range(n+2)]

# 填充原始网格数组
for i in range(1, n+1):
    # 将每行的数据存入二维数组g中
    g[i] = [0] + list(map(int, input().split()))

# 处理每个操作
for i in range(q):
    # 获取操作的左上角坐标、右下角坐标和要添加到该区域内的值
    x1, y1, x2, y2, c = map(int, input().split())
    
    # 更新操作影响数组
    d[x1][y1] += c
    d[x1][y2+1] -= c
    d[x2+1][y1] -= c
    d[x2+1][y2+1] += c 
    
# 根据操作影响数组更新原始网格数组
for i in range(1, n+1):
    for j in range(1, m+1):
        # 更新操作影响的累积值
        d[i][j] = d[i][j] + d[i-1][j] + d[i][j-1] - d[i-1][j-1]
        # 更新原始网格数组中的值
        g[i][j] += d[i][j]

# 打印更新后的原始网格数组
for i in range(1, n+1):
    print(*g[i][1:m+1])

```

### 双指针

```py
for i in range(n):
    j = 0
    while j < i and check(j, i):
        j += 1
    # 具体问题的逻辑

# 常见问题分类： 
# (1) 对于一个序列，用两个指针维护一段区间
# (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作
```

### 位运算

```py
原码，反码，补码
求n二进制表示中第k位数字: n >> k & 1 
返回n的最后一位1：lowbit(n) = n & -n 树状数组基本操作
```

### 离散化

```py
alls = []  # 存储所有待离散化的值

# 将所有值排序
alls.sort()

# 去掉重复元素
alls = list(set(alls))

# 二分求出x对应的离散化的值
def find(x):
    l, r = 0, len(alls) - 1
    while l < r:
        mid = (l + r) // 2
        if alls[mid] >= x:
            r = mid
        else:
            l = mid + 1
    return r + 1  # 映射到1, 2, ...n

```

```py
import sys
input = lambda:sys.stdin.readline().strip()

def find(x):
    l, r = 0, num
    while l<r:
        mid = (l+r+1)>>1
        if ls[mid] <= x:
            l = mid
        else:
            r = mid-1
    return l

N = int(1e5)+10
s = [0]*N
ls = []
d = dict()
n, m = map(int, input().split())
for i in range(n):
    x, c = map(int, input().split())
    if x not in d:
        d[x] = c
        ls.append(x)
    else:
        d[x] += c

ls.sort()
num = len(d)
ls = [0] + ls
for i in range(1, num+1):
    s[i] = s[i-1] + d[ls[i]]
    
for i in range(m):
    l, r = map(int, input().split())
    pl, pr = find(l), find(r)
    if l in d:
        pl -= 1
    print(s[pr] - s[pl])
```



### 区间合并

```py
def merge(segs):
    segs.sort()  # 区间左端点排序
    res = []
    st, ed = -2e9, -2e9
    for seg in segs:
        if ed < seg[0]:
            if st != -2e9:
                res.append((st, ed))
            st, ed = seg[0], seg[1]
        else:
            ed = max(ed, seg[1])
    if st != -2e9:
        res.append((st, ed))
    segs[:] = res
```

```py
n = int(input())
st, ed = -int(2e9), -int(2e9)
segs = []

for i in range(n):
    l, r = map(int, input().split())
    segs.append((l, r))

segs.sort()
cnt = 0

for seg in segs:
    s, e = seg
    if s>ed:
        st, ed = s, e
        cnt+=1
    else:
        ed = max(e, ed)

print(cnt)
```

### 矩阵快速幂

```py
mod = int(1e9) + 7

def mul(a, b):
    t = [[0]*101 for _ in range(101)]
    for i in range(1, n+1):
        for j in range(1, n+1):
            for k in range(1, n+1):
                t[i][j] = (t[i][j] + a[i][k]*b[k][j]) % mod

    return t

def qp(a, k):
    res = [[0]*101 for _ in range(101)]
    for i in range(1, n+1):
        res[i][i] = 1
    while k:
        if k&1:
            res = mul(res, a)
        a = mul(a, a)
        k>>=1
    return res

n, k = map(int, input().split())
a = [[0]*101 for _ in range(101)]

for i in range(1, n+1):
    row = [0] + list(map(int, input().split()))
    for j in range(1, n+1):
        a[i][j] = row[j]


ans = qp(a, k)

for i in range(1, n+1):
    print(*ans[i][1:n+1], sep=' ')

```



### 递归[98. 分形之城](https://www.acwing.com/problem/content/description/100/)

```py
t = int(input())

def calc(n, m):
    if not m: return (0, 0)
    
    l, cnt = 1<<(n-1), 1<<(2*n-2)
    x, y = calc(n-1, m%cnt)
    z = m//cnt
    
    if z==0:
        return (y, x)
    if z==1:
        return (x, y+l)
    if z==2:
        return (x+l, y+l)
    if z==3:
        return (2*l-1 - y, l-1 - x)

for i in range(t):
    n, a, b = map(int, input().split())
    a-=1; b-=1
    xa, ya = calc(n, a)
    xb, yb = calc(n, b)
    
    dis = int(( (xa-xb)**2 + (ya-yb)**2 ) ** 0.5 * 10 + 0.5)
    print(dis)

```

### 递推[95. 费解的开关](https://www.acwing.com/problem/content/description/97/)

**题目大意**：游戏规则如下：有 `25` 盏灯排成 `5×5` 的方形。每一盏灯都有一个开关，改变某一盏灯的状态会导致其上下左右相邻的灯也改变状态。给定初始状态，判断是否能在 `6` 步以内使所有的灯都变亮。

**实现思路**：首先，我们需要实现一个函数 `turn(x, y)` 来实现改变灯的状态以及连锁反应。然后，我们对于每个初始状态，采用深度优先搜索或者广度优先搜索的方式来模拟游戏过程，尝试所有可能的开关状态组合，并记录达到目标状态所需的步数。最后，输出结果即可。

```py
from copy import deepcopy
dx = [0, 1, 0, -1, 0]
dy = [0, 0, 1, 0, -1]
g = [[] for _ in range(5)]

def turn(x, y):
    for i in range(5):
        xx = x+dx[i]
        yy = y+dy[i]
        if xx in range(5) and yy in range(5):
            g[xx][yy] ^= 1
        
def work():
    global g
    backup = deepcopy(g)
    ans = float('inf')
    
    for k in range(1<<5):
        cur, flag = 0, True
        g = deepcopy(backup)
        for i in range(5):
            if (k>>i)&1:
                turn(0, i)
                cur+=1
        for i in range(4):
            for j in range(5):
                if g[i][j]==0:
                    turn(i+1, j)
                    cur+=1
        for i in range(5):
            if g[4][i]==0:
                flag = False
                break
        if flag:
            ans = min(ans, cur)
            
    return ans

t = int(input())
for _ in range(t):
    for i in range(5):
        g[i] = [int(_) for _ in list(input())]
    if _ < t-1:
        input()
    res = work()
    if res<=6:print(res)
    else:print(-1)
```

### ST1273. 天才的记忆

**题目大意**：有一个长度为 N 的数字序列，编号为 1 到 N，每次询问给出两个数字 A 和 B，要求回答 A 到 B 区间内的最大数。

**实现思路**：这是一个典型的区间最值查询问题，可以使用线段树进行求解。首先，需要初始化一个二维数组 f 用于存储区间最大值信息。然后，利用预处理的方式，计算出 log 数组，用于快速计算区间长度的对数。接着，利用动态规划的思想，初始化 f 数组，使得 f[i][0] 等于数字序列中对应位置的值。接下来，利用动态规划的思想，更新 f 数组，使得 f[i][j] 等于区间 [i, i+2^j-1] 内的最大值。最后，对于每个询问，利用预处理的区间最值信息，通过 log 数组快速确定区间长度的对数，然后利用区间最值信息查询区间最大值，即可得到答案。

```py
import sys
input = lambda:sys.stdin.readline().strip()
N = int(2e5)+10
M = 20
f = [[0]*M for _ in range(N)]
log = [0]*N

def init():
    log[0] = -1
    for i in range(1, N):
        log[i] = log[i>>1] + 1
    for j in range(M):
        for i in range(1, n+1):
            if not j:
                f[i][j] = w[i]
            else:
                if i + (1<<(j-1)) > n:
                    break
                f[i][j] = max(f[i][j-1], f[i+(1<<(j-1))][j-1])

def query(l, r):
    leng = r-l+1
    k = log[leng]
    return max(f[l][k], f[r-(1<<k)+1][k])
    
n = int(input())
w = [0] + list(map(int, input().split()))
init()
m = int(input())

for i in range(m):
    l, r = map(int, input().split())
    print(query(l, r))
```

