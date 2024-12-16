---
title : '算法笔记（二）——数据结构(Python实现)'
date : 2024-04-09T15:37:01+08:00
lastmod: 2024-04-09T15:37:01+08:00
description : "算法笔记（二）——数据结构(Python实现)" 
image : img/cat.jpg
draft : false    
categories : ["算法笔记"]
tags : ["算法笔记","Python"]
# password : leetcode
---

#  算法笔记（二）——数据结构(Python实现)

## 数据结构

### 单链表

```py
N = int(1e5+10)
e=[0]*N
ne=[0]*N
head=-1
idx=1

def insert(x):
    global idx, head
    e[idx]=x
    ne[idx]=head
    head=idx
    idx+=1
    
def add(k, x):
    global idx
    e[idx]=x
    ne[idx]=ne[k]
    ne[k]=idx
    idx+=1

def remove(k):
    global idx, head
    if k==0:
        head = ne[head]
    else:
        ne[k]=ne[ne[k]]
n = int(input())
for _ in range(n):
    s = input().split()
    op=s[0]
    if op=='H':
        insert(int(s[1]))
    elif op=='I':
        add(int(s[1]), int(s[2]))
    else:
        remove(int(s[1]))

i=head
while i!=-1:
    print(e[i], end=' ')
    i=ne[i]
```

### 双链表

```py
N = int(1e5+10)
e=[0]*N
l=[0]*N
r=[0]*N
idx=0
def init():
    global idx
    r[2]=1
    l[1]=2
    idx=3
def insert(k, x):
    global idx
    e[idx]=x
    l[idx]=k
    r[idx]=r[k]
    l[r[k]]=idx
    r[k]=idx
    idx+=1
def remove(k):
    l[r[k]]=l[k]
    r[l[k]]=r[k]
init()
m=int(input())
for _ in range(m):
    s=input().split()
    if s[0]=='L':
        x=int(s[1])
        insert(2,x)
    elif s[0]=='R':
        x=int(s[1])
        insert(l[1],x)
    elif s[0]=='D':
        k=int(s[1])+2
        remove(k)
    elif s[0]=='IL':
        k=int(s[1])+2
        x=int(s[2])
        insert(l[k],x)
    elif s[0]=='IR':
        k=int(s[1])+2
        x=int(s[2])
        insert(k,x)
i=2
while i!=0:
    if i==2 or i==1:
        i=r[i]
        continue
    print(e[i],end=" ")
    i=r[i]
```

### 栈

```py
N = int(1e5+10)  # 假设N的值为100

stk = [0] * N
tt = 0

# 向栈顶插入一个数
tt += 1
stk[tt] = x

# 从栈顶弹出一个数
tt -= 1

# 栈顶的值
stk[tt]

# 判断栈是否为空
if tt > 0:
    pass
```

### 队列

```py
N = 100  # 假设N的值为100

q = [0] * N
hh = 0
tt = -1

# 向队尾插入一个数
tt += 1
q[tt] = x

# 从队头弹出一个数
hh += 1

# 队头的值
q[hh]

# 判断队列是否为空
if hh <= tt:
    pass
    
N = 100  # 假设N的值为100

q = [0] * N
hh = 0
tt = 0

# 向队尾插入一个数
q[tt] = x
tt += 1
if tt == N:
    tt = 0

# 从队头弹出一个数
hh += 1
if hh == N:
    hh = 0

# 队头的值
q[hh]

# 判断队列是否为空
if hh != tt:
    pass
```

### 单调栈

```py
tt = 0
stk = [0] * (n + 1)

for i in range(1, n + 1):
    while tt and check(stk[tt], i):
        tt -= 1
    stk[tt + 1] = i
    tt += 1
```

### 单调队列

```py
n = 10  # 假设n的值为10

hh = 0
tt = -1
q = [0] * n

for i in range(n):
    while hh <= tt and check_out(q[hh]):
        hh += 1
    while hh <= tt and check(q[tt], i):
        tt -= 1
    q[tt + 1] = i
    tt += 1
    
N = int(1e6+10)
q=[0 for _ in range(N)]
n,k=map(int, input().split())
a=[0]+[int(x) for x in input().split()]

hh,tt=0,-1
for i in range(1,n+1):
    if hh<=tt and i-q[hh]+1>k:
        hh+=1
    while hh<=tt and a[q[tt]] >= a[i]:
        tt-=1
    tt+=1
    q[tt]=i
    if i >= k:
        print(a[q[hh]], end=" ")
    
print()
hh,tt=0,-1
for i in range(1,n+1):
    if hh<=tt and i-q[hh]+1>k:
        hh+=1
    while hh<=tt and a[q[tt]] <= a[i]:
        tt-=1
    tt+=1
    q[tt]=i
    if i>=k:
        print(a[q[hh]], end=" ")
```

### KMP

