---
title : 'MIT6.S081(8)-Page faults'
date : 2024-09-17T19:30:13+08:00
lastmod: 2024-09-17T19:20:13+08:00
description : "MIT6.S081(8)" 
categories : ["操作系统"]
tags : ["MIT6.S081"]
---

# MIT6.S081(8)-Page faults

* plan: cool things you can do with vm
  - Better performance/efficiency
    e.g., one zero-filled page
    e.g., copy-on-write fork
  - New features
    e.g., memory-mapped files
  
* virtual memory: several views
  * primary purpose: isolation
    each process has its own address space
  * Virtual memory provides a level-of-indirection
    provides kernel with opportunity to do cool stuff
	  already some examples:
	- shared trampoline page
	- guard page
	but more possible...
	
* Key idea: change page tables on page fault
  Page fault is a form of a trap (like a system call)
  Xv6 panics on page fault
    But you don't have to panic!
  Instead:
    update page table instead of panic
	restart instruction (see userret() from traps lecture)
  Combination of page faults and updating page table is powerful!
	
* RISC-V page faults
  3 of 16 exceptions are related to paging
  Exceptions cause controlled transfers to kernel
    See traps lecture
	
* Information we might need at page fault to do something interesting:
  1) The virtual address that caused the fault
      See stval register; page faults set it to the fault address
  2) The type of violation that caused the fault
      See scause register value (instruction, load, and Store page fault)
  3) The instruction and mode where the fault occurred
      User IP: tf->epc
      U/K mode: implicit in usertrap/kerneltrap
  
* lazy/on-demand page allocation
  * sbrk() is old fashioned;
    applications often ask for memory they need
    - for example, the allocate for the largest possible input but
      an application will typically use less
      if they ask for much, sbrk() could be expensive
    - for example, if all memory is in use, have to wait until
      kernel has evicted some pages to free up memory
      sbrk allocates memory that may never be used.
  * moderns OSes allocate memory lazily
    plan:
      allocate physical memory when application needs it
      adjust p->sz on sbrk, but don't allocate
      when application uses that memory, it will result in page fault
      on pagefault allocate memory
      resume at the fault instruction
    may use less memory
      if not used, no fault, no allocation
    spreads the cost of allocation over the page faults instead
    of upfront in sbrk()
  * demo
    modify sysproc.c
    modify trap.c
    modify vm.c

* one zero-filled page (zero fill on demand)
  * applications often have large part of memory that must zero
    global arrays, etc.
    the "block starting symbol" (bbs) segment
  * thus, kernel must often fill a page with zeros
  * idea: memset *one* page with zeros
    map that page copy-on-write when kernel needs zero-filled page
    on write make copy of page and map it read/write in app address space

* copy-on-write fork
  * observation:
    xv6 fork copies all pages from parent (see fork())
    but fork is often immediately followed by exec
  * idea: share address space between parent and child
    modify fork() to map pages copy-on-write
      use extra available system bits (RSW) in PTEs
    on page fault, make copy of page and map it read/write

* demand paging
  * observation: exec loads the complete file into memory (see exec.c)
    expensive: takes time to do so (e.g., file is stored on a slow disk)
    unnecessary: maybe not the whole file will be used
  * idea: load pages from the file on demand
    allocate page table entries, but mark them on-demand
    on fault, read the page in from the file and update page table entry
    need to keep some meta information about where a page is located on disk
      this information is typically in structure called virtual memory area (VMA)
  * challenge: file larger than physical memory (see next idea)

* use virtual memory larger than physical memory
  * observation: application may need more memory than there is physical memory
  * idea: store less-frequently used parts of the address space on disk
    page-in and page-out pages of the address address space transparently
  * works when working sets fits in physical memory
    most popular replacement strategy: least-recently used (LRU)
    the A(cess) bit in the PTE helps the kernel implementing LRU
  * demo: run top and vmstat
    on laptop and dialup.athena.mit.edu
    see VIRT RES MEM SHR columns
  
