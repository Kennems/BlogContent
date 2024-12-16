---
title : '算法笔记（五）——DP（Python实现）'
date : 2024-04-09T15:37:01+08:00
lastmod: 2024-04-09T15:37:01+08:00
description : "算法笔记（五）——DP（Python实现）" 
image : img/cat.jpg
draft : false    
categories : ["算法笔记"]
tags : ["算法笔记","Python"]
# password : leetcode
---

#  算法笔记（五）——DP（Python实现）

### DP

#### 数字三角形

```py
f=[]
n=int(input())
for _ in range(n):
    f.append([int(x) for x in input().split()])

for i in range(n-2,-1,-1):
    for j in range(i+1):
        f[i][j]=max(f[i+1][j], f[i+1][j+1])+f[i][j]
print(f[0][0])
```

### 背包

空间优化成1维之后，只有完全背包问题的体积是从小到大循环的

#### 01背包

```py
N = int(1e3+10)
f=[ 0 for _ in range(N) ]
n,v=map(int,input().split())
for i in range(n):
    vi,wi=map(int,input().split())
    for j in range(v, vi-1,-1):
        f[j]=max(f[j],f[j-vi]+wi)
print(f[v])
```

#### 多重背包

##### 单调队列

```py
MN = int(2e4+10)
f=[0 for _ in range(MN)]
q=[0 for _ in range(MN)]
g=[0 for _ in range(MN)]

N,V = map(int, input().split())

for i in range(N):
    v,w,s=map(int, input().split())
    g=f[:]
    for j in range(v):
        hh,tt=0,-1
        for k in range(j,V+1,v):
            while hh<=tt and q[hh]<k-s*v:
                hh+=1
            while hh<=tt and g[q[tt]]+(k-q[tt])//v*w <= g[k]:
                tt-=1
            tt+=1
            q[tt]=k
            f[k]=g[q[hh]]+(k-q[hh])//v*w
            
print(f[V])
```

#### 二维费用背包

```py
N = int(1e2+10)
f=[[0]*N for _ in range(N)]

n,V,M = map(int , input().split())

for i in range(n):
    v,m,w=map(int , input().split())
    for j in range(V,v-1,-1):
        for k in range(M, m-1, -1):
            f[j][k]=max(f[j][k], f[j-v][k-m]+w)
             
print(f[V][M])
```

#### 宠物小精灵

- 题目大意：小智在野外捕捉宠物小精灵，他带了一些精灵球和皮卡丘，精灵球可以捕捉小精灵，但每捕捉一个小精灵都会消耗精灵球和减少皮卡丘的体力。现在给定小智拥有的精灵球数量、皮卡丘的初始体力值以及每个小精灵需要的精灵球数量和对皮卡丘造成的伤害数目，问小智最多能捕捉多少个小精灵，并且在这种情况下，皮卡丘的剩余体力值最多是多少。


- 实现思路：

1. 使用动态规划，定义状态 f[i][j] 表示使用 i 个精灵球时，对皮卡丘造成 j 伤害时能够捕捉到的小精灵数量。
2. 遍历每个野生小精灵，更新状态数组 f。
3. 最后输出 `f[n][m-1]` 表示捕捉到的小精灵数量，然后再计算皮卡丘的剩余体力值。

```py
N = int(1e3+10)
M = int(5e2+10)
f=[[0]*M for _ in range(N)]

n,m,kk = map(int, input().split())

for i in range(kk):
    v1,v2=map(int, input().split())
    for j in range(n, v1-1, -1):
        for k in range(m-1,v2-1,-1):
            f[j][k]=max(f[j][k], f[j-v1][k-v2]+1)
            
print(f[n][m-1], end=" ")
t=m-1
while t>0 and f[n][m-1]==f[n][t-1]:
    t-=1
    
print(m-t)
```

#### 潜水

- 最多


- 恰好


- 最少

- 题目大意：潜水员需要使用特殊装备下潜，装备有两种气缸，一种氧气，一种氮气，每个气缸有一定的重量和气体容量。下潜的深度需要一定数量的氧气和氮气。现在给定潜水员需要的氧气和氮气的量，以及每个气缸的氧气、氮气容量和重量，求完成工作所需气缸的总重的最低值。


实现思路：
1. 使用动态规划，定义状态 f[i][j] 表示满足氧气需求为 i，氮气需求为 j 时的最小气缸总重量。
2. 遍历每个气缸，更新状态数组 f。
3. 最后输出 `f[m][n]` 表示完成工作所需的气缸的总重的最低值。


```py
N = int(1e2+10)
INF = 0x3f3f3f3f
f=[ [INF]*N for _ in range(N) ]
f[0][0]=0

m,n=map(int, input().split())
k=int(input())

for i in range(k):
    a,b,c=map(int, input().split())
    for j in range(m,-1,-1):
        for k in range(n,-1,-1):
            f[j][k]=min(f[j][k], f[max(j-a, 0)][max(k-b, 0)]+c)
            
print(f[m][n])
```

#### 庆功会

- 题目大意：为了庆祝班级在学校运动会上取得第一名，班主任决定开一场庆功会，并拨款购买奖品犒劳运动员。需要选择购买的奖品，使得购买的奖品的总价值最大，且不超过拨款金额。


- 实现思路：

1. 使用动态规划，定义状态 f[i] 表示拨款金额为 i 时可以购买的奖品的最大总价值。
2. 遍历每个奖品，更新状态数组 f。
3. 最后输出 f[m]，即拨款金额为 m 时可以购买的奖品的最大总价值。

```py
N = int(6e3+10)
f=[0 for _ in range(N)]
n,m = map(int, input().split())

for i in range(n):
    v,w,s=map(int, input().split())
    for j in range(1,s+1):
        for k in range(m, v-1, -1):
            f[k]=max(f[k], f[k-v]+w)
            
            
print(f[m])
```

#### 分组背包

分组背包的顺序：

1. **物品组**
2. **体积**
3. **决策**

```py
N = int(1e2+10)
f=[0 for _ in range(N)]
v=[0 for _ in range(N)]
w=[0 for _ in range(N)]

N,V = map(int, input().split())
for i in range(N): # 物品组
    s=int(input())
    for j in range(s):
        v[j],w[j]=map(int, input().split())
    for j in range(V,-1,-1): #体积
        for k in range(s): #决策
            if j>=v[k]:
                f[j]=max(f[j], f[j-v[k]]+w[k])

print(f[V])
```