```py
m = len(p)  # 假设p为模板串，长度为m
n = len(s)  # 假设s为模式串，长度为n

ne = [0] * (m + 1)  # 初始化ne数组

# 求Next数组
j = 0
for i in range(2, m + 1):
    while j and p[i] != p[j + 1]:
        j = ne[j]
    if p[i] == p[j + 1]:
        j += 1
    ne[i] = j

# 匹配
j = 0
for i in range(1, n + 1):
    while j and s[i] != p[j + 1]:
        j = ne[j]
    if s[i] == p[j + 1]:
        j += 1
    if j == m:
        j = ne[j]
        # 匹配成功后的逻辑
```

```py
n = int(input())
p = ' ' + input()
m = int(input())
s = ' ' + input()

ne = [0]*(n+1)
j = 0
for i in range(2, n+1):
    while j and p[i]!=p[j+1]:
        j = ne[j]
    if p[i]==p[j+1]:
        j += 1
    ne[i] = j

j = 0
res = []
for i in range(1, m+1):
    while j and s[i]!=p[j+1]:
        j = ne[j]
    if s[i]==p[j+1]:
        j += 1
    if j==n:
        res.append(i-j)
        j = ne[j]

print(*res, sep=' ')    
```

### Z函数

```py
def z_function(s):
    n = len(s)
    z = [0] * n
    l, r = 0, 0
    for i in range(1, n):
        if i <= r and z[i - l] < r - i + 1:
            z[i] = z[i - l]
        else:
            z[i] = max(0, r - i + 1)
            while i + z[i] < n and s[z[i]] == s[i + z[i]]:
                z[i] += 1
        if i + z[i] - 1 > r:
            l = i
            r = i + z[i] - 1
    return z
```

### Tire

```py
N = 100010
son = [[0] * 26 for _ in range(N)]
cnt = [0] * N
idx = 0

# 0号点既是根节点，又是空节点
# son[][]存储树中每个节点的子节点
# cnt[]存储以每个节点结尾的单词数量
# 插入一个字符串
def insert(s):
    global idx
    p = 0
    for i in range(len(s)):
        u = ord(s[i]) - ord('a')
        if not son[p][u]:
            idx += 1
            son[p][u] = idx
        p = son[p][u]
    cnt[p] += 1

# 查询字符串出现的次数
def query(s):
    p = 0
    for i in range(len(s)):
        u = ord(s[i]) - ord('a')
        if not son[p][u]:
            return 0
        p = son[p][u]
    return cnt[p]

```

### 马拉车（字符串回文串算法）

```py
class Solution:
    # 推荐教学视频 ：https://www.bilibili.com/video/BV1Sx4y1k7jG/?spm_id_from=333.337.search-card.all.click&vd_source=a4a2b56f746715b34521bfb853094cf4
    def longestPalindrome(self, s: str) -> str:
        s = '#' + '#'.join(list(s)) + '#'
        n = len(s)
        p = [0]*n #每个点的 最长回文字串 能到的 两侧长度
        c, r = 0, 0 # 右边能到达最远的蘑菇的位置 和 其最右边能达到的位置

        for i in range(n):
            if i<=r:
                p[i] = min(r-i, p[c + c-i]) # 由已知条件得到当前位置能达到的最大右侧距离( 需要取min(镜像位置的值, 当前最大蘑菇能覆盖到的最大值) )
            while i+p[i]+1 < n and s[i-p[i]-1] == s[i+p[i]+1]:
                p[i]+=1
            if p[i]+i > r:
                r = p[i] + i
                c = i

        ma = max(p)
        idx = p.index(ma)

        return s[idx-ma+1:idx+ma+1:2] 
```

### 并查集

```PY
N = 1000005  # 假设N的值为1000005

p = [0] * N  # 初始化p数组

# 返回x的祖宗节点
def find(x):
    if p[x] != x:
        p[x] = find(p[x])
    return p[x]
''' 非递归写法
def find(x):
    while p[x]!=x:
        p[x] = p[p[x]]
        x = p[x]
    return x
'''
# 初始化，假定节点编号是1~n
for i in range(1, n + 1):
    p[i] = i

# 合并a和b所在的两个集合
p[find(a)] = find(b)
```

维护size信息

```py
# Python中没有类似于C++的数组声明方式，我们直接使用列表来代替
p = [i for i in range(N)]
size = [1] * N

# 返回x的祖宗节点
def find(x):
    if p[x] != x:
        p[x] = find(p[x])
    return p[x]

# 初始化，假定节点编号是1~n
n = N # 假设n是提前定义好的
for i in range(1, n + 1):
    p[i] = i
    size[i] = 1

# 合并a和b所在的两个集合
p[find(a)] = find(b)
size[b] += size[a]
```

维护到祖宗节点距离的并查集

```py
# Python中没有类似于C++的数组声明方式，我们直接使用列表来代替
p = [i for i in range(N)]
d = [0] * N

# 返回x的祖宗节点
def find(x):
    if p[x] != x:
        u = find(p[x])
        d[x] += d[p[x]]
        p[x] = u
    return p[x]

# 初始化，假定节点编号是1~n
n = N # 假设n是提前定义好的
for i in range(1, n + 1):
    p[i] = i
    d[i] = 0

# 合并a和b所在的两个集合
p[find(a)] = find(b)
d[find(a)] = distance  # 根据具体问题，初始化find(a)的偏移量
```

