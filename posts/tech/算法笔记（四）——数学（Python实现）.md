---
title : '算法笔记（四）——数学（Python实现）'
date : 2024-04-09T15:37:01+08:00
lastmod: 2024-04-09T15:37:01+08:00
description : "算法笔记（四）——数学（Python实现）" 
image : img/cat.jpg
draft : false    
categories : ["算法笔记"]
tags : ["算法笔记","Python"]
# password : leetcode
---

#  算法笔记（四）——数学（Python实现）

## 数学

### 试除法判定质数

```py
def check(x): # 判定
    if x<2:
        return False
    for i in range(2, int(x**0.5)+1):
        if x%i==0:
            return False
    return True
    
n = int(input())
for i in range(n):
    x = int(input())
    if check(x):
        print("Yes")
    else:
        print("No")
```

### 试除法分解质因数

```py
def get(x): #分解
    for i in range(2, int(x**0.5)+1):
        if x%i==0:
            cnt=0
            while x%i==0:
                x//=i
                cnt+=1
            print(i, cnt, sep=' ')
        
    if x>1:
        print(x, 1, sep=' ')

n = int(input())
for i in range(n):
    x = int(input())
    get(x)
    print()
```

### 线性筛法求素数

```python
N = int(1e6+10)
primes = []
st = [False]*N

def get(n):
    for i in range(2, n+1):
        if not st[i]:
            primes.append(i)
        for j in range(len(primes)):
            if i*primes[j]>n:
                break
            st[i*primes[j]]=True
            if i%primes[j]==0:
                break

n = int(input())
get(n)
print(len(primes))
```

##### 朴素筛法求素数

```py
N = 1000005  # 根据需要修改
primes = []  # 存储所有素数
st = [False] * N  # st[x]存储x是否被筛掉

# 筛素数函数
def get_primes(n):
    global primes
    global st
    for i in range(2, n + 1):
        if not st[i]:
            primes.append(i)
            for j in range(i, n + 1, i):
                st[j] = True

# Example usage:
# get_primes(100)
# print(primes)
```

### 试除法求所有约数 

```py
def get(x):
    res = [1]
    for i in range(2, int(x**0.5)+1):
        if x%i==0:
            res.append(i)
            if i!=x//i:
                res.append(x//i)
    if x>1:
        res.append(x)
    return sorted(res)


n = int(input())
for i in range(n):
    x = int(input())
    res = get(x)
    print(*res)
```

### 约数个数和约数之和 

```
如果 N = p1^c1 * p2^c2 * ... *pk^ck
约数个数： (c1 + 1) * (c2 + 1) * ... * (ck + 1)
约数之和： (p1^0 + p1^1 + ... + p1^c1) * ... * (pk^0 + pk^1 + ... + pk^ck)
```

#### [约数之和](https://www.acwing.com/problem/content/description/99/)

**题目大意**：给定两个自然数 A 和 B，定义 S 为 A*B 的所有约数之和。求 S mod 9901 的值。

**实现思路**：首先，我们需要编写一个快速幂函数 qpow(a, k) 来计算幂运算的结果。然后，定义一个函数 calc(p, k) 来计算 p^k 的结果并对 mod 取余。在主程序中，我们对 A 进行质因数分解，并计算每个质因数的幂次方与 B 的乘积，并累加到最终结果 res 中。最后，输出 res 的值对 mod 9901 取余的结果。

```py
import sys
input = lambda:sys.stdin.readline().strip()

mod = 9901

def qpow(a, k):
    res = 1
    while k:
        if k&1:
            res = res*a%mod
        a = a*a%mod
        k>>=1
    return res

def calc(p, k):
    if k==0: return 1
    if k%2==1:
        return (1+qpow(p, k//2+1)) * calc(p, k//2) % mod
    return (qpow(p, k)%mod + (1 + qpow(p, (k-1)//2+1)) * calc(p, (k-1)//2)) % mod

a, b = map(int, input().split())
res = 1
for i in range(2, a+1):
    if a<i:
        break
    s = 0
    while a%i==0:
        s+=1
        a//=i
    if s:
        res = res * calc(i, s*b) % mod

print(res if a else 0)
```