#### 机器分配

多重背包变种

- 题目大意：总公司有M台相同的高效设备，要分给N个分公司。每个分公司根据分配的设备数量能够为国家带来一定的盈利。问如何分配这M台设备才能使国家得到的盈利最大，输出最大盈利值和分配方案。

- 实现思路：这是一个经典的背包问题，可以使用动态规划来解决。定义状态`dp[i][j]`表示前i个分公司中，分配j台设备所能获得的最大盈利。转移方程为`dp[i][j] = max(dp[i-1][j-k] + w[i][k])`，其中k表示第i个分公司分配的设备数量，`0 <= k <= j`。最后遍历求解得到最大盈利值，并记录分配方案。

```py
N = 100
f=[0 for _ in range(N)]
w=[[0]*N for _ in range(N)]
c=[[0]*N for _ in range(N)]

n,m=map(int, input().split())
for i in range(n):
    w[i]=[0]+[int(x) for x in input().split()]
    
for i in range(n):
    for j in range(m,-1,-1):
        for k in range(1,j+1):
            if f[j-k]+w[i][k] > f[j]:
                f[j]=f[j-k]+w[i][k]
                c[i][j]=k
                
print(f[m])

t=m
for i in range(n-1,-1,-1):
    print(i+1, c[i][t])
    t-=c[i][t]

```

#### 金明的预算方案

**题目描述**：金明想购买物品，其中有主件和附件。每个主件可以有0个、1个或2个附件，附件不能有从属于自己的附件。金明希望在不超过N元（可以等于N元）的前提下，使每件物品的价格与重要度的乘积的总和最大。物品的价格都是10元的整数倍。

**实现思路**：这个问题可以使用动态规划来解决。首先，需要将主件和附件分开处理。对于每一个物品，分别考虑不同附件数量的情况，计算可能的价格与重要度乘积的总和，然后更新动态规划数组f。最终，返回f[n]即可得到结果。

```py
N = 80
M = 32010
f=[0 for _ in range(M)]
mas=[[0,0] for _ in range(N)]
ser=[[] for _ in range(N)]

n,m=map(int,input().split())

for i in range(1,m+1):
    v,p,q=map(int, input().split())
    if q==0:
        mas[i]=[v,v*p]
    else:
        ser[q].append((v, v*p))
        
        
for i in range(1,m+1):
    for j in range(n, -1, -1):
        for k in range( (1 << len(ser[i]) )):
            v,p=mas[i]
            for l in range(len(ser[i])):
                if k>>l & 1:
                    v+=ser[i][l][0]
                    p+=ser[i][l][1]
                    
            if j>=v:
                f[j]=max(f[j], f[j-v]+p)
                
print(f[n])
```

#### 摘花生

```py
N = 105    
f=[ [0]*N for _ in range(N)]
t=int(input())
for _ in range(t):
    r,c=map(int, input().split())    
    for i in range(1,r+1):
        f[i] = [0] + [int(x) for x in input().split()]
        for j in range(1,c+1):
            f[i][j]+=max(f[i-1][j], f[i][j-1])
    print(f[r][c])
```

#### 最小通行费

```py
N = int(1e3+10)
INF = 0x3f3f3f3f
f = [[INF]*N for _ in range(N)]
f[1][0]=f[0][1]=0
n=int(input())
for i in range(1,n+1):
    f[i]=[INF]+[int(x) for x in input().split()]
for i in range(1,n+1):
    for j in range(1,n+1):
        f[i][j]+=min(f[i-1][j],f[i][j-1])
print(f[n][n])
```

#### 方格

```py
N = 15
g = [[0]*N for _ in range(N)]
f = [[[[0]*N for _ in range(N)] for _ in range(N)] for _ in range(N)]
n=int(input())
while True:
    r,c,x=map(int,input().split())
    if r==0 and c==0 and x==0:
        break
    g[r][c]=x
for i in range(1,n+1):
    for j in range(1,n+1):
        for k in range(1,n+1):
            for l in range(1,n+1):
                if i==k and j==l:
                    f[i][j][k][l] = max(f[i-1][j][k-1][l], f[i-1][j][k][l-1], f[i][j-1][k-1][l], f[i][j-1][k][l-1]) + g[i][j]
                else:
                    f[i][j][k][l] = max(f[i-1][j][k-1][l], f[i-1][j][k][l-1], f[i][j-1][k-1][l], f[i][j-1][k][l-1]) + g[i][j] + g[k][l]
print(f[n][n][n][n])
```

```py
N = 55
g=[[0]*N for _ in range(N)]
f=[[[[0]*N for _ in range(N)] for _ in range(N)] for _ in range(N)]

m,n=map(int, input().split())
for i in range(1,m+1):
    g[i]=[0]+[int(x) for x in input().split()]

for i in range(1,m+1):
    for j in range(1,n+1):
        for k in range(1,m+1):
            for l in range(1,n+1):
                if i==k and j==l:
                    f[i][j][k][l]=max(f[i-1][j][k-1][l], f[i-1][j][k][l-1], f[i][j-1][k-1][l], f[i][j-1][k][l-1])+g[i][j]
                else:
                    f[i][j][k][l]=max(f[i-1][j][k-1][l], f[i-1][j][k][l-1], f[i][j-1][k-1][l], f[i][j-1][k][l-1])+g[i][j]+g[k][l]
print(f[m][n][m][n])
```

### LIS & LCS

#### LIS

```py
N=int(1e3+10)
f=[1 for _ in range(N)]
a=[]
n=int(input())
a=[0]+[int(x) for x in input().split()]
for i in range(1,n+1):
    for j in range(1,i):
        if a[i]>a[j]:
            f[i]=max(f[i],f[j]+1)
ans = 0
for i in range(1,n+1):
    ans = max(ans, f[i])
print(ans)
```

#### LCS

```py
N = int(1e3+10)
f=[[0]*N for _ in range(N)]
a=""
b=""
n,m=map(int, input().split())
a=input()
b=input()
for i in range(n):
    for j in range(m):
        f[i][j]=max(f[i-1][j], f[i][j-1])
        if a[i]==b[j]:
            f[i][j]=f[i-1][j-1]+1
print(f[n-1][m-1])
```

**回溯**寻找所有的最长公共子序列