**题目大意**

Alice 和 Bob 玩了一个古老的格子游戏。游戏在一个 n×n 的点阵上进行，玩家轮流在相邻的点之间画线，分别是红边和蓝边。玩家在画出一条封闭的圈（面积不限）时游戏结束，该玩家获胜。题目要求判断游戏在第几步结束（即画出封闭圈），若 m 步之后也没有结束，则输出 "draw"。
**实现思路**

1. 输入与初始化：读取输入的点阵大小 n 和已画的 m 条边。初始化一个数组 p 作为并查集，p[i] 表示点 i 的父节点。
2. 并查集的查找函数：定义一个 find 函数用于查找点的根节点，同时进行路径压缩以优化查找效率。
3. 处理每条边：
   - ​    将二维坐标转换为一维标识。
   - ​    根据边的方向（向右或向下）计算出连接的两个点。
   - ​    使用并查集查找这两个点的根节点，判断它们是否属于同一个集合。如果是同一个集合，则说明形成了封闭圈，输出当前步骤并结束程序。
4. ​    如果不属于同一个集合，则将两个点合并。
5. 输出结果：如果遍历完所有边后仍未形成封闭圈，则输出 "draw"。

```python
from sys import stdin
input = lambda:stdin.readline().strip()

def find(x):
    while p[x] != x:
        p[x] = p[p[x]]
        x = p[x]
    return x

n, m = map(int, input().split())
p = [i for i in range(n*n+1)]
for i in range(1, m+1):
    x, y, op = input().split()
    x, y = map(int, (x, y))
    a = (x-1)*n + y - 1
    if op=='D':
        b = x*n + y - 1
    else:
        b = (x-1)*n + y 
    
    pa, pb = find(a), find(b)
    if pa == pb:
        print(i)
        exit()
    
    p[pa] = pb

print('draw')
```

### 堆

```py
# h[N] 存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x + 1
# ph pos[k] 存储第k个插入的点在堆中的位置
# hp ord[k] 存储堆中下标是k的点是第几个插入的

N = 1000005  # 假设N的值为1000005

h = [0] * N  # 初始化h数组
ph = [0] * N  # 初始化ph数组
hp = [0] * N  # 初始化hp数组
size = 0  # 初始化size为0

# 交换两个点，及其映射关系
def heap_swap(a, b):
    ph[hp[a]], ph[hp[b]] = ph[hp[b]], ph[hp[a]]
    hp[a], hp[b] = hp[b], hp[a]
    h[a], h[b] = h[b], h[a]
    

def down(u):
    t = u
    if u * 2 <= size and h[u * 2] < h[t]:
        t = u * 2
    if u * 2 + 1 <= size and h[u * 2 + 1] < h[t]:
        t = u * 2 + 1
    if u != t:
        heap_swap(u, t)
        down(t)

def up(u):
    while u // 2 and h[u] < h[u // 2]:
        heap_swap(u, u // 2)
        u >>= 1

# O(n)建堆
for i in range(n // 2, 0, -1):
    down(i)

def add_element(x):
    global size
    size += 1
    h[size] = x
    ph[size] = size
    hp[size] = size
    up(size)

def get_heap_top():
    return h[1]

def delete_element(k):
    global size
    heap_swap(k, size)
    size -= 1
    down(k)
    up(k)

def update_element(k, x):
    h[k] = x
    down(k)
    up(k)

# 添加元素
add_element(x)

# 堆顶元素
heap_top = get_heap_top()

# 删除第size个节点
delete_element(1)

# 删除第k个节点
delete_element(k)

# 将第k个元素赋值为x
update_element(k, x)
```

```py
import sys
from heapq import *
input = lambda:sys.stdin.readline().strip()
n = int(input())
cnt = 1
d = dict()
h = []

for i in range(n):
    op = input().split()
    if op[0]=='I':
        v = int(op[1])
        heappush(h, v)
        d[cnt] = v; cnt+=1
    elif op[0]=='D':
        k = int(op[1])
        
        if d[k]==0:
            continue
    
        v = d[k]; d[k]=0
        h.remove(v)
        heapify(h)
    elif op[0]=='C':
        k = int(op[1])     
        x = int(op[2])
        if d[k]==0:
            continue
        
        v = d[k]; d[k]=x
        h.remove(v)  
        heapify(h)
        heappush(h, x)
    elif op[0]=='DM':
        heappop(h)
    elif op[0]=='PM':
        print(h[0])
```

### 动态中位数

```py
import sys
from heapq import *
input = lambda:sys.stdin.readline().strip()

p = int(input())

for _ in range(p):
    id, m = map(int, input().split())
    print(id, (m+1)//2)
    
    cnt1, cnt2 = 0, 0
    hl, hu = [], []
    while m:
        a = list(map(int, input().split()))
        m -= len(a)
        for i in range(len(a)):
            if len(hl)==0 or len(hl)==len(hu):
                heappush(hu, -a[i])
                tmp = -heappop(hu)
                heappush(hl, tmp)
            else:
                heappush(hl, a[i])
                tmp = heappop(hl)
                heappush(hu, -tmp)
                
            cnt1+=1
            
            if cnt1&1:
                print(hl[0], end=" ")
                cnt2+=1
                if cnt2%10==0 and m!=0:
                    print()

    print()
```