* memory-mapped files
  * idea: allow access to files using load and store
    can easily read and writes part of a file
    e.g., don't have to change offset using lseek system call
  * Unix systems a new system call for m-mapped files:
    void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
  * kernel page-in pages of a file on demand
    when memory is full, page-out pages of a file that are not frequently used

* shared virtual memory
  * idea: allow processes on different machines to share virtual memory
    gives the illusion of physical shared memory, across a network
  * replicate pages that are only read
  * invalidate copies on write
  
* TLB management
  CPUs caches paging translation for speed
  xv6 flushes entire TLB during user/kernel transitions
    why?
  RISC-V allows more sophisticated plans
    * PTE_G: global TLB bits
      what page could use this?
    * ASID numbers
      TLB entries are tagged with ASID, so kernel can flush selectively
      SATP takes an ASID number
      sfence.vma also takes an ASID number
    * Large pages
      2MB and 1GB

* Virtual memory is still evolving
  Recent changes in Linux
    PKTI to handle meltdown side-channel
      (https://en.wikipedia.org/wiki/Kernel_page-table_isolation)
    xv6 basically implements KPTI
  Somewhat recent changes
    Support for 5-level page tables (57 address bits!)
    Support for ASIDs
  Less recent changes
    Support for large pages
    NX (No eXecute) PTE_X flag

--- COW in Linux

https://lwn.net/Articles/849876/

## Page faults

### Virtual Memory Benefits

1. Isolation 
2. Level of Indirection

Va `->` Pa

### Information Needed

1. the faulting va ( va `->` stval register)
2. the type of page fault ( scause )
3. the va of instruction that cause the page fault (sepc, trapframe)

### Allocation : sbrk()

sbrk() -> eager allocation

applications tend to over ask.

### Lazy allocation

sbrk() do nothing

only grow p`->`sz 

PgFault : va < p`->`sz  > stack 

allocate 1 page 

zero the page 

map the page

restart instruction



## Copy-on-Write(Cow) - Fork

COW fork 是一种优化技术，核心思想是推迟（defer）和共享（share）物理内存的复制过程。

### 动机

传统 fork() 系统调用会完整复制父进程的所有内存页。但 fork 之后经常紧跟 exec()，这意味着复制的大量页面很快被丢弃，造成资源浪费。统计表明，许多子进程在 fork 后只使用极少的内存就执行 exec。

### 核心实现机制

1. **修改 fork() 的页表复制逻辑**
   - fork() 不再为子进程分配新的物理页并拷贝内容
   - 而是让父子进程共享同一物理页
   - 利用 PTE 中的 RSW (Reserved for Supervisor Software) 位标记 COW 状态
   - 将父子进程的 PTE 都设置为只读（read-only），清除 PTE_W 标志

2. **PTE 标记设计**
   - 在 xv6 的 RSW 位中定义一个 `PTE_COW` 标志位（如 bit 8）
   - 当 PTE_COW=1 且 PTE_W=0 时，表示该页是 COW 共享页
   - 任何对该页的写操作都会触发 page fault

3. **Page Fault 处理流程**
   - CPU 检测到对只读页的写入，触发 store page fault
   - usertrap() 识别 scause == 15 (store page fault)
   - 检查 faulting address 对应的 PTE 是否设置了 PTE_COW 标志
   - 如果是 COW 页，则：
     a. 分配一个新的物理页（kalloc）
     b. 将原物理页内容复制到新页（memmove）
     c. 更新进程的页表，映射到新物理页，设置 PTE_W 权限
     d. 清除 PTE_COW 标志
     e. 如果物理页引用计数降为1，原进程页表也更新为可写
   - 重新执行（retry）触发 fault 的指令

4. **引用计数管理**
   - 每个物理页需要维护引用计数（reference count）
   - kinit() 时初始化所有物理页的引用计数为 1
   - fork() 共享页时，对应物理页引用计数 +1
   - 页面回收时，只有当引用计数归零时才真正释放
   - 需要修改 kalloc() 和 kfree() 配合引用计数

### 内存布局示例

```
父进程地址空间            物理内存            子进程地址空间
    VA                   PA                   VA
  [页 A] ────COW共享───> [页 X] <───COW共享──── [页 A]
  [页 B] ────COW共享───> [页 Y] <───COW共享──── [页 B]
  
子进程写页 A 触发 fault 后：
  [页 A] ────可写──────> [页 X]  (父进程保留)
  [页 A'] ────可写──────> [页 Z]  (子进程新分配并复制)
```

### 优势

- **节省物理内存**：只读页面在父子进程间共享，避免重复占用
- **延迟开销**：复制操作推迟到真正写入时发生
- **提高 fork 速度**：fork 调用本身只需复制页表条目，不涉及物理页拷贝
- **支持按需复制**：从未被修改的页面可以一直共享

### 边界情况处理

- **引用计数溢出**：物理页最大引用数需考虑，xv6 中使用固定数组记录
- **多级共享**：多个进程通过多次 fork 共享同一物理页时需正确处理
- **COW 与 madvise 交互**：Linux 中 madvise(MADV_DONTNEED) 需要正确更新引用计数
- **缺页时内存不足**：如果 kalloc 失败，需要 kill 进程

## Zero-fill-on-demand

零填充按需分配（Zero-fill-on-demand）是一种优化虚拟内存中零初始化页面的技术。

### 问题背景

许多应用程序中有大量内存需要在初始化时填充为零：
- 全局数组和静态变量（BSS segment — Block Started by Symbol）
- 堆上新分配的内存（calloc / kmalloc 清零）
- 栈页面的初始状态

传统做法是每次分配页面时立即 memset 整个页（4096 字节），这会产生可观的 CPU 开销。

### 核心思想

1. **预清零的共享只读页**
   - 内核在启动时预先 memset 一个物理页为零
   - 将这个物理页保留为 "zero page"（零页）
   - 所有需要零填充的虚拟页面都映射到同一个零物理页
   - 映射设置为只读（read-only）

2. **按需写入时复制**
   - 当进程尝试写入零页时，触发 page fault
   - 在 fault handler 中分配新的物理页
   - 从零页复制内容（实际上也是零）到新页
   - 更新页面映射为可写（read-write）
   - 恢复执行触发 fault 的指令

### 在 xv6 中的实现

```
// kernel/vm.c
// 全局零页（在 kinit 时初始化）
char *zeropage;

// 初始化零页
void zeropage_init(void) {
    zeropage = kalloc();
    memset(zeropage, 0, PGSIZE);
}

// 映射零页（在进程创建或页面分配时调用）
void map_zeropage(pagetable_t pagetable, uint64 va, int perm) {
    uint64 pa = (uint64)zeropage;
    // 映射为零页，只读权限，加上 PTE_ZERO 标志
    perm = (perm & ~PTE_W) | PTE_ZERO;
    mappages(pagetable, va, PGSIZE, pa, perm);
}
```

### 优势

- **节省物理内存**：系统中所有零初始化页面共享同一物理页，仅占用 4KB
- **减少延迟**：页面映射操作是常数时间，无需立即清零
- **写时复制由硬件自动触发**：无需显式的表格或状态管理
- **缓存友好**：零页常驻 CPU cache，访问延迟低

### 与 COW fork 的对比

| 特性 | Zero-fill-on-demand | COW fork |
|------|-------------------|----------|
| 共享的对象 | 单个预清零页 | 父进程所有物理页 |
| 触发条件 | 写入零初始化页 | 父子进程任何一方写入 |
| 复制时机 | 首次写零页 | 首次写共享页 |
| 主要目的 | 避免大量 memset | 避免大量 memcpy |
| 内存节省 | 所有零页共享 4KB | 共享所有未修改页 |

### 在 Linux 中的实现

Linux 内核使用 `ZERO_PAGE(0)` 宏访问全局零页，定义为 `empty_zero_page`。mmap 的匿名映射（anonymous mapping）默认使用零页填充分配，当发生写操作时通过 do_anonymous_page() 触发 COW 处理。

## Demand paging

请求分页（Demand paging）是一种虚拟内存管理技术，只在页面被实际访问时才将其从磁盘加载到物理内存。

### 动机

传统的 exec() 系统调用会将整个程序文件从磁盘加载到内存中：
- 磁盘 I/O 非常慢（毫秒级），影响启动性能
- 程序可能只使用代码的一小部分（如错误处理路径通常不会执行）
- 内存浪费：未使用的页面占用了宝贵的物理内存

### 核心流程

1. **exec 时的延迟加载**
   - exec() 解析 ELF 文件头，遍历各 segment
   - 为每个 segment 创建页表项（PTE），但不分配物理页
   - 在 PTE 中设置有效位，但清除 PTE_V（valid）或使用自定义标志标记未加载
   - 将文件偏移和大小等信息记录在 VMA（Virtual Memory Area）中

2. **Page Fault 触发加载**
   - 当 CPU 首次访问该虚拟地址时，MMU 检测到无效 PTE
   - 触发 page fault，usertrap() 捕获到 scause == 13 (load) 或 15 (store)
   - 通过 faulting address 在 VMA 链表中查找对应的区域
   - 分配物理页（kalloc）
   - 从文件系统的 buffer cache 或磁盘读取页面内容到物理内存
   - 更新 PTE，设置为有效（valid）并赋予适当权限
   - 恢复执行触发 fault 的指令

3. **VMA（Virtual Memory Area）结构**
   ```c
   struct vma {
       uint64 addr;      // 虚拟地址起始
       uint64 sz;        // 区域大小
       uint64 offset;    // 文件内偏移
       int    fd;        // 对应的文件描述符
       int    perm;      // 权限标志
       int    flags;     // MAP_SHARED / MAP_PRIVATE
       struct file *fp;  // 文件结构体指针
   };
   ```

### 页面置换

当物理内存不足时，内核必须选择一些页面换出到磁盘：

1. **页面置换算法**
   - FIFO（First-In-First-Out）：简单但存在 Belady 异常
   - LRU（Least Recently Used）：利用 PTE 的 A（Access）位辅助判断
   - Clock / Second Chance：近似 LRU，开销更低
   - LFU（Least Frequently Used）：按访问频率排序

2. **PTE 中的管理位**
   - PTE_A（Accessed bit）：由硬件在页面被访问时自动置位，内核定期检查并清除
   - PTE_D（Dirty bit）：由硬件在页面被写入时置位，标识需要写回磁盘
   - 利用 RSW 位存储换出信息（如磁盘块号）

3. **Swap 空间管理**
   - 磁盘上预留 swap 分区或 swap 文件
   - 换出时将物理页内容写入 swap，记录磁盘位置到 PTE 的保留位
   - 换入时从 swap 读取内容，分配新物理页，更新 PTE

### 性能考量

- **工作集（Working Set）**：进程当前活跃使用的页面集合
- **抖动（Thrashing）**：当物理内存不足以容纳工作集时，系统频繁换入换出，CPU 利用率急剧下降
- **预取（Prefetching）**：根据局部性原理，在缺页时一次读入相邻页面
- **页面大小**：大页面（2MB/1GB）减少页表项数量，但增加内部碎片



### shared virtual memory





# [Lab: xv6 lazy page allocation](https://pdos.csail.mit.edu/6.S081/2020/labs/lazy.html)

## Eliminate allocation from sbrk() ([easy](https://pdos.csail.mit.edu/6.S081/2020/labs/guidance.html))

![image-20240917170736968](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917170736968.png)

## Lazy allocation ([moderate](https://pdos.csail.mit.edu/6.S081/2020/labs/guidance.html))

![image-20240917171019615](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171019615.png)

![image-20240917171044276](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171044276.png)

![image-20240917171125065](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171125065.png)

![image-20240917171154524](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171154524.png)

## Lazytests and Usertests ([moderate](https://pdos.csail.mit.edu/6.S081/2020/labs/guidance.html))

![image-20240917171215373](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171215373.png)

![image-20240917171241017](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171241017.png)

![image-20240917171300075](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171300075.png)

![image-20240917171314378](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240917171314378.png)