```py
def lcs_length(X, Y):
    m, n = len(X), len(Y)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if X[i - 1] == Y[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    return dp


def find_all_lcs(X, Y, dp):
    def backtrack(i, j):
        if i == 0 or j == 0:
            return {""}
        elif X[i - 1] == Y[j - 1]:
            return {lcs + X[i - 1] for lcs in backtrack(i - 1, j - 1)}
        else:
            lcs_set = set()
            if dp[i - 1][j] >= dp[i][j - 1]:
                lcs_set.update(backtrack(i - 1, j))
            if dp[i][j - 1] >= dp[i - 1][j]:
                lcs_set.update(backtrack(i, j - 1))
            return lcs_set
    
    return backtrack(len(X), len(Y))

```

#### 最大上升子序列和

```py
import copy
a=[]
f=[]
ans=0

n=int(input())
a=[int(x) for x in input().split()]
f=copy.deepcopy(a)
for i in range(n):
    for j in range(i):
        if a[i]>a[j]:
            f[i]=max(f[i], f[j]+a[i])
            
for i in range(n):
    ans = max(ans, f[i])
print(ans)

N = int(1e3+10)
f=[0 for _ in range(N)]
ans = 0

n=int(input())
a=[0]+[int(x) for x in input().split()]

for i in range(1,n+1):
    f[i]=a[i]
    for j in range(1,i):
        if a[i]>a[j]:
            f[i]=max(f[i], f[j]+a[i])
    ans = max(ans, f[i])
    
print(ans)
```

#### 最大上升子序列II

```py
N = int(1e5+10)
INF = 0x3f3f3f3f
q=[INF for _ in range(N)]
ans=0

n=int(input())
a=[0]+[int(x) for x in input().split()]

for i in range(1,n+1):
    l,r=0,i
    while l<r:
        mid = (l+r+1)>>1
        if q[mid]<a[i]:
            l=mid
        else:
            r=mid-1
    ans = max(ans, l+1)
    q[l+1]=min(q[l+1], a[i])
    
print(ans)
```

```py
N = int(1e5+10)
q=[0 for _ in range(N)]
len = 0

n=int(input())
a=[0]+[int(x) for x in input().split()]

for i in range(1, n+1):
    l,r=0,len
    while l<r:
        mid = (l+r+1)>>1
        if q[mid]<a[i]:
            l=mid
        else:
            r=mid-1
    len=max(len, l+1)
    q[l+1]=a[i]
    
print(len)
```

```py
N = int(1e5+10)
q=[0 for _ in range(N)]
len = 0

n=int(input())
a=[0]+[int(x) for x in input().split()]
for i in range(1,n+1):
    l,r=0,len
    while l<r:
        mid = (l+r+1)>>1
        if q[mid]>=a[i]:
            r=mid-1
        else:
            l=mid
    len = max(len, l+1)
    q[l+1]=a[i]

print(len)
```

二分原则：

有单调性，并且二分之后能保持单调性

#### 怪盗基德的滑翔翼

- **题意：**怪盗基德需要逃跑，城市中有N幢建筑排成一条线，每幢建筑的高度各不相同。他希望尽可能多地经过不同建筑的顶部，以减缓下降时的冲击力，减少受伤的可能性。他可以选择一个方向逃跑，但不能中途改变方向，且只能从较高的建筑滑翔到较低的建筑。怪盗基德最多可以经过多少幢不同建筑的顶部（包含初始时的建筑）。

- **实现思路：**可以使用动态规划来解决。首先，对于每一座建筑，分别计算以该建筑为终点的最长递增子序列长度，得到从左往右和从右往左的两个最长递增子序列数组。然后将这两个数组对应位置的最大值相加，再减去1（因为初始位置也算一座建筑），即为怪盗基德最多可以经过的不同建筑数量。

```py
N = int(1e3+10)
f=[0 for _ in range(N)]

t=int(input())
while t:
    t-=1
    ans=0
    n=int(input())
    a=[0]+[int(x) for x in input().split()]
    for i in range(1,n+1):
        f[i]=1
        for j in range(1,i):
            if a[i]>a[j]:
                f[i]=max(f[i], f[j]+1)
        ans = max(ans, f[i])
    for i in range(n,0,-1):
        f[i]=1
        for j in range(n,i,-1):
            if a[i]>a[j]:
                f[i]=max(f[i], f[j]+1)
        ans = max(ans, f[i])
    print(ans)
```

#### 登山

```py
N = int(1e3+10)
f=[1 for _ in range(N)]
g=[1 for _ in range(N)]
ans = 0

n=int(input())
a=[0]+[int(x) for x in input().split()]

for i in range(1,n+1):
    for j in range(1,i):
        if a[i]>a[j]:
            f[i]=max(f[i], f[j]+1)
            
for i in range(n,0,-1):
    for j in range(n,i,-1):
        if a[i]>a[j]:
            g[i]=max(g[i], g[j]+1)
        
for i in range(1,n+1):
    ans = max(ans, f[i]+g[i]-1)
    
print(ans)
```

#### 合唱队形

```py
N = int(1e3+10)
f=[1 for _ in range(N)]
g=[1 for _ in range(N)]
ans = 0

n=int(input())
a=[0]+[int(x) for x in input().split()]

for i in range(1,n+1):
    for j in range(1,i):
        if a[i]>a[j]:
            f[i]=max(f[i], f[j]+1)
            
for i in range(n,0,-1):
    for j in range(n,i,-1):
        if a[i]>a[j]:
            g[i]=max(g[i], g[j]+1)

for i in range(1,n+1):
    ans = max(ans, f[i]+g[i]-1)
    
print(n-ans)
```

#### 友好城市

```py
N = int(5e3+10)
a=[(-1,-1)]
f=[1 for _ in range(N)]
ans = 0

n=int(input())
for i in range(n):
    x,y=map(int, input().split())
    a.append((x,y))
    
a.sort(key=lambda x:x[0])

for i in range(1,n+1):
    for j in range(1,i):
        if a[i][1]>a[j][1]:
            f[i]=max(f[i], f[j]+1)
    ans = max(f[i], ans)
    
print(ans)
```

#### 拦截导弹

**贪心证明**

- **题意**：某国为了防御敌国的导弹袭击，发展出一种导弹拦截系统，但有缺陷：每一发炮弹都不能高于前一发的高度。给定导弹依次飞来的高度，计算这套系统最多能拦截多少导弹，并且如果要拦截所有导弹，最少需要配备多少套这种导弹拦截系统。