### 哈希

#### (1)拉链法

```py
N = 1000005  # 假设N的值为1000005

h = [-1] * N  # 初始化h数组为-1
e = [0] * N  # 初始化e数组
ne = [0] * N  # 初始化ne数组
idx = 0  # 初始化idx为0

# 向哈希表中插入一个数
def insert(x):
    k = (x % N + N) % N
    e[idx] = x
    ne[idx] = h[k]
    h[k] = idx
    idx += 1

# 在哈希表中查询某个数是否存在
def find(x):
    k = (x % N + N) % N
    i = h[k]
    while i != -1:
        if e[i] == x:
            return True
        i = ne[i]
    return False
```

```py
N = 100003
e, ne, h = [0]*N, [0]*N, [-1]*N
idx = 0

def insert(x):
    global idx
    k = (x%N + N)%N
    e[idx] = x
    ne[idx] = h[k]
    h[k] = idx
    idx+=1
    
def query(x):
    k = (x%N + N)%N
    i = h[k]
    while i!=-1:
        if e[i]==x:
            return True
        i = ne[i]
    return False
    
n = int(input())

for i in range(n):
    s = input().split()
    op, k = s[0], int(s[1])
    if op=='I':
        insert(k)
    else:
        if query(k):
            print("Yes")
        else:
            print("No")
```

#### (2) 开放寻址法

```py
N = 1000005  # 假设N的值为1000005

h = [0] * N  # 初始化h数组

# 如果x在哈希表中，返回x的下标；如果x不在哈希表中，返回x应该插入的位置
def find(x):
    t = (x % N + N) % N
    while h[t] != 0 and h[t] != x:
        t += 1
        if t == N:
            t = 0
    return t
```

### 字符串哈希

这段代码实现了字符串的哈希算法，用于快速计算子串的哈希值。下面我来解释一下：

**题目大意：**
这段代码实现了字符串哈希算法，可以用于快速计算给定字符串中任意子串的哈希值。

**实现思路：**

1. 首先定义了两个数组 `h` 和 `p`，分别用于存储字符串的哈希值和幂次值。
2. 对于字符串的每个位置，计算其对应的哈希值并存储在数组 `h` 中。同时计算幂次值并存储在数组 `p` 中。
3. 字符串的哈希值由以下公式计算得到：$h[i] = h[i - 1] \times P + \text{ord}(str[i])$其中，$P$是一个预先定义的质数（例如 `131` 或 `13331`），$\text{ord}(str[i])$ 表示字符串中第 $i$ 个字符的 `ASCII` 值。
4. 计算子串 $str[l:r]$ 的哈希值时，使用以下公式：$ \text{get}(l, r) = h[r] - h[l - 1] \times p[r - l + 1] $其中，$h[r]$ 是子串起始位置到结束位置的哈希值，$h[l - 1]$ 是子串起始位置前一个位置的哈希值，$p[r - l + 1]$ 是子串长度对应的幂次值。

总体来说，这段代码实现了字符串哈希算法，用于快速计算给定字符串中任意子串的哈希值。

- `h` 数组存储了字符串的哈希值，即从字符串的起始位置到当前位置的子串的哈希值。
- `h[i]` 表示字符串从第一个字符到第 i 个字符（包括第 i 个字符）的子串的哈希值。
- `p[i]` 表示幂次值，即 $P^i$ 的值，其中 $P$ 是一个预先定义的质数。
- `p` 数组存储了幂次值，用于计算子串的哈希值时使用。

```py
import sys
input = lambda:sys.stdin.readline().strip()

mod = 1<<64
def get(l, r):
    return (h[r] - h[l-1]*p[r-l+1])%mod

N = int(1e5) + 10
P = 131
p = [0]*N
h = [0]*N

n, m = map(int, input().split())
s = ' ' + input()
p[0] = 1

for i in range(1, n+1):
    h[i] = (h[i-1]*P + ord(s[i]))%mod
    p[i] = (p[i-1]*P)%mod

for i in range(m):
    l1, r1, l2, r2 = map(int, input().split())
    if get(l1, r1) == get(l2, r2):
        print("Yes")
    else:
        print("No")
```

### 树状数组

楼兰图腾

```py
import sys
input=lambda:sys.stdin.readline()

M = 200010
suml, sumg = 0, 0
tr = [0]*M
n = int(input())
a = [0]+[int(_) for _ in input().split()]

def lowbit(x):
    return x&-x

def add(x,v):
    while x<M:
        tr[x]+=v
        x+=lowbit(x)
        
def query(x):
    res = 0
    while x:
        res+=tr[x]
        x-=lowbit(x)
    return res

for i in range(1,n+1):
    y=a[i]
    lw = query(y-1)
    lwr = y-1-lw # the lower element on the right side
    gr = query(n)-query(y)
    grr = (n-y) - gr
    suml += (lw*lwr)
    sumg += (gr*grr)
    add(y,1)
    
print(sumg, suml)
```