### gcd

```py
def gcd(a, b):
    return gcd(b, a % b) if b else a
```

### 求欧拉函数

```py
def get(x):
    res = x
    for i in range(2, int(x**0.5)+1):
        if x%i==0:
            res = res//i*(i-1)
            while x%i==0:
                x//=i
    if x>1:
        res = res//x*(x-1)
    return res


n = int(input())
for i in range(n):
    x = int(input())
    print(get(x))
```

### 筛法求欧拉函数

```py
N = int(1e6+10)
primes = []
euler = [0]*N
st = [False]*N
cnt = 0

def get(n):
    euler[1] = 1
    for i in range(2, n+1):
        if not st[i]:
            primes.append(i)
            euler[i] = i-1
        for j in range(len(primes)):
            t = primes[j]*i
            if t>n:
                break
            st[t] = True
            if i%primes[j]==0:
                euler[t] = euler[i]*primes[j]
                break
            euler[t] = euler[i]*(primes[j]-1)



n = int(input())
get(n)
for i in range(1,n+1):
    cnt+=euler[i]
print(cnt)
```

### 快速幂

```py
# 快速幂函数
def qmi(m, k, p):
    res, t = 1 % p, m
    while k:
        if k & 1:
            res = res * t % p
        t = t * t % p
        k >>= 1
    return res

# Example usage:
# result = qmi(2, 10, 1000000007)
# print(result)
```

### 拓展欧几里得

```py
# 求 x, y，使得 ax + by = gcd(a, b)
def exgcd(a, b, x, y):
    if b == 0:
        x[0], y[0] = 1, 0
        return a
    d = exgcd(b, a % b, y, x)
    y[0] -= (a // b) * x[0]
    return d

# Example usage:
# x = [0]
# y = [0]
# gcd = exgcd(30, 20, x, y)
# print("x:", x[0], "y:", y[0], "gcd:", gcd)
```

#### [P1082 [NOIP2012 提高组] 同余方程](https://www.luogu.com.cn/problem/P1082)

- 题目描述：求解关于x的同余方程$ax ≡ 1 (mod b)$的最小正整数解。


- 实现思路：这个问题可以转化为求解$ax + by = gcd(a, b)$的解，其中x就是我们需要的结果。可以利用扩展欧几里得算法来求解这个方程。具体地，通过递归的方式求解出最大公约数，并且求解出使得$ax + by = gcd(a, b)$成立的整数x和y。然后通过对x取模b的操作得到最小正整数解x。最后输出x即可。


```py
# 求 x, y，使得 ax + by = gcd(a, b)
def exgcd(a, b, x, y):
    if b == 0:
        x[0], y[0] = 1, 0
        return a
    d = exgcd(b, a % b, y, x)
    y[0] -= (a // b) * x[0]
    return d

a, b = map(int, input().split())

x, y = [0], [0]
exgcd(a, b, x, y)
x = x[0]
x = (x%b+b)%b

print(x)
```

### 递推法求组合数

```py
N = int(2e3)+10
mod = int(1e9)+7
c = [[0]*N for _ in range(N)]

def get(n):
    for i in range(n+1):
        for j in range(i+1):
            if j==0:
                c[i][j] = 1
            else:
                c[i][j] = (c[i-1][j]+c[i-1][j-1])%mod



MN = int(2e3)
get(MN)
n = int(input())
for i in range(n):
    a, b = map(int, input().split())
    print(c[a][b])
```

### 通过预处理逆元的方式求组合数