- **实现思路**：首先使用动态规划求解最多能拦截的导弹数，然后再利用贪心算法求解最少需要配备的系统数。具体实现时，先从左往右依次计算每个位置能拦截的最大导弹数，然后再从右往左依次计算每个位置能拦截的最大导弹数。接着，利用贪心算法，遍历导弹高度，如果当前高度大于已有系统的高度，则增加系统数；否则，更新已有系统的高度。最终得到最多能拦截的导弹数和最少需要配备的系统数。

```py
N = int(1e3+10)
f=[1 for _ in range(N)]
g=[0 for _ in range(N)]
ans = 0
cnt = 0

a=[0]+[int(x) for x in input().split()]
n=len(a)

for i in range(n-1,0,-1):
    for j in range(n-1,i,-1):
        if a[i]>=a[j]:
            f[i]=max(f[i], f[j]+1)
    ans = max(ans, f[i])

print(ans)

for i in range(1, n):
    k=0
    while k<cnt and g[k]<a[i]:
        k+=1
    if k>=cnt:
        cnt+=1
    g[k]=a[i]
    
print(cnt)
```

#### 导弹防御系统

- **题意：**R国更新了导弹防御系统，一套系统的导弹拦截高度要么一直严格单调上升，要么一直严格单调下降。给定即将袭来的一系列导弹的高度，请你求出至少需要多少套防御系统，才能将它们全部击落。

- **实现思路：**可以利用深度优先搜索（DFS）来解决。首先定义两个数组`up`和`down`，分别表示上升序列和下降序列。然后使用DFS进行状态搜索，每次尝试将当前导弹加入上升序列或下降序列，并更新当前的防御系统数量。最终得到至少需要的防御系统数量。代码中的`dfs`函数用于进行状态搜索，尝试将当前导弹加入上升序列或下降序列，并递归调用下一个导弹的处理。最终输出最少需要的防御系统数量。

```py
N = int(1e2+10)
up=[0 for _ in range(N)]
down=[0 for _ in range(N)]
ans=0

def dfs(u, su, sd):
    global ans
    if su + sd>=ans:
        return
    if u==n:
        ans=su+sd
        return
    
    k=0
    while k<su and up[k]>=a[u]:
        k+=1
    t=up[k]
    up[k]=a[u]
    if k>=su:
        dfs(u+1, su+1, sd)
    else:
        dfs(u+1, su, sd)
    up[k]=t
    
    k=0
    while k<sd and down[k]<=a[u]:
        k+=1
    t=down[k]
    down[k]=a[u]
    if k>=sd:
        dfs(u+1, su, sd+1)
    else:
        dfs(u+1, su, sd)
    down[k]=t
    
while True:
    n=int(input())
    ans = n
    if n==0:
        break
    a=[int(x) for x in input().split()]


    dfs(0,0,0)
    print(ans)
```

#### 最长公共上升子序列

- 题意：熊大妈的奶牛们在小沐沐的指导下研究了最长公共上升子序列，即两个数列中都包含一段位置不一定连续的严格递增的数段，要求找出最长的这样的子序列。现在你需要告诉奶牛们最长公共上升子序列的长度。


- 实现思路：可以使用动态规划来解决。定义一个二维数组f，其中f[i][j]表示数列A的前i个元素和数列B的前j个元素中，以数列A的第i个元素和数列B的第`j`个元素结尾的最长公共上升子序列的长度。然后使用动态规划的方式填表，最终找出最长的公共上升子序列的长度。

  - 代码中的f[i][j]的状态转移方程如下：

    - 如果a[i]等于b[j]，则f[i][j]等于`f[i-1][j-1]+1`（表示在a[i]和b[j]相等的情况下可以构成更长的公共上升子序列）。

    - 如果a[i]大于b[j]，则f[i][j]等于`f[i-1][j]`（表示a[i]不在公共上升子序列中）。

    - 否则，f[i][j]等于f[i][j-1]（表示b[j]不在公共上升子序列中）。


```py
N = int(3e3+10)
f=[[0]*N for _ in range(N)]
ans=0

n=int(input())
a=[0]+[int(x) for x in input().split()]
b=[0]+[int(x) for x in input().split()]

for i in range(1,n+1):
    maxv=1
    for j in range(1,n+1):
        f[i][j]=f[i-1][j]
        if a[i]==b[j]:
            f[i][j]=max(f[i][j], maxv)
        if a[i]>b[j]:
            maxv=max(maxv, f[i][j]+1)
for i in range(1,n+1):
    ans = max(ans, f[n][i])
    
print(ans)
```

### 状态机模型

闫氏DP分析法——状态机分析法

#### 大盗阿福

```py
N = int(1e5+10)


t=int(input())
while t:
    t-=1
    f=[[0]*2 for _ in range(N)]
    n=int(input())
    a=[0]+[int(x) for x in input().split()]
    f[1][0]=0 
    f[1][1]=a[1]
    for i in range(2,n+1):
        f[i][0]=max(f[i-1][1],f[i-1][0])
        f[i][1]=max(f[i-1][0], f[i-2][1])+a[i]

    print(max(f[n][0], f[n][1]))
```

#### 买卖股票 IV

- 题目大意：给定一个长度为n的数组，表示一个给定股票在n天内的价格。设计一个算法，计算可以获取的最大利润，最多可以完成**k笔交易**。每次买入卖出合为一笔交易，且不能同时参与多笔交易。


- 实现思路：

1. 使用动态规划解决该问题，定义一个三维数组f，其中f[i][j][0]表示第i天，已完成j笔交易，并且手上没有股票时的最大利润；f[i][j][1]表示第i天，已完成j笔交易，并且手上有股票时的最大利润。
2. 初始化数组f，将所有位置的利润初始化为负无穷-INF，表示不可达。
3. 遍历股票价格数组，使用动态规划递推出f[i][j][0]和f[i][j][1]，即在第i天已完成j笔交易时的最大利润。
4. 最终，取f[n][k][0]中的最大值作为最终结果，表示第n天已完成k笔交易，并且手上没有股票时的最大利润。

代码实现已给出，其中使用了两重循环来填充动态规划数组f，时间复杂度为O(nk)，因为n的最大值为10^5，k的最大值为100，所以算法的时间复杂度为可接受的范围内。

这个算法的核心思想是利用动态规划逐步计算出在每一天已完成一定数量交易时的最大利润，然后根据问题要求取得最优解。