### 线段树

### [\1265. 数星星](https://www.acwing.com/problem/content/description/1267/)

**题目大意**：给定一些星星的坐标，以平面直角坐标系表示，需要统计每个星星的等级，即在该星星左下方（含正左、正下）的星星数量。

**实现思路**：

1. 使用线段树来维护每个星星的等级。
2. 构建线段树，初始时所有节点值都为0。
3. 读入每个星星的坐标，通过查询线段树统计该星星左边（含正左）的星星数量，即为该星星的等级。
4. 更新线段树的值，将当前星星的横坐标位置对应的节点值加1。
5. 统计每个等级的星星数量，输出结果。

```py
import sys
input = lambda:sys.stdin.readline()

N = 15010
M = 32010
tr = [0]*(M*4)
level = [0]*N

def pushup(u):
    tr[u] = tr[u << 1] + tr[u << 1 | 1]

def build(u, l, r):
    if l == r:
        tr[u] = 0
    else:
        mid = (l + r) >> 1
        build(u << 1, l, mid)
        build(u << 1 | 1, mid + 1, r)
        pushup(u)

def query(u, l, r, ql, qr):
    if l >= ql and r <= qr:
        return tr[u]
    mid = (l + r) >> 1
    
    if mid==l and mid==r:
        return 0
        
    res = 0
    if ql <= mid:
        res += query(u << 1, l, mid, ql, qr)
    if qr > mid:
        res += query(u << 1 | 1, mid + 1, r, ql, qr)
    return res

def modify(u, x, l, r, val):
    if l == r:
        tr[u] += val
    else:
        mid = (l + r) >> 1
        if x <= mid:
            modify(u << 1, x, l, mid, val)
        else:
            modify(u << 1 | 1, x, mid + 1, r, val)
        pushup(u)

l, r = 0, 32000
build(1, l, r)

n = int(input())

for i in range(n):
    x, _ = map(int, input().split())
    t = query(1, l, r, 0, x)
    
    level[t]+=1
    
    modify(1, x, l, r, 1)
    
for i in range(n):
    print(level[i])
```

### 最大数

```py
import sys
input = lambda:sys.stdin.readline().strip()

m = int(2e5)+2
tr = [0]*(m*4)

def pushup(u):
    tr[u] = max(tr[u<<1], tr[u<<1|1])

def modify(u, l, r, x, v):
    if l==r:
        tr[u] = v
    else:
        mid = l+r>>1
        if x<=mid:
            modify(u<<1, l, mid, x, v)
        else:
            modify(u<<1|1, mid+1, r, x, v)
        pushup(u)

def query(u, l, r, ql, qr):
    if l>=ql and r<=qr:
        return tr[u]
        
    if l==r:
        return 0
    
    res = 0
    mid = l+r>>1
    if ql<=mid:
        res = query(u<<1, l, mid, ql, qr)
    if qr>mid:
        res = max(res, query(u<<1|1, mid+1, r, ql, qr))
    
    return res
            
m, p = map(int, input().split())
n, pre = 0, 0
for i in range(m):
    op, x = input().split()
    x = int(x)
    
    if op=='Q':
        pre = query(1, 1, m, n-x+1, n)
        print(pre)
    else:
        n += 1
        modify(1, 1, m, n, (x+pre)%p)
```

### [\243. 一个简单的整数问题2](https://www.acwing.com/problem/content/description/244/)

```py
import sys
input = lambda:sys.stdin.readline().strip()

N = int(1e5)+10
M = 1<<N.bit_length() + 1
tr = [0]*M
add = [0]*M

def pushup(u):
    tr[u] = tr[u<<1] + tr[u<<1|1]

def pushdown(u, ln, rn):
    if add[u]:
        lazy(u<<1, add[u], ln)
        lazy(u<<1|1, add[u], rn)
        add[u] = 0
    
def lazy(u, v, n):
    tr[u] += v*n
    add[u] += v

def build(u, l, r):
    if l==r:
        tr[u] = w[l]
    else:
        mid = l+r>>1
        build(u<<1, l, mid)
        build(u<<1|1, mid+1, r)
        pushup(u)
    add[u] = 0

def modify(u, l, r, ml, mr, v):
    if l>=ml and r<=mr:
        lazy(u, v, r-l+1)
    else:
        mid = l+r>>1
        pushdown(u, mid-l+1, r-mid)
        if ml <= mid:
            modify(u<<1, l, mid, ml, mr, v)
        if mr > mid:
            modify(u<<1|1, mid+1, r, ml, mr, v)
        pushup(u)
        
def query(u, l, r, ql, qr):
    if l>=ql and r<=qr:
        return tr[u]
    mid = l+r>>1
    pushdown(u, mid-l+1, r-mid)
    res = 0
    if ql<=mid:
        res = query(u<<1, l, mid, ql, qr)
    if qr>mid:
        res += query(u<<1|1, mid+1, r, ql, qr)
    
    return res


# 主程序开始
n, m = map(int, input().split())  # 输入节点数量n和操作数量m
w = list(map(int, input().split()))  # 输入节点的初始权值

build(1, 0, n-1)

res = []  # 存储查询结果
while m:
    m -= 1
    op, opt = input().split(maxsplit=1)
    if op == 'Q':
        l, r = map(int, opt.split())
        l-=1; r-=1
        res.append(str(query(1, 0, n-1, l, r)))  # 查询区间和并将结果添加到res列表中
    else:
        l, r, val = map(int, opt.split())
        l-=1; r-=1
        modify(1, 0, n-1, l, r, val)  # 修改区间的值

print("\n".join(res))  # 输出查询结果

```

