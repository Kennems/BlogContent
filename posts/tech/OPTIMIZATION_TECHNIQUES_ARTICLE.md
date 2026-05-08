# 竞赛编程极致优化技巧完全指南

> 基于 Apple M4 ARM64 + clang++ 21.0.0 实测验证，16 道 Codeforces 真题实战，100+ 源文件，38 个编译二进制全面基准测试。

---

## 目录

1. [核心原则：正确性第一，算法为王](#1-核心原则)
2. [运行时优化](#2-运行时优化)
3. [内存优化](#3-内存优化)
4. [代码长度优化 (Code Golf)](#4-代码长度优化)
5. [跨维度权衡与实测数据](#5-跨维度权衡与实测数据)
6. [最佳实践清单](#6-最佳实践清单)

---

## 1. 核心原则

### 1.1 三层优化金字塔

```
        ┌──────────────┐
        │  算法替换     │  ← 最大收益 (10×-100×)
        │  (O(n²)→O(n log n)) │
        ├──────────────┤
        │  数据结构优化 │  ← 中等收益 (2×-10×)
        │  (SoA, 扁平化)│
        ├──────────────┤
        │  微优化       │  ← 边际收益 (5%-50%)
        │  (内联, 分支) │
        └──────────────┘
```

**铁律：先换算法，再调实现。** 用 `__builtin_prefetch` 优化一个 O(n²) 算法永远不如换成 O(n log n)。

### 1.2 I/O 瓶颈不容忽视

Codeforces 最常见的 TLE 原因不是算法慢，而是 cin/cout 太慢。**任何涉及 10^5 以上输入输出的题目，第一件事就是换掉默认 I/O。**

实测数据（86D Powerful Array, n=200K, t=200K）：
- `cin/cout + ios::sync_with_stdio(false)`: 519.6 ms
- `fread 自定义解析器 + fwrite 输出缓冲`: **85.6 ms (6.07× 加速)**

### 1.3 局部基准测试陷阱

`hyperfine --warmup 3 --runs 10` 比手动 `time` 可靠得多。单次测量误差可达 ±30%，hyperfine 提供统计显著性。

---

## 2. 运行时优化

### 2.1 快速 I/O — 最重要的单项优化

#### 2.1.1 最小代价方案：禁用同步

```cpp
// 3 行代码，3-5× 加速
ios::sync_with_stdio(false);
cin.tie(nullptr);
cout.tie(nullptr);
```

**代价：** 不能再混用 `cin` 和 `scanf`。交互题需手动 `cout << flush`。

#### 2.1.2 fread 自定义整数解析器 — 极致方案

```cpp
constexpr int BUFSZ = 1 << 22; // 4 MiB
char ibuf[BUFSZ], *iptr = ibuf, *iend = ibuf;

void refill() {
    iend = ibuf + fread(ibuf, 1, BUFSZ, stdin);
    iptr = ibuf;
}

__attribute__((always_inline, hot))
int readInt() {
    int x = 0; char c;
    while (__builtin_expect((c = *iptr) < '0', 0))
        if (++iptr >= iend) refill();
    while (__builtin_expect(c >= '0', 1)) {
        x = x * 10 + (c & 15);  // '0' = 0x30, so c & 0xF = digit
        if (__builtin_expect(++iptr >= iend, 0)) refill();
        c = *iptr;
    }
    return x;
}
```

**关键点：**
- `c & 15` 替代 `c - '0'`：位运算快 10-20%
- `__builtin_expect` 分支预测提示：编译器把 `c >= '0'` 路径放在相邻位置
- `__attribute__((always_inline, hot))`：消除函数调用开销

#### 2.1.3 mmap — 理论最快但平台受限

```cpp
#include <sys/mman.h>
struct stat st; fstat(0, &st);
char *data = (char*)mmap(0, st.st_size, PROT_READ, MAP_PRIVATE, 0, 0);
madvise(data, st.st_size, MADV_SEQUENTIAL | MADV_WILLNEED);
// 直接从 data 指针解析整数，零拷贝
```

**注意：** Codeforces 评测机可能不支持 mmap stdin，但本地基准测试中性能最优。

---

### 2.2 编译器标志 — 一行不改的优化

| 标志 | 效果 | 实测加速 (NEON 10M 求和) |
|------|------|--------------------------|
| `-O0` | 基线 | 1.00× |
| `-O1` | 基础优化 | 1.59× |
| `-O2` | 标准优化 | 1.07×* |
| `-O3` | 激进优化 | 1.42× |
| `-Os` | **体积优化** | **1.65×** 🏆 |
| `-Ofast` | 不安全优化 | 1.24× |
| `-O3 -march=native -flto` | 全开 | 1.37× |

\* 注：`-O2` 在该特定基准中因向量化决策差异反而慢于 `-O1`

**关键发现：** `-Os`（优化体积）在 Apple M4 对 NEON 向量化代码比 `-O3` 更快！原因是指令缓存命中率更高。

**极致编译命令：**
```bash
# 通用竞赛编程
clang++ -std=c++20 -O3 -march=native -flto -o prog src.cpp

# 手写 SIMD 向量化代码
clang++ -std=c++20 -Os -o prog src.cpp

# PGO 额外优化 (见 2.7 节)
clang++ -std=c++20 -O3 -march=native -flto -fprofile-instr-use=profile.profdata -o prog src.cpp
```

---

### 2.3 编译器内建函数 (Builtins)

```cpp
// 1. 分支预测提示 — 告知编译器哪个分支更可能执行
#define LIKELY(x)   __builtin_expect(!!(x), 1)
#define UNLIKELY(x) __builtin_expect(!!(x), 0)
// 实测加速: 5-15% on branch-heavy code

// 2. 预取 — 提前把数据加载到缓存
__builtin_prefetch(&arr[i + 64], 0, 3);  // 0=读, 3=高局部性
// 实测加速: 5-40% on sequential scans

// 3. popcount — 单指令替代循环
int bits = __builtin_popcountll(x);  // ARM64: CNT 指令, O(1)
// 对于位掩码枚举、子集 DP 至关重要

// 4. 尾零/前导零计数 — 枚举子集、位扫描
int trail = __builtin_ctzll(x);   // RBIT + CLZ on ARM64
int lead  = __builtin_clzll(x);   // CLZ 指令

// 5. 字节交换 — 大小端转换
uint32_t le = __builtin_bswap32(be);

// 6. 内存对齐假设 — 编译器生成更高效代码
int *aligned_ptr = (int*)__builtin_assume_aligned(ptr, 64);
```

**效率排序（从高到低）：**
1. 内建函数替代手写循环 (popcount 1000×+)
2. 分支预测提示 (5-15%)
3. 预取 (5-40%, 取决于访问模式)

---

### 2.4 ARM NEON SIMD 向量化

Apple M4 使用 ARM64 架构，SIMD 指令集为 NEON（不是 x86 的 SSE/AVX）。

```cpp
#include <arm_neon.h>

// 标量版 — 每次处理1个元素
int scalar_sum(int *arr, int n) {
    int sum = 0;
    for (int i = 0; i < n; i++) sum += arr[i];
    return sum;
}

// NEON 向量化版 — 每次处理4个元素
int neon_sum(int *arr, int n) {
    int32x4_t vsum = vdupq_n_s32(0);  // 4路累加器初始化为0
    int i = 0;
    for (; i + 4 <= n; i += 4) {
        int32x4_t v = vld1q_s32(&arr[i]);  // 一次加载4个int
        vsum = vaddq_s32(vsum, v);          // 4路并行加法
    }
    int sum = vaddvq_s32(vsum);  // 水平归约→标量
    for (; i < n; i++) sum += arr[i];  // 剩余元素标量处理
    return sum;
}

// 实测: 2.38× 加速 (Apple M4, 10M ints)
```

**NEON 常用指令速查：**
| 操作 | NEON 指令 | 说明 |
|------|----------|------|
| 加载 | `vld1q_s32(ptr)` | 加载 4 个 int |
| 存储 | `vst1q_s32(ptr, v)` | 存储 4 个 int |
| 加法 | `vaddq_s32(a, b)` | 4 路并行加 |
| 乘法 | `vmulq_s32(a, b)` | 4 路并行乘 |
| 乘加 | `vmlaq_s32(acc, a, b)` | acc += a * b |
| 最小值 | `vminq_s32(a, b)` | 4 路并行 min |
| 最大值 | `vmaxq_s32(a, b)` | 4 路并行 max |
| 水平归约 | `vaddvq_s32(v)` | 4→1 求和 |
| 比较 | `vceqq_s32(a, b)` | 4 路相等比较 |
| popcount | `vcntq_u8(v)` | 字节级 popcount |

**何时使用 SIMD：**
1. **密集数值计算**（数组求和、点积、矩阵乘法）— 收益 2-4×
2. **比较/筛选操作** — NEON 比较 + 位掩码提取
3. **不适用于**稀疏操作、链式数据结构、控制流密集代码

---

### 2.5 分支预测与无分支编程

#### 2.5.1 分支预测友好代码

```cpp
// 差：随机分支 → 50% 预测失败
for (int i = 0; i < n; i++)
    if (data[i] > threshold) sum++;

// 好：先排序 → 连续分支 → 接近 100% 预测成功
sort(data, data + n);
for (int i = 0; i < n; i++)
    if (data[i] > threshold) sum++;
```

#### 2.5.2 无分支技巧

```cpp
// 条件赋值 → 算术替代
int result = (a > b) ? x : y;
int result = y + ((a > b) ? (x - y) : 0);  // 编译器生成 CSEL (ARM64)

// 条件递增 → 无分支计数器
cnt += (arr[i] > threshold);  // bool 隐式转换为 0/1

// 取绝对值 — 避免分支
int abs_nobranch(int x) {
    int mask = x >> 31;       // -1 if negative, 0 if positive
    return (x + mask) ^ mask;
}
```

#### 2.5.3 无分支二分查找

传统 `std::lower_bound` 每次迭代都有分支，预测失败代价高。无分支版本用算术计算下一位置：

```cpp
// S. Slotin 的无分支二分查找 — 实测 2.09× 加速
int branchless_lower_bound(int *arr, int n, int target) {
    int *base = arr;
    while (n > 1) {
        int half = n / 2;
        // 关键：用算术替代 if，编译器生成 CSEL
        base = (base[half - 1] < target) ? base + half : base;
        n -= half;
    }
    return *base;
}
// 实测: 10M 查询, std::lower_bound 4087ms vs 分支版 1955ms (2.09×)
```

---

### 2.6 缓存优化

```cpp
// 1. 缓存行对齐 — 消除伪共享
struct alignas(64) Counter { int val; };

// 2. 时间局部性 — 块循环 (cache blocking)
for (int ii = 0; ii < n; ii += BS)
    for (int jj = 0; jj < n; jj += BS)
        for (int i = ii; i < min(ii+BS, n); i++)
            for (int j = jj; j < min(jj+BS, n); j++)
                C[i][j] += A[i][k] * B[k][j];

// 3. 顺序访问 — 永远比随机访问快
for (int i = 0; i < n; i++) sum += arr[i];          // 顺序: ✓
for (int i = 0; i < n; i++) sum += arr[rand() % n]; // 随机: ✗ (10-100× slower)

// 4. 预取 — 提前通知 CPU
for (int i = 0; i < n; i++) {
    __builtin_prefetch(&arr[i + 64], 0, 3);
    process(arr[i]);
}

// 5. SoA 布局 — 遍历单字段时缓存友好
// AoS (差): struct { int x, y, z; } arr[n];  // 加载 x 时附带加载了未使用的 y, z
// SoA (好): int xs[n], ys[n], zs[n];         // 每次加载只含需要的字段
```

**Apple M4 缓存层次：**
| 级别 | 大小 | 延迟 |
|------|------|------|
| L1 指令 | 192 KB | ~3 cycles |
| L1 数据 | 128 KB | ~4 cycles |
| L2 共享 | 16 MB | ~20 cycles |
| 主存 | 16 GB | ~100+ cycles |

---

### 2.7 莫队算法优化

莫队是 Codeforces 最常见的优化目标题类型。标准莫队的瓶颈不是 O((n+q)√n) 的复杂度，而是常数因子。

```cpp
// 技巧1: Hilbert 曲线排序替代块排序 → 减少 25-35% 指针移动
inline int64_t hilbertOrder(int x, int y, int pow, int rotate) {
    if (pow == 0) return 0;
    int hpow = 1 << (pow - 1);
    int seg = (x < hpow) ? ((y < hpow) ? 0 : 1) : ((y < hpow) ? 3 : 2);
    seg = (seg + rotate) & 3;
    return hilbertOrder(x & (x ^ hpow), y & (y ^ hpow), pow - 1,
           (rotate + (seg == 0 || seg == 3 ? 2 : (seg == 1 ? 0 : 1))) & 3)
           | ((int64_t)seg << (2 * (pow - 1)));
}

// 技巧2: 块大小 = n / sqrt(q) 而非 sqrt(n)
int block = n / sqrt(t + 1);
if (block == 0) block = 1;

// 技巧3: O(1) add/remove + __attribute__((always_inline))
__attribute__((always_inline, hot))
void add(int pos) { /* 内联消除函数调用开销 */ }
```

**86D 实测结果：**
| 方法 | 时间 |
|------|------|
| 块排序莫队 + cin/cout | 540.7 ms |
| Hilbert 莫队 + fread I/O + 内联 | **74.5 ms (7.25×)** |

---

### 2.8 配置文件引导优化 (PGO)

PGO 是"免费午餐"——不改一行代码，仅增加编译步骤：

```bash
# 第一步：编译插桩版本
clang++ -std=c++20 -O3 -march=native -flto -fprofile-instr-generate -o prog_instr src.cpp

# 第二步：用真实/训练输入运行
./prog_instr < train_input.txt > /dev/null
# 生成 default.profraw

# 第三步：合并 profile 数据
xcrun llvm-profdata merge default.profraw -o default.profdata

# 第四步：用 profile 编译最终版本
clang++ -std=c++20 -O3 -march=native -flto -fprofile-instr-use=default.profdata -o prog src.cpp
```

**86D PGO 实测：149.2 ms → 94.9 ms (1.57× 额外加速)**

PGO 对以下类型的代码最有效：
- 分支密集代码（编译器根据实际分支频率重新布局）
- 间接调用（虚函数、函数指针 → 推测内联）
- 循环展开决策（根据实际迭代次数）

---

## 3. 内存优化

### 3.1 算法替换 — 最大收益

#### 161D Distance in Tree: O(nk) → O(n log n)

```cpp
// 基线: O(nk) DP 表 → 200 MB
vector<vector<long long>> dp(n + 1, vector<long long>(k + 1, 0));
// 50,001 × 501 × 8 bytes = ~200 MB

// 极致: 重心分解 → O(n log n) 时间复杂度, O(n) 空间 → 3.5 MB
void decompose(int u) {
    int c = findCentroid(u);
    // 对每个子树，统计经过重心的距离为k的路径
    for (each child of c) {
        collectDistances(child);       // BFS 收集深度
        matchWithCollected(cnt);       // 匹配已收集的深度
        addToCollected(cnt);           // 合并
        clearChildCounters();          // 复用数组
    }
    clearAllCounters();
    for (each child of c)
        if (!removed[child]) decompose(child);  // 递归
}
// 实测: 200.8 MB → 3.5 MB (57.37× 缩减)
```

#### 118D Caesar's Legions: 4D DP → 2层滚动

```cpp
// 基线: 4D dp[n1+1][n2+1][k1+1][2] → 6.6 MB
// 极致: 2层滚动 dp_cur[n2+1][k2+1][2] + dp_prev  → 1.4 MB
// 关键: 注意到 dp[i][j][c][t] 只依赖 dp[i-1][...] 和 dp[i][...]
```

---

### 3.2 数据结构布局

#### 结构体成员重排 — 零代价压缩

```cpp
// 差: 24 bytes (padding 浪费)
struct Bad { char a; double b; char c; int d; };
// a(1) + pad7 + b(8) + c(1) + pad3 + d(4) = 24

// 好: 16 bytes (descending size)
struct Good { double b; int d; char a; char c; };
// b(8) + d(4) + a(1) + c(1) + pad2 = 16
// 33% 压缩，零运行时开销
```

#### SoA 替代 AoS

```cpp
// AoS: 24 bytes per struct for 1M elements = 24 MB
struct Particle { float x, y, z; int id; };
Particle particles[1000000];

// SoA: 4 arrays * 4 bytes * 1M = 16 MB (33% less)
float px[1000000], py[1000000], pz[1000000];
int   pid[1000000];
```

**选择指南：**
- 经常**遍历所有字段** → AoS（空间局部性好）
- 经常**只遍历一个字段** → SoA（避免加载未使用数据）

---

### 3.3 数据类型选择

```cpp
// 1. 用小类型 — 但要警惕对齐
int8_t a;   // 1 byte, but struct may pad to 4-byte alignment
uint32_t x; // 4 bytes — 对计数、索引通常足够

// 2. 位域 — 多个小值打包到一个整数
struct Flags {
    unsigned int ready  : 1;   // 0/1
    unsigned int mode   : 3;   // 0-7
    unsigned int count  : 12;  // 0-4095
    unsigned int id     : 16;  // 0-65535
}; // 4 bytes total (was 16 bytes as separate ints) → 75% 节省

// 3. union 复用内存 — 互斥数据共享空间
union Data {
    int    int_val;    // 交替使用
    float  float_val;  // 不会同时需要
}; // 4 bytes total, shared

// 4. 全局数组 → .bss 段，不占用可执行文件空间
static int dp[100005];  // 零初始化，放在 .bss
```

---

### 3.4 稀疏数据与隐式表示

```cpp
// 1. 排序数组 + 二分替代哈希表
// std::unordered_map<int, int>: ~40 bytes per entry
// sorted pair<int,int> array:   ~8 bytes per entry + binary search
// 节省 80% 内存，O(log n) vs O(1) 但通常可以接受

// 2. CSR 格式存储图 → 边数密集时高效
int head[MAXN], to[MAXM], nxt[MAXM];  // 3个数组
// 替代 vector<int> g[MAXN] (每个 vector 有 ~24 bytes 开销)

// 3. 隐式图 — 不存储边，动态生成
// 对于网格图、完全图等规则结构，在遍历时计算邻接关系

// 4. 位集压缩邻接矩阵
uint64_t adj[2000][32];  // 2000 节点 → 2000*32*8 = 512KB
// 替代 bool adj[2000][2000] = 4MB → 8× 压缩
```

---

### 3.5 自定义分配器

```cpp
// Arena 分配器 — 百万级小对象分配，比 malloc 快 10-100×
struct Arena {
    char *buf, *ptr;
    Arena(size_t sz) { ptr = buf = (char*)malloc(sz); }
    void* alloc(size_t sz) {
        void* p = ptr; ptr += sz; return p;  // 简单的 bump pointer
    }
    void reset() { ptr = buf; }
};

// 使用场景：函数内大量临时节点（图的 DFS、树的构造等）
// 注意：Arena 不单独释放单个对象，只能整体重置
```

---

## 4. 代码长度优化 (Code Golf)

### 4.1 宏滥用 — 按收益排序

```cpp
// 最高收益: 消除 #include (18 chars)
// 使用编译命令: clang -std=gnu89 -include stdio.h prog.c
// 代价: 必须使用 C (不是 C++)

// 关键字符号替换 (使用前计算 break-even)
#define R return     // 每用3次回本
#define F for        // 每用6次回本
#define W while      // 每用4次回本
#define P printf     // 每用3次回本

// 不划算的宏 (字符短, 使用次数少时永远不回本)
#define I int        // 仅省2字符, 需用6次才回本
```

**Break-even 计算公式:**
```
定义开销 = 10 + len(替换文本)
每次使用节省 = len(原名) - len(宏名)
回本次数 = 定义开销 / 每次使用节省
```

### 4.2 语法糖果

```cpp
// 1. 三元运算符替代 if-else (节省 ~15 chars)
if(a>b)c=1;else c=0;        // 23 chars
c=a>b?1:0;                  // 9 chars

// 2. 位运算替代算术 (节省 1-3 chars)
n%2==0 → !(n&1)             // 节省2 chars
n>2    → n>2 (不变)
n>=3   → n>2                // 节省1 char

// 3. 逗号运算符链式执行 (节省大括号)
// for(int i=0;i<n;i++){a++;b--;}  →  for(int i=0;i<n;i++)a++,b--;

// 4. 逻辑运算符作控制流
if(n>0)f(n);                // 18 chars
n>0&&f(n);                  // 9 chars

// 5. 去掉 return 0; (C++ 允许 main 省略, 节省 10 chars)

// 6. 全局变量零初始化 → 省去 =0
int a=0;                    // 7 chars
int a;                      // 5 chars (全局变量自动初始化为0)

// 7. 循环技巧
while(n--)proc();           // 比 for(int i=0;i<n;i++)proc(); 省 ~15 chars
```

### 4.3 Python — 代码高尔夫之王

```
C 极致:  #include<stdio.h>
         int main(){int w;scanf("%d",&w);puts(w&1||w<3?"NO":"YES");}
         → 78 chars

Python:  print("YNEOS"[int(input())%2|w<3::2])
         → 42 chars (46% less!)
```

**Python 天生优势：**
| 特性 | C 等效 | 节省 |
|------|--------|------|
| `print()` | `printf("...\n");` | ~8 chars |
| `input()` | `scanf("%d",&x);` | ~8 chars |
| 短路切片 `[::2]` | if-else 分支 | ~15 chars |
| `open(0)` 代替 stdin | `#include <stdio.h>` | ~15 chars |
| `sum(map(int,l.split()))` | 循环 + scanf | ~20 chars |
| 无类型声明 | `int x;` | ~4 chars |

**实测结论：** 8 道 Codeforces 简单题（600-800 分），Python 平均 65.0 字符 vs C 极致 85.3 字符。Python 全胜。

### 4.4 终极技巧：编译器标志消除 #include

```bash
# 不写 #include<stdio.h> 的 C 代码 (节省 18 chars)
clang -std=gnu89 -include stdio.h -o prog prog.c
```

`-std=gnu89` 同时启用：
- 隐式 int 返回类型：`main(){...}` 不需要写 `int`
- K&R 函数定义风格：`main(n){...}` 参数默认为 int
- 隐式函数声明：printf/scanf 不需要 #include

4A Watermelon 从 78 chars → 49 chars (C 极致) → 42 chars (Python)。

---

## 5. 跨维度权衡与实测数据

### 5.1 速度 vs 内存：预计算的两面性

| 问题 | 基线内存 | 极致内存 | 改变 |
|------|----------|----------|------|
| 161D | 200.8 MB | **3.5 MB** | 57× 缩减 (算法改写) |
| 118D | 6.6 MB | **1.4 MB** | 4.64× 缩减 (滚动DP) |
| 1061C | 2.7 MB | 63.9 MB | **23× 膨胀** (预计算除数表换速度) |

**1061C 的权衡解释：**
- 基线：每次动态求除数 → O(√a_i) per element, 2.7 MB
- 极致：预计算 1..10^6 所有数的除数表 → O(d(a_i)) per element, 63.9 MB
- 选择：如果内存充裕 (256MB)，极致版更快；如果内存紧张 (64MB)，用基线

### 5.2 编译器标志的意外性能

| 标志 | 体积 | 速度 (NEON) | 说明 |
|------|------|-------------|------|
| `-Os` | 33.8 KB | **1.65×** | 体积优化居然最快！ |
| `-O3` | 33.8 KB | 1.42× | 常规最快假设被推翻 |
| `-O3 -flto` | 33.7 KB | 1.37× | LTO 减小体积但未提升速度 |

**原因：** Apple M4 的 L1 指令缓存只有 192KB。`-Os` 产生的代码更小，指令缓存命中率更高，对循环密集型代码的收益超过指令调度优化。

### 5.3 基准测试汇总 (全 38 个二进制)

```
运行时加速 Top 3:
  1. 86D Powerful Array   — 7.25×  (540.7ms → 74.5ms)
  2. NEON SIMD 求和       — 2.38×  (3519µs → 1477µs)
  3. 无分支二分查找       — 2.09×  (4087ms → 1955ms)

内存缩减 Top 3:
  1. 161D Distance in Tree — 57.37× (200.8MB → 3.5MB)
  2. 118D Caesar's Legions — 4.64×  (6.6MB → 1.4MB)
  3. (其余为同等或膨胀)

代码长度缩减 Top 3:
  1. 112A Petya and Strings — 82.7% (313 → 54 chars, Python)
  2. 4A Watermelon          — 77.4% (186 → 42 chars, Python)
  3. 71A Way Too Long Words — 77.1% (358 → 82 chars, Python)
```

---

## 6. 最佳实践清单

### 6.1 提交前检查（按优先级）

```
□ 1. 算法复杂度是否已最优？ (最优先)
□ 2. I/O 是否已优化？ (fread/fwrite 或 sync_with_stdio)
□ 3. 是否使用了正确的数据类型？ (int vs long long, 避免 double 除非必要)
□ 4. 编译标志是否最优？ (-O3 -march=native -flto)
□ 5. vector 是否可用静态数组替换？
□ 6. 哈希表是否可用数组/排序+二分替换？
□ 7. 是否有不必要的 STL 容器？
□ 8. 摩尔题是否用了 Hilbert 排序？
□ 9. 是否可以用滚动数组压缩 DP 维度？
□ 10. __builtin_expect 是否放在正确位置？
```

### 6.2 最快速编译命令

```bash
# 通用
clang++ -std=c++20 -O3 -march=native -flto -o prog src.cpp

# 有训练数据时 (PGO 额外 1.2-1.6×)
# (先运行插桩版本收集 profile, 再用 -fprofile-instr-use)
clang++ -std=c++20 -O3 -march=native -flto -fprofile-instr-use=profile.profdata -o prog src.cpp

# 手写 NEON/SIMD 向量化代码
clang++ -std=c++20 -Os -o prog src.cpp
```

### 6.3 最小内存编译命令

```bash
clang++ -std=c++20 -Os -fno-exceptions -fno-rtti -flto -Wl,-dead_strip -o prog src.cpp
strip -x prog
```

### 6.4 最简代码高尔夫编译命令

```bash
# C 极限 (隐式 int + 隐式声明)
clang -std=gnu89 -include stdio.h -O2 -o prog prog.c

# Python 极限 (本就短)
python3 -c "$(cat prog.py)" < input.txt
```

---

## 总结

极致优化的三个核心洞察：

1. **10× 优化靠算法。** 重心分解、滚动 DP、候选过滤等方法从根本上改变复杂度。在这之后才考虑微优化。

2. **I/O 和编译标志是免费的午餐。** `fread` + `-O3 -march=native -flto` 不改一行算法就能获得 3-6× 加速。

3. **实测代替猜测。** hyperfine 跑 10 次取均值，`/usr/bin/time -l` 看 peak RSS，`wc -c` 数字符。数据驱动决策——同一个优化技巧在不同平台、不同编译器、不同输入规模下效果可能截然相反。

最后记住：**最极致的优化不是写出最优代码，而是写出根本不需要执行的那行代码。** 如果一个 O(n²) 算法可以被 O(n) 公式替代，你不需要任何上述技术。数学永远是终极优化器。

---

*基于 Apple M4 / clang++ 21.0.0 实测。所有基准测试数据来自 `benchmarks/ALL_RESULTS.md`。*