```python
K = 110
INF = 0x3f3f3f3f
f=[[[-INF]*2 for _ in range(K)] for _ in range(2)]

n,k = map(int ,input().split())
w=[0]+[int(x) for x in input().split()]

f[1][0][0]=0
t=0

for i in range(1,n+1):
    for j in range(k+1):
        f[t][j][0]=max(f[t^1][j][0], f[t^1][j][1]+w[i])
        f[t][j][1]=max(f[t^1][j][1], f[t^1][j-1][0]-w[i])
        t^=1

res =max(f[t^1][i][0] for i in range(k+1))
print(res)
```

#### 股票交易V

- 题目大意：给定一个整数数组prices，其中第i天的股票价格为`prices[i]`。设计一个算法计算出在满足约束条件下可以获取的最大利润。约束条件为：卖出股票后，你无法在第二天买入股票（即冷冻期为1天），且不能同时参与多笔交易（必须在再次购买前出售掉之前的股票）。


- 实现思路：

1. 使用动态规划解决该问题，定义一个二维数组f，其中f[i][j]表示第i天的交易状态为j时的最大利润，j有三种状态：0表示持有股票，1表示不持有股票且处于冷冻期，2表示不持有股票且不处于冷冻期。
2. 初始化数组f，将所有位置的利润初始化为负无穷-INF，表示不可达。并且将`f[0][2]`初始化为`0`，表示第`0`天结束时，不持有股票且不处于冷冻期时的最大利润为0。
3. 遍历股票价格数组，使用动态规划递推出`f[i][j]`，即在第i天的交易状态为j时的最大利润。
4. 最终，取`f[n][1]`和`f[n][2]`中的最大值作为最终结果，表示最后一天结束时的最大利润。

代码实现已给出，其中使用了一重循环来填充动态规划数组f，时间复杂度为$O(n)$，因为n的最大值为`5000`，所以算法的时间复杂度为可接受的范围内。

这个算法的核心思想是利用动态规划逐步计算出在每一天不同交易状态下的最大利润，然后根据问题要求取得最优解。

```py
N = int(1e5+10)
INF = 0x3f3f3f3f
f=[[-INF]*3 for _ in range(N)]

n=int(input())
w=[0]+[int(x) for x in input().split()]
f[0][2]=0

for i in range(1,n+1):
    f[i][0]=max(f[i-1][0], f[i-1][2]-w[i])
    f[i][1]=f[i-1][0]+w[i]
    f[i][2]=max(f[i-1][1], f[i-1][2])

print(max(f[n][1], f[n][2]))
```

### [P1220 关路灯](https://www.luogu.com.cn/problem/P1220)

- 题意：这个问题是关于优化老张关灯顺序以最小化总功耗的。老张每次在天亮时首先关掉自己所处位置的路灯，然后可以向左或向右关灯。为了最小化总功耗，需要找到一个最优的关灯顺序。


- 实现思路：
  1. 首先，我们可以使用动态规划来解决这个问题。我们定义一个三维数组f，其中f[i][j][k]表示从第i盏灯到第j盏灯，老张最后一个关灯位置在第k盏灯时的最小功耗。
  1. 然后，我们初始化数组f，将所有位置的功耗都初始化为无穷大INF，表示不可达。
  1. 对于每一对路灯i和j，我们通过动态规划递推出f[i][j][0]和f[i][j][1]，分别表示老张从左边或右边走到位置i到j的最小功耗。
  1. 最后，我们只需比较`f[1][n][0]`和`f[1][n][1]`，取其中较小的值作为最终答案。


- 使用了三重循环来填充动态规划数组f，时间复杂度为$O(n^3)$，因为n的最大值为50，所以算法的时间复杂度为可接受的范围内。这个算法的核心思想是利用动态规划逐步计算出老张从左或右关灯到达每个位置的最小功耗，然后比较两种情况下的最小功耗，找到最优解。


```py
maxn = int(8e2) + 100
INF = 0x3f3f3f3f
f = [[[INF] * 2 for _ in range(maxn)] for _ in range(maxn)]
a = [0] * (maxn + 1)
w = [0] * (maxn + 1)
sum = [0] * (maxn + 1)

n, r = map(int, input().split())

for i in range(1, n + 1):
    a[i], w[i] = map(int, input().split())
    sum[i] = sum[i - 1] + w[i]

f[r][r][0] = f[r][r][1] = 0

for i in range(2, n + 1):
    for j in range(1, n - i + 2):
        l = j + i - 1
        f[j][l][0] = min(f[j + 1][l][0] + (a[j + 1] - a[j]) * (sum[j] + sum[n] - sum[l]),
                         f[j + 1][l][1] + (a[l] - a[j]) * (sum[j] + sum[n] - sum[l]))
        f[j][l][1] = min(f[j][l - 1][0] + (a[l] - a[j]) * (sum[j - 1] + sum[n] - sum[l - 1]),
                         f[j][l - 1][1] + (a[l] - a[l - 1]) * (sum[j - 1] + sum[n] - sum[l - 1]))

ans = min(f[1][n][0], f[1][n][1])
print(ans)
```

### 状态压缩DP

#### 小国王

```py
N = 13
M = 1<<N 
K = 110
state=[]
h=[[] for _ in range(M)]
cnt = [0]*M
f=[[[0]*M for _ in range(K)] for _ in range(N)]

def check(x):
    global n
    for i in range(n):
        if (x>>i)&1 and (x>>i+1)&1:
            return False
    return True

def count(x):
    global n
    cnt=0
    for i in range(n):
        if (x>>i)&1:
            cnt+=1
    return cnt

n,m = map(int, input().split())

for i in range(1<<n):
    if check(i):
        state.append(i)
        cnt[i]=count(i)

for i in range(len(state)):
    for j in range(len(state)):
        a = state[i]
        b = state[j]
        if (a&b)==0 and check(a|b):
            h[i].append(j)
            
f[0][0][0]=1
for i in range(1,n+2):
    for j in range(m+1):
        for k in range(len(state)):
            for t in h[k]:
                c = cnt[state[k]]
                if j>=c:
                    f[i][j][state[k]]+=f[i-1][j-c][state[t]]
                    
                    
print(f[n+1][m][0])

```

#### 愤怒的小鸟