### [维护序列](https://www.luogu.com.cn/problem/P2023)

**题目大意**：给定一个长度为 `n` 的数列 `𝑎𝑖`，有三种操作：1. 格式`1 t g c`，将区间 `[t, g]` 内的数乘以 `c`；2. 格式`2 t g c`，将区间 `[t, g]` 内的数加上 `c`；3. 格式3 t g，求区间 `[t, g]` 内数的和，并输出结果模 `p` 的值。

**实现思路**：

1. 使用线段树来维护区间和，每个节点保存区间内数的和。
2. 对于每种操作，根据操作类型分别进行处理。
3. 对于格式1和格式2，采用延迟标记来实现区间修改。对于格式3，采用递归的方式查询区间和。
4. 实现具体的操作函数，包括建树、修改和查询。
5. 循环读入每个操作并进行处理，最后输出查询结果。

```py
import sys

input = lambda: sys.stdin.readline().strip()

N = int(1e5) + 1
M = 1 << N.bit_length() + 1

tr = [0] * M
add = [0] * M
mul = [0] * M


def lazy(u, n, ad, mu):
    tr[u] = (tr[u] * mu + n * ad) % p
    add[u] = (add[u] * mu + ad) % p
    mul[u] = (mul[u] * mu) % p


def pushup(u):
    tr[u] = tr[u << 1] + tr[u << 1 | 1]


def pushdown(u, ln, rn):
    if add[u] != 0 or mul[u] != -1:
        lazy(u << 1, ln, add[u], mul[u])
        lazy(u << 1 | 1, rn, add[u], mul[u])
        add[u] = 0
        mul[u] = 1


def build(u, l, r):
    if l == r:
        tr[u] = w[l]
    else:
        mid = l + r >> 1
        build(u << 1, l, mid)
        build(u << 1 | 1, mid + 1, r)
        pushup(u)
    add[u] = 0
    mul[u] = 1


def modify(u, l, r, ml, mr, ad, mu):
    if l >= ml and r <= mr:
        lazy(u, r - l + 1, ad, mu)
    else:
        mid = l + r >> 1
        pushdown(u, mid - l + 1, r - mid)
        if ml <= mid:
            modify(u << 1, l, mid, ml, mr, ad, mu)
        if mr > mid:
            modify(u << 1 | 1, mid + 1, r, ml, mr, ad, mu)
        pushup(u)


def query(u, l, r, ql, qr):
    if l >= ql and r <= qr:
        return tr[u]

    mid = l + r >> 1
    pushdown(u, mid - l + 1, r - mid)
    res = 0
    if ql <= mid:
        res += query(u << 1, l, mid, ql, qr)
    if qr > mid:
        res += query(u << 1 | 1, mid + 1, r, ql, qr)
    return res % p


n, p = map(int, input().split())
w = [0] + list(map(int, input().split()))
m = int(input())

build(1, 1, n)
res = []
for i in range(m):
    op, par = input().split(maxsplit=1)
    if op == "1":
        l, r, mu = map(int, par.split())
        modify(1, 1, n, l, r, 0, mu)
    elif op == "2":
        l, r, ad = map(int, par.split())
        modify(1, 1, n, l, r, ad, 1)
    else:
        l, r = map(int, par.split())
        res.append(str(query(1, 1, n, l, r)))

print(*res, sep="\n")
```





### [\246. 区间最大公约数](https://www.acwing.com/problem/content/description/247/)

```py
from math import gcd
from sys import stdin
input = stdin.readline

def pushup(u):
    tr[u] = tr[u<<1] + tr[u<<1|1]
    gd[u] = gcd(gd[u<<1], gd[u<<1|1])

def build(u, l, r):
    if l==r:
        tr[u] = gd[u] = arr[l]-arr[l-1]
    else:
        mid = l+r>>1
        build(u<<1, l, mid)
        build(u<<1|1, mid+1, r)
        pushup(u)

def modify(u, l, r, x, v):
    if l==r:
        tr[u] += v
        gd[u] += v
    else:
        mid = l+r>>1
        if x<=mid:
            modify(u<<1, l, mid, x, v)
        else:
            modify(u<<1|1, mid+1, r, x, v)
        pushup(u)

def query(u, l, r, ql, qr):
    if l>=ql and r<=qr:
        return (tr[u], gd[u])
        
    mid = l+r>>1
    a, b = [0, 0], [0, 0]
    if ql<=mid:
        a = query(u<<1, l, mid, ql, qr)
    if qr>mid:
        b = query(u<<1|1, mid+1, r, ql, qr)
    return (a[0]+b[0], gcd(a[1], b[1]))
    


n, m = map(int, input().split())
arr = [0] + list(map(int, input().split()))
M = 1<<n.bit_length() + 1
tr = [0]*M
gd = [0]*M

build(1, 1, n)
res = []

for i in range(m):
    op, par = input().split(maxsplit=1)
    if op=='Q':
        l, r = map(int, par.split())
        ans = gcd(query(1, 1, n, 1, l)[0], query(1, 1, n, l+1, r)[1]) if l+1<=r else query(1, 1, n, 1, l)[0]
        res.append(str(ans))
    else:
        l, r, v = map(int, par.split())
        modify(1, 1, n, l, v)
        if r+1<=n:
            modify(1, 1, n, r+1, -v)

print(*res, sep='\n')
        
```