```py
mod = int(1e9)+7
N = int(2e5)+10
fact, infact = [0]*N, [0]*N

def qpow(a, k, p):
    res = 1
    while k:
        if k&1:
            res = (res*a)%p
        a = (a*a)%p
        k>>=1
    return res
    
def init():
    fact[0] = infact[0] = 1
    for i in range(1, N):
        fact[i] = (fact[i-1]*i)%mod
        infact[i] = (infact[i-1]*qpow(i, mod-2, mod))%mod
        
def C(n, m):
    if n<m:
        return 0
    return (fact[n]*infact[m]%mod*infact[n-m]%mod)
    
init()
n = int(input())
for i in range(n):
    a, b = map(int, input().split())
    print(C(a, b))
```

### Lucas定理

```py
p = 1000000007  # 根据需要调整

# 快速幂模板
def qmi(a, k):
    res = 1
    while k:
        if k & 1:
            res = (res * a) % p
        a = (a * a) % p
        k >>= 1
    return res

# 通过定理求组合数C(a, b)
def C(a, b):
    res = 1
    for i in range(1, b + 1):
        res = (res * (a - i + 1)) % p
        res = (res * qmi(i, p - 2)) % p
    return res

# Lucas定理计算组合数
def lucas(a, b):
    if a < p and b < p:
        return C(a, b)
    return (C(a % p, b % p) * lucas(a // p, b // p)) % p

# Example usage:
# result = lucas(10, 5)  # 计算组合数 C(10, 5)
# print(result)
```

### 分解质因数法求组合数

分解质因数法计算组合数：

1. 获取小于等于 `a` 的所有质数。
2. 初始化质数的幂次列表 `s`。
3. 对每个质数 `p`，计算在 `a!`，`b!` 和 `(a-b)!` 中 `p` 的幂次，通过 `get(a, p) - get(b, p) - get(a - b, p)` 得到组合数分解中 `p` 的总幂次。
4. 通过快速幂计算每个质数 `p` 的幂次乘积，最终得到组合数。

```py
def get_primes(n):
    primes = []
    st = [False]*(n+1)
    for i in range(2, n+1):
        if not st[i]:
            primes.append(i)
        for p in primes:
            if i*p>n:
                break
            st[i*p] = True
            if i%p==0:
                break
    return primes

def get(n, p):
    cnt = 0
    while n:
        cnt += n//p
        n//=p
    return cnt

def qpow(a, k):
    res = 1
    while k:
        if k&1:
            res = res * a
        a = a*a
        k>>=1
    return res
    
    
def calc(a, b):
    primes = get_primes(a)
    n = len(primes)
    res = 1
    for i, p in enumerate(primes):
        s = get(a, p) - get(b, p) - get(a-b, p)
        res *= qpow(p, s)
    return res
        
a, b = map(int, input().split())
print(calc(a, b))
```



### 高斯消元

```py
eps = 1e-8  # 根据需要调整

# a 是增广矩阵，n 是矩阵维度
def gauss(a, n):
    c, r = 0, 0
    for c in range(n):
        t = r
        for i in range(r, n):
            if abs(a[i][c]) > abs(a[t][c]):
                t = i
        if abs(a[t][c]) < eps:
            continue
        for i in range(c, n + 1):
            a[r][i], a[t][i] = a[t][i], a[r][i]
        for i in range(n, c - 1, -1):
            a[r][i] //= a[r][c]
        for i in range(r + 1, n):
            if abs(a[i][c]) > eps:
                for j in range(n, c - 1, -1):
                    a[i][j] -= a[r][j] * a[i][c]
        r += 1

    if r < n:
        for i in range(r, n):
            if abs(a[i][n]) > eps:
                return 2  # 无解
        return 1  # 有无穷多组解

    for i in range(n - 1, -1, -1):
        for j in range(i + 1, n):
            a[i][n] -= a[i][j] * a[j][n]
    return 0  # 有唯一解

# Example usage:
# a = [[2, 1, -1, 8], [-3, -1, 2, -11], [-2, 1, 2, -3]]
# n = 3
# result = gauss(a, n)
# print(result)
```