```py
eps = 1e-6
N = 18
M = 1<<18
INF = 0x3f3f3f3f

def cmp(a,b):
    if abs(a-b)<eps:
        return 0
    if a>b:
        return 1
    return -1

t = int(input())

for _ in range(t):
    f = [INF]*M
    path = [[0]*N for _ in range(N)]
    q = [0]*N
    
    n,m = map(int, input().split())
    
    for i in range(n):
        x,y = map(float, input().split())
        q[i]=(x,y)
        
    for i in range(n):
        path[i][i]=1<<i #关键点：与其他点的抛物线可能都不合法，所以需要独立出一条抛物线
        for j in range(n):
            x1,y1 = q[i]
            x2,y2 = q[j]
            
            if cmp(x1,x2)==0:
                continue
            
            a = (y1/x1-y2/x2)/(x1-x2)
            if cmp(a,0)>=0:
                continue
            b = y1/x1-a*x1
            
            state=0
            for k in range(n):
                x3,y3 = q[k]
                if cmp(a*x3*x3+b*x3, y3) == 0:
                    state+=(1<<k)
            path[i][j]=state
    
    f[0]=0
    for i in range(1<<n):
        for j in range(n):
            if not (i>>j)&1:
                x=j
                break
        for j in range(n):
            f[i | path[x][j]] = min(f[i | path[x][j]], f[i]+1)
            
    print(f[(1<<n)-1])
```

### 集合类状态压缩DP

#### 最短Hamilton距离

```py
N = 22
M = 1<<20
INF = 0x3f3f3f3f
f=[[INF]*N for _ in range(M)]
w=[[]*N for _ in range(N)]

n=int(input())
for i in range(n):
    w[i]=[int(_) for _ in input().split()]
    
f[1][0]=0
for i in range(1<<n):
    for j in range(n):
        if i>>j&1:
            for k in range(n):
                if i>>k&1:
                    f[i][j]=min(f[i][j], f[i-(1<<j)][k]+w[k][j])

print(f[(1<<n)-1][n-1])
```

### 区间DP

#### 石子合并

```py
N = 1010
INF = 0x3f3f3f3f
f=[[INF]*N for _ in range(N)]
s=[0]*N
n = int(input())
a=[0]+[int(_) for _ in input().split()]

for i in range(1,n+1):
    f[i][i]=0
    s[i]=s[i-1]+a[i]
    
for l in range(2,n+1):
    for i in range(1,n-l+2):
        j=i+l-1
        for k in range(i,j):
            f[i][j] = min(f[i][j], f[i][k] + f[k+1][j] + s[j]-s[i-1])
            
print(f[1][n])
```

#### 环形石子合并

```py
N = 410
w=[0]*N
s=[0]*N
INF = 0x3f3f3f3f
f=[[-INF]*N for _ in range(N)]
g=[[INF]*N for _ in range(N)]
n=int(input())

a= [0]+[int(_) for _ in input().split()]

for i in range(1,n+1):
    w[i] = w[i+n] = a[i]
    
for i in range(1,n*2+1):
    s[i]=s[i-1]+w[i]
    

for l in range(1,n+1):
    for i in range(1,n*2-l+2):
        j=i+l-1
        if l==1:
            f[i][j]=g[i][j]=0
        for k in range(i,j):
            f[i][j] = max(f[i][j], f[i][k]+f[k+1][j]+s[j]-s[i-1])
            g[i][j] = min(g[i][j], g[i][k]+g[k+1][j]+s[j]-s[i-1])
            
            
minv, maxv = INF, -INF

for i in range(1,n+1):
    minv = min(minv, g[i][i+n-1])
    maxv = max(maxv, f[i][i+n-1])
    
print(minv, maxv, sep='\n')
```

#### 能量项链

```py
N = 210
w=[0]*N
f=[[0]*N for _ in range(N)]

n = int(input())
a=[0]+[int(_) for _ in input().split()]

for i in range(1,n+1):
    w[i]=w[i+n]=a[i]


for l in range(3,n+2):
    for i in range(1,n*2-l+2):
        j=i+l-1
        for k in range(i+1,j):
            f[i][j] = max(f[i][j], f[i][k]+f[k][j]+w[i]*w[k]*w[j])
            
maxv = -1
for i in range(1,n+1):
    maxv = max(maxv, f[i][i+n])
    
print(maxv)
```

#### 凸多边形的划分

```py
N = 55
INF = 1e30
f=[[INF]*N for _ in range(N)]

n = int(input())
w=[0]+[int(_) for _ in input().split()]

for i in range(1,n+1):
    f[i][i+1]=0

for l in range(3,n+1):
    for i in range(1, n-l+2):
        j=i+l-1
        for k in range(i+1,j):
            f[i][j] = min(f[i][j], f[i][k]+f[k][j]+w[i]*w[k]*w[j])

print(f[1][n])
```

#### 加分二叉树

```py
N = 50

def dfs(l, r):
    if l>r:
        return
    k=root[l][r]
    print(k,end=' ')
    dfs(l,k-1)
    dfs(k+1,r)
    

n = int(input())
w = [0]+[int(_) for _ in input().split()]
f=[[0]*N for _ in range(N)]
root=[[0]*N for _ in range(N)]

for l in range(1,n+1):
    for i in range(1,n-l+2):
        j=i+l-1
        for k in range(i,j+1):
            left = 1 if k==i else f[i][k-1]
            right = 1 if k==j else f[k+1][j]
            s = left*right+w[k]
            if i==j:
                s=w[k]
            if f[i][j]<s:
                f[i][j]=s
                root[i][j]=k
    
print(f[1][n])
dfs(1,n)
```

### 树形DP

#### 树的最长路径

注意注意再注意，while循环链不符合要求是需要 i=ne[i]

```py
import sys
sys.setrecursionlimit(int(1e5+10))
N = 10010*2
e = [0]*N
ne = [0]*N
w = [0]*N
h = [-1]*N 
idx = 1
ans = -1

def add(a, b, c):
    global idx
    e[idx]=b
    w[idx]=c
    ne[idx]=h[a]
    h[a]=idx
    idx+=1

def dfs(root, father):
    global ans
    dis, d1, d2 = 0,0,0
    i=h[root]
    while i!=-1:
        j = e[i]
        if j==father:
            i=ne[i] # 重点
            continue
        d = dfs(j, root)+w[i]
        dis = max(dis, d)
        if d>d1:
            d2=d1
            d1=d
        else:
            d2 = max(d2, d)
        i=ne[i]
    ans = max(ans, d1+d2)
    return dis
    
n = int(input())

for i in range(n-1):
    a,b,c = map(int, input().split())
    add(a,b,c)
    add(b,a,c)
    
dfs(1,-1)
print(ans)
```