### 类实现线段树

```py
# 定义树节点，l,r, val表示该节点记录的是区间[l, r]的最大值是val
class Tree():
    def __init__(self):
        self.l = 0
        self.r = 0
        self.lazy = 0
        self.val = 0

# 二叉树是堆形式，可以用一维数组存储，注意数组长度要开4倍空间
tree = [Tree() for i in range(10*4)]

# 建树，用cur<<1访问左子树，cur<<1|1访问右子树，位运算操作很方便
def build(cur, l, r):
    tree[cur].l, tree[cur].r, tree[cur].lazy, tree[cur].val = l, r, 0, 0
    # 当l==r的时候结束递归
    if l < r:
        mid = l + r >> 1
        build(cur<<1, l, mid)
        build(cur<<1|1, mid+1, r)

# 当子节点计算完成后，用子节点的值来更新自己的值
def pushup(cur):
    tree[cur].val = max(tree[cur<<1].val, tree[cur<<1|1].val)

# 单点更新
def add(cur, x, v):
    if tree[cur].l == tree[cur].r:
        tree[cur].val += v
    else:
        mid = tree[cur].r + tree[cur].l >> 1
        if x > mid:
            add(cur>>1|1, x, v)
        else:
            add(cur<<1, x, v)
        pushup(cur)

# 将lazy标记向下传递一层
def pushdown(cur):
    if tree[cur].lazy:
        lazy = tree[cur].lazy
        tree[cur<<1].lazy += lazy
        tree[cur<<1|1].lazy += lazy
        tree[cur<<1].val += lazy
        tree[cur<<1|1].val += lazy
        tree[cur].lazy = 0

# 区间更新
def update(cur, l, r, v):
    if l <= tree[cur].l and tree[cur].r <= r:
        tree[cur].lazy += v
        tree[cur].val += v
        return
    if r < tree[cur].l or l > tree[cur].r:
        return
    if tree[cur].lazy:
        pushdown(cur)
    update(cur<<1, l, r, v)
    update(cur<<1|1, l, r, v)
    pushup(cur)

# 区间查询
def query(cur, l, r):
    if l <= tree[cur].l and tree[cur].r <= r:
        return tree[cur].val
    if tree[cur].l > r or tree[cur].r < l:
        return 0
    if tree[cur].lazy:
        pushdown(cur)
    return max(query(cur<<1, l, r), query(cur<<1|1))

# 测试
# -----
#        ---
#  -------
#   --
#         --

build(1, 1, 10)
update(1, 1, 5, 1)
update(1, 7, 10, 1)
update(1, 2, 8, 1)
update(1, 3, 4, 1)
update(1, 9, 10, 1)
print(query(1, 1, 10))
```

```py
def pushup(u):
    tr[u] = tr[u << 1] + tr[u << 1 | 1]

def build(u, l, r):
    if l == r:
        tr[u] = 0
    else:
        mid = (l + r) >> 1
        build(u << 1, l, mid)
        build(u << 1 | 1, mid + 1, r)
        pushup(u)

def query(u, l, r, ql, qr):
    if l >= ql and r <= qr:
        return tr[u]
    mid = (l + r) >> 1
    
    if mid==l and mid==r:
        return 0
        
    res = 0
    if ql <= mid:
        res = query(u << 1, l, mid, ql, qr)
    if qr > mid:
        res += query(u << 1 | 1, mid + 1, r, ql, qr)
    return res

def modify(u, x, l, r, val):
    if l == r:
        tr[u] += val
    else:
        mid = (l + r) >> 1
        if x <= mid:
            modify(u << 1, x, l, mid, val)
        else:
            modify(u << 1 | 1, x, mid + 1, r, val)
        pushup(u)
```

##  搜索深入

### 池塘计数

```py
import sys
from collections import deque
input = lambda:sys.stdin.readline().strip()

N = int(1e3+10)
M = N*N
g = [0]*N
cnt=0
vis = [ [False]*N for _ in range(N) ]

def bfs(x, y):
    q=deque()
    q.append( (x,y) )
    while q:
        tx, ty = q[0]
        q.popleft()
        for i in range(-1, 2):
            for j in range(-1, 2):
                if i==0 and j==0: 
                    continue
                xx, yy = tx+i, ty+j
                if xx<0 or xx>=n or yy<0 or yy>=m or vis[xx][yy] or g[xx][yy]=='.': 
                    continue
                vis[xx][yy]=True
                q.append( (xx,yy) )

n, m = map(int, input().split())
for i in range(n):
    g[i]=input()

for i in range(n):
    for j in range(m):
        if g[i][j]!='W' or vis[i][j]: 
            continue
        bfs(i, j)
        cnt+=1
print(cnt)
```