#### 树的中心

- 题目大意：给定一棵包含 n 个节点的树，每条边都有一个权值。要求在树中找到一个点，使得该点到树中其他节点的最远距离最近。


- 实现思路：

1. 构建无向图的邻接表，其中每个节点对应一个链表，存储其相邻的节点以及对应的边权值。
2. 使用 DFS 遍历树，分别计算每个节点到其他节点的最长距离 d1 和次长距离 d2，并记录其相邻节点 s1。
3. 再次使用 DFS 遍历树，计算每个节点到其相邻节点的最长距离 up。
4. 最后，遍历所有节点，求出所有节点中最大的 max(up[i], d1[i])，即为所求的最近距离。

```py
N = 10010*2
e = [0]*N
ne=[0]*N
h=[-1]*N
w=[0]*N
d1=[0]*N
d2=[0]*N
s1=[0]*N
up=[0]*N
idx = 1

def add(a,b,c):
    global idx
    e[idx]=b
    w[idx]=c
    ne[idx]=h[a]
    h[a]=idx
    idx+=1
    
def dfs1(u, father):
    i=h[u]
    while i!=-1:
        j=e[i]
        if j==father:
            i=ne[i]
            continue
        dfs1(j,u)
        dis = d1[j]+w[i]
        if dis>d1[u]:
            d2[u]=d1[u]
            d1[u], s1[u]=dis, j
        elif dis>d2[u]:
            d2[u]=dis
        i=ne[i]
    
def dfs2(u, father):
    i=h[u]
    while i!=-1:
        j=e[i]
        if j==father:
            i=ne[i]
            continue
        if s1[u]==j:
            up[j]=max(up[u], d2[u])+w[i]
        else:
            up[j]=max(up[u], d1[u])+w[i]
        i=ne[i]
        dfs2(j, u)
        
n = int(input())

for i in range(n-1):
    a,b,c = map(int ,input().split())
    add(a,b,c)
    add(b,a,c)
    
dfs1(1,-1)
dfs2(1,-1)
ans = 1e10
for i in range(1,n+1):
    ans = min(ans, max(up[i], d1[i]))
print(ans)
```

#### 数字转换

- 题目大意：给定一个正整数 n，如果一个数 x 的约数之和 y（不包括 x 本身）比它本身小，那么 x 可以变为 y，y 也可以变为 x。限定所有数字变换在不超过 n 的正整数范围内进行，求不断进行数字变换且不出现重复数字的最多变换步数。


- 实现思路：

1. 首先，对于每个数 i，计算其所有约数之和，并存储在数组 s 中。
2. 然后，遍历所有的数 i，如果约数之和 s[i] 小于 i，则将其作为一个节点添加到有向图中，表示可以从 s[i] 变换到 i。
3. 构建有向图后，使用深度优先搜索（DFS）算法遍历图中的每个节点，并求出每个节点的最长路径长度。
4. 在DFS过程中，使用变量 ans 记录图中最长路径的长度，即最多变换步数。
5. 最后，输出 ans 即可。

```py
N = int(5e4+10)
s=[0]*N
st=[False]*N
e=[0]*N
ne=[0]*N
h=[-1]*N
idx=1
ans = -1

def add(a,b):
    global idx
    e[idx]=b
    ne[idx]=h[a]
    h[a]=idx
    idx+=1

def dfs(u):
    global ans
    d1,d2=0,0
    i=h[u]
    while i!=-1:
        j=e[i]
        dis = dfs(j)+1
        if dis>d1:
            d2,d1=d1,dis
        elif dis>d2:
            d2=dis
        i=ne[i]
    ans = max(ans, d1+d2)
    return d1
    
n = int(input())

for i in range(1,n+1):
    for j in range(2,n+1):
        if i>n//j:
            break
        s[i*j]+=i
    
for i in range(2,n+1):
    if s[i]<i:
        add(s[i], i)
        st[i]=True
    
# for i in range(1,n+1):
#     if not st[i]:
#         dfs(i)
    
dfs(1)
print(ans)
```

#### 没有上司的舞会

- 题目大意：Ural大学有N名职员，编号为1∼N。他们的关系像一棵以校长为根的树，父节点就是子节点的直接上司。每个职员有一个快乐指数，用整数Hi表示。现在要召开一场周年庆宴会，但没有职员愿意和直接上司一起参会。在满足这个条件的前提下，主办方希望邀请一部分职员参会，使得所有参会职员的快乐指数总和最大，求这个最大值。


- 实现思路：

1. 首先，建立一棵以校长为根的树，使用一个二维数组v来存储树的结构关系，其中v[i]表示第i个职员的直接下属。
2. 使用一个二维数组f来记录每个职员参加和不参加宴会时的最大快乐指数，其中f[i][0]表示第i个职员不参加宴会时的最大快乐指数，f[i][1]表示第i个职员参加宴会时的最大快乐指数。
3. 使用深度优先搜索（DFS）来遍历树的每个节点，计算每个节点不参加和参加宴会时的最大快乐指数。
4. 在DFS过程中，对于每个节点i，首先更新该节点参加宴会时的最大快乐指数f[i][1]为当前节点的快乐指数h[i]，然后遍历当前节点的直接下属，更新当前节点参加和不参加宴会时的最大快乐指数。
5. 最终，取根节点的参加和不参加宴会时的最大快乐指数的较大值作为最终结果。

代码实现已给出，其中使用了深度优先搜索来遍历树的每个节点，时间复杂度为O(N)，因为N的最大值为6000，所以算法的时间复杂度为可接受的范围内。

这个算法的核心思想是利用深度优先搜索逐步计算出每个节点参加和不参加宴会时的最大快乐指数，然后根据问题要求找到最大的快乐指数。

```py
import sys
sys.setrecursionlimit(int(1e4))

N = int(7e3)

v=[[] for _ in range(N)]
f=[[0]*2 for _ in range(N)]
st=[False]*N
h=[0]*N
root=0

def dfs(u):
    f[u][1]+=h[u]
    for i in v[u]:
        dfs(i)
        f[u][1]+=f[i][0]
        f[u][0]+=max(f[i][0], f[i][1])        

n = int(input())

for i in range(1,n+1):
    h[i]=int(input())

for i in range(n-1):
    l,k = map(int, input().split())
    v[k].append(l)
    st[l]=True
    
for i in range(1,n+1):
    if not st[i]:
        root=i

dfs(root)
        
print(max(f[root][0], f[root][1]))
```

### 单调队列优化的DP

#### 最大子序和

- 题目大意：给定一个长度为n的整数序列，从中找出一段长度不超过m的连续子序列，使得子序列中所有数的和最大。要求输出该序列的最大子序和。


- 实现思路：

1. 首先，将输入的序列进行预处理，计算出前缀和数组s，其中s[i]表示序列的前i个数的和。
2. 使用单调队列来解决该问题。单调队列维护的是前缀和数组s中的下标，队列中的元素按照前缀和的值单调递增。队列中的下标表示的是以该下标结尾的连续子序列的最大和。
3. 遍历序列，对于每个位置i，更新单调队列中的元素，使得队列中的下标符合要求，即满足长度不超过m的连续子序列的条件。
4. 在遍历的过程中，不断更新记录最大子序和的变量ans，最终得到的ans即为所求结果。

代码实现已给出，其中使用了单调队列来维护满足条件的下标，时间复杂度为O(n)，因为n的最大值为300000，所以算法的时间复杂度为可接受的范围内。

这个算法的核心思想是利用单调队列维护以每个位置结尾的满足条件的子序列的最大和，然后根据问题要求找到最大子序和。

```py
N = 300010
q, s = [0]*N, [0]*N
ans = -2**33
hh, tt = 0, 0


n,m = map(int, input().split())

s = [0]+list(map(int, input().split()))
for i in range(1,n+1):
    s[i]+=s[i-1]

for i in range(1,n+1):
    if q[hh]<i-m:
        hh+=1    
    ans = max(ans, s[i]-s[q[hh]])
    while hh<=tt and s[q[tt]]>=s[i]:
        tt-=1
    tt+=1
    q[tt]=i


print(ans)
```



### 数位DP

1081

```py
from typing import List
N = 35
def init() -> List[List[int]]:
    f = [[0] * N for _ in range(N)]
    for i in range(N):
        for j in range(i + 1):
            if j == 0:
                f[i][j] = 1
            else:
                f[i][j] = f[i - 1][j] + f[i - 1][j - 1]
    return f

def dp(n: int, k: int, b: int, f: List[List[int]]) -> int:
    if n == 0:
        return 0
    nums = []
    while n:
        nums.append(n % b)
        n //= b
    res = 0
    last = 0
    for i in range(len(nums) - 1, -1, -1):
        x = nums[i]
        if x > 0:
            res += f[i][k - last]
            if x > 1:
                if k - last - 1 >= 0:
                    res += f[i][k - last - 1]
                break
            else:
                last += 1
                if last > k:
                    break

        if i == 0 and last == k:
            res += 1

    return res

def solve(l: int, r: int, k: int, b: int) -> int:
    f = init()
    return dp(r, k, b, f) - dp(l - 1, k, b, f)

if __name__ == "__main__":
    l, r, k, b = map(int, input().split())
    print(solve(l, r, k, b))
```

### 记忆化

#### 滑雪

- 题目大意：Michael喜欢滑雪，并希望知道在一个区域中最长的滑坡。给定一个二维数组表示区域，数组中的每个数字代表点的高度。一个人可以从某个点滑向上下左右相邻四个点之一，当且仅当高度会减小。要求找到最长的滑坡，即从一个高点开始，一直滑到一个低点。


- 实现思路：

1. 使用优先队列（堆）来实现广度优先搜索（BFS）。
2. 首先，初始化一个二维数组f用于存储从每个点出发的最长滑坡距离，初始化优先队列pq用于存储待搜索的点。
3. 将所有点按照高度从小到大加入优先队列pq。
4. 对于优先队列中的每个点，依次向上下左右四个方向搜索，如果下一个点的高度小于当前点，则更新下一个点的最长滑坡距离为当前点的最长滑坡距离加1。
5. 在搜索过程中，不断更新记录最长滑坡距离的变量ma，最终得到的ma即为最长滑坡的长度。

代码实现已给出，其中使用了优先队列pq来进行广度优先搜索，时间复杂度为O(nmlog(nm))，因为n和m的最大值为100，所以算法的时间复杂度为可接受的范围内。

这个算法的核心思想是利用广度优先搜索逐步计算出从每个点出发的最长滑坡距离，然后根据问题要求找到最长的滑坡。

```py
from heapq import *

n, m = map(int, input().split())
f = [[0] * (m + 5) for _ in range(n + 5)]  # distance
g = [[0] * (m + 5) for _ in range(n + 5)]  # store heights

pq = []
for i in range(1, n + 1):
    nums = [0] + list(map(int, input().split()))
    for j, num in enumerate(nums[1:]):
        f[i][j] = 1
        g[i][j] = num
        heappush(pq, (num, i, j))  # Push (num, i, j) into the priority queue

ma = -1
while pq:
    num, i, j = heappop(pq)
    if g[i - 1][j] < num:
        f[i][j] = max(f[i][j], f[i - 1][j] + 1)
    if g[i + 1][j] < num:
        f[i][j] = max(f[i][j], f[i + 1][j] + 1)
    if g[i][j - 1] < num:
        f[i][j] = max(f[i][j], f[i][j - 1] + 1)
    if g[i][j + 1] < num:
        f[i][j] = max(f[i][j], f[i][j + 1] + 1)
    ma = max(ma, f[i][j])

print(ma)

```

```py
from functools import lru_cache
from sys import stdin
input = stdin.readline

def solve(matrix):
    if not matrix:
        return 0

    dirs = [(-1, 0), (0, 1), (1, 0), (0, -1)]
    @lru_cache(None)
    def dfs(r, c):
        res = 1
        for dx, dy in dirs:
            a, b = r + dx, c + dy
            if 0<= a <m and 0<= b <n and matrix[a][b] > matrix[r][c]:
                res = max(res, dfs(a, b) + 1)
        
        return res

    m, n = len(matrix), len(matrix[0])
    res = 0
    for i in range(m):
        for j in range(n):
            res = max(res, dfs(i, j))

    return res

r, c = map(int, input().split())
matrix = [[]for _ in range(r)]
for i in range(r):
    matrix[i] = list(map(int, input().split()))

print(solve(matrix))
```