### 城堡问题

```py
import sys
from collections import deque
input = lambda:sys.stdin.readline().strip()
N = 55
g = []
vis = [[False]*N for _ in range(N)]
area = 0
cnt=0

def bfs(x, y):
    vis[x][y]=True
    q = deque()
    q.append((x, y))
    dx, dy = [0, -1, 0, 1], [-1, 0, 1, 0]
    ans=1
    while q:
        tx, ty = q.popleft()
        for i in range(4):
            xx = tx+dx[i]
            yy = ty+dy[i]
            if xx<0 or xx>=n or yy<0 or yy>=m or vis[xx][yy]:
                continue
            if (g[tx][ty]>>i)&1:
                continue
            ans+=1
            vis[xx][yy]=True
            q.append((xx,yy))
    return ans

n, m = map(int, input().split())
for _ in range(n):
    g.append(list(map(int, input().split())))

for i in range(n):
    for j in range(m):
        if vis[i][j]:
            continue
        area = max(area, bfs(i, j))
        cnt+=1
print(cnt)
print(area)
```

### 山峰和山谷

```py
import sys
from collections import deque
N = 1010
g = []
vis = [[0]*N for _ in range(N)]
pek, val = 0, 0

def bfs(x, y):
    global higher, lower
    vis[x][y]=True
    q = deque()
    q.append((x,y))
    while q:
        tx,ty = q.popleft()
        for i in range(-1, 2):
            for j in range(-1, 2):
                if i==0 and j==0:
                    continue
                xx, yy = tx+i, ty+j
                if xx<0 or xx>=n or yy<0 or yy>=n:
                    continue
                if g[xx][yy]!=g[tx][ty]:
                    if g[xx][yy]>g[tx][ty]:
                        higher=True
                    elif g[xx][yy]<g[tx][ty]:
                        lower=True
                elif not vis[xx][yy]:
                    vis[xx][yy]=True
                    q.append((xx,yy))
                    
higher, lower = False, False
n = int(input())
for _ in range(n):
    g.append(list(map(int, input().split())))
    
# print(g)
for i in range(n):
    for j in range(n):
        if vis[i][j]:
            continue
        higher, lower = False, False
        bfs(i, j)
        if not higher:pek+=1
        if not lower:val+=1
print(pek, val)
```

### BFS 

#### [P1038 [NOIP2003 提高组] 神经网络](https://www.luogu.com.cn/problem/P1038)

- 题目大意：兰兰提出了一个神经网络模型，该模型可以表示为一个有向图，图中的节点称为神经元，节点之间的连接代表神经元之间的联系。神经元按照一定的顺序排列，构成整个神经网络。每个神经元都有一个状态和阈值，当神经元处于兴奋状态时，会向其他神经元传送信号。


- 实现思路：

1. 首先，根据输入的网络结构信息，构建神经元之间的连接关系。使用字典g来表示神经元之间的连接关系，以及字典wei来表示连接神经元之间的边的权值。
2. 然后，根据输入的初始状态和阈值信息，初始化神经元的状态。同时，记录输入层神经元的初始状态，如果神经元的状态不为0，则将其加入队列q中。
3. 利用广度优先搜索（BFS）算法，遍历神经元，根据模型公式更新每个神经元的状态。同时，更新神经元的状态时，考虑到边的权值以及神经元的阈值。
4. 最后，输出输出层神经元的状态。如果输出层神经元的最后状态均小于等于0，则输出"NULL"，否则按照编号从小到大顺序输出输出层神经元的状态。

```py
from collections import deque, defaultdict

maxn = 1e3 + 10
c = [0] * int(maxn)
u = [0] * int(maxn)
wei = defaultdict(lambda: defaultdict(int))
vis = [False] * int(maxn)
beginer = [False] * int(maxn)
g = defaultdict(list)

n, p = map(int, input().split())
en = []
q = deque()

for i in range(1, n + 1):
    c[i], u[i] = map(int, input().split())
    if c[i] != 0:
        q.append(i)
        beginer[i] = True
    else:
        en.append(i)

if not en:
    for i in range(1, n + 1):
        print(i, c[i])
    exit()

for _ in range(p):
    s, e, w = map(int, input().split())
    g[s].append(e)
    wei[s][e] = w

while q:
    t = q.popleft()
    if not beginer[t]:
        c[t] -= u[t]

    base = c[t]
    if base > 0:
        for j in g[t]:
            c[j] += base * wei[t][j]
            if not vis[j]:
                q.append(j)
                vis[j] = True

ans = 0
for i in en:
    if c[i] > 0 and not g[i]:
        print(i, c[i])
        ans += 1

if not ans:
    print("NULL")
```

