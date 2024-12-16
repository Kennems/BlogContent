---
title : 'MIT6.S081(3)-OS organization'
date : 2024-08-31T22:30:13+08:00
lastmod: 2024-08-31T22:20:13+08:00
description : "MIT6.S081(3)" 
draft : false    
categories : ["操作系统"]
tags : ["MIT6.S081"]
---

# MIT6.S081(3)-OS organization

## Lecture Topic:

###   OS design

​    system calls
​    micro/monolithic kernel
  First system call in xv6

### OS picture

  apps: sh, echo, ...
  system call interface (open, close,...)
        OS

### Goal of OS

  run multiple applications
  isolate them
  multiplex them
  share

Strawman design: No OS
  Application directly interacts with hardware
    CPU cores & registers
    DRAM chips
    Disk blocks
    ...
  OS library perhaps abstracts some of it

Strawman design not conducive to multiplexing
  each app periodically must give up hardware
  BUT, weak isolation
    app forgets to give up, no other app runs
    apps has end-less loop, no other app runs
    you cannot even kill the badly app from another app
  but used by real-time OSes
    "cooperative scheduling"

Strawman design not conducive to memory isolation
  all apps share physical memory
  one app can overwrites another apps memory
  one app can overwrite OS library

Unix interface conducive to OS goals
  abstracts the hardware in way that achieves goals
  processes (instead of cores): fork
     OS transparently allocates cores to processes
       Saves and restore registers
     Enforces that processes give them up
       Periodically re-allocates cores     
  memory (instead of physical memory): exec
     Each process has its "own" memory
     OS can decide where to place app in memory
     OS can enforce isolation between memory of different apps
     OS allows storing image in file system
  files (instead of disk blocks)
     OS can provide convenient names
     OS can allow sharing of files between processes/users
  pipes (instead of shared physical mem)
     OS can stop sender/receiver

OS must be defensive
  an application shouldn't be able to crash OS
  an application shouldn't be able to break out of its isolation
  => need strong isolation between apps and OS
  approach: hardware support
  - user/kernel mode
  - virtual memory

Processors provide user/kernel mode
  kernel mode: can execute "privileged" instructions
    e.g., setting kernel/user bit
    e.g., reprogramming timer chip
  user mode: cannot execute privileged instructions
  Run OS in kernel mode, applications in user mode
  [RISC-V has also an M mode, which we mostly ignore]

Processors provide virtual memory
  Hardware provides page tables that translate virtual address to physical
  Define what physical memory an application can access
  OS sets up page tables so that each application can access only its memory

Apps must be able to communicate with kernel
  Write to storage device, which is shared => must be protected => in kernel
  Exit app
  ...

Solution: add instruction to change mode in controlled way
  ecall <n>
  enters kernel mode at a pre-agreed entry point

Modify OS picture
  user / kernel (redline)
  app -> printf() -> write() -> SYSTEM CALL -> sys_write() -> ...
    user-level libraries are app's private business
  kernel internal functions are not callable by user

  other way of drawing picture:
  syscall 1  -> system call stub -> kernel entry -> syscall -> fs
  syscall 2                                                 -> proc

  system call stub executes special instruction to enter kernel
    hardware switches to kernel mode
    but only at an entry point specified by the kernel

  syscall need some way to get at arguments of syscall

  [syscalls the topic of this week's lab]

Kernel is the Trusted Computing Base (TCB)
  Kernel must be "correct"
    Bugs in kernel could allow user apps to circumvent kernel/user
      Happens often in practice, because kernels are complex
      See CVEs
  Kernel must treat user apps as suspect
    User app may trick kernel to do the wrong thing
    Kernel must check arguments carefully
    Setup user/kernel correctly
    Etc.
  Kernel in charge of separating applications too
    One app may try to read/write another app's memory
  => Requires a security mindset
    Any bug in kernel may be a security exploit

Aside: can one have process isolation WITHOUT h/w-supported
  kernel/user mode and virtual memory?
  yes! use a strongly-typed programming language
  - For example, see Singularity O/S
    the compiler is then the trust computing base (TCB)
    but h/w user/kernel mode is the most popular plan

Monolothic kernel
  OS runs in kernel space
  Xv6 does this.  Linux etc. too.
  kernel interface == system call interface
  one big program with file system, drivers, &c
  - good: easy for subsystems to cooperate
    one cache shared by file system and virtual memory
  - bad: interactions are complex
    leads to bugs
    no isolation within

Microkernel design
  many OS services run as ordinary user programs
    file system in a file server
  kernel implements minimal mechanism to run services in user space
    processes with memory
    inter-process communication (IPC)
  kernel interface != system call interface		
  - good: more isolation
  - bad: may be hard to get good performance
    both monolithic and microkernel designs widely used

Xv6 case study
  Monolithic kernel
    Unix system calls == kernel interface
  Source code reflects OS organization (by convention)
    user/    apps in user mode
    kernel/  code in kernel mode
  Kernel has several parts
    kernel/defs.h
       proc
       fs
       ..
  Goal: read source code and understand it (without consulting book)

Using xv6
  Makefile builds
    kernel program
    user programs
    mkfs
  $ make qemu
    runs xv6 on qemu
    emulates a RISC-V computer

Building kernel

```
  .c -> gcc -> .s -> .o  \
  ....                     ld -> a.out
  .c -> gcc -> .s -> .o  /
```

  makefile keeps .asm file around for binary
  see for example, kernel/kernel.asm

The RISC-V computer
  A very simple board (e.g., no display)

  - RISC-V processor with 4 cores
  - RAM (128 MB)
  - support for interrupts (PLIC, CLINT)
  - support for UART
    allows xv6 to talk to console
    allows xv6 to read from keyboard
  - support for e1000 network card (through PCIe)

Development using Qemu
  More convenient than using the real hardware
  Qemu emulates several RISC-V computers
  - we use the "virt" one
    https://github.com/riscv/riscv-qemu/wiki
  - close to the SiFive board (https://www.sifive.com/boards)
    but with virtio for disk

What is "to emulate"?
  Qemu is a C program that faithfully implements a RISC-V processor

```
  for (;;) {
    read next instructions
    decode instruction
    execute instruction (updating processor state)
  }
```

  [big idea: software = hardware]

Boot xv6 (under gdb)

```
  $ make CPUS=1 qemu-gdb
```

​    runs xv6 under gdb (with 1 core)
  Qemu starts xv6 in kernel/entry.S (see kernel/kernel.ld)
​    set breakpoint at _entry
​      look at instruction
​      info reg
​    set breakpoint at main
​      Walk through main
​    single step into userinit
​      Walk through userinit
​      show proc.h
​      show allocproc()
​      show initcode.S/initcode.asm
​    break forkret()
​      walk to userret
​    break syscall
​      print num
​      syscalls[num]
​      exec "/init"

## Operating system organization

| 文件名        | 描述                             |
| ------------- | -------------------------------- |
| bio.c         | 文件系统的磁盘块缓存。           |
| console.c     | 连接用户键盘和屏幕。             |
| entry.S       | 最早的引导指令。                 |
| exec.c        | `exec()` 系统调用。              |
| file.c        | 文件描述符支持。                 |
| fs.c          | 文件系统。                       |
| kalloc.c      | 物理页面分配器。                 |
| kernelvec.S   | 处理来自内核的陷阱和定时器中断。 |
| log.c         | 文件系统日志和崩溃恢复。         |
| main.c        | 控制启动时其他模块的初始化。     |
| pipe.c        | 管道。                           |
| plic.c        | RISC-V 中断控制器。              |
| printf.c      | 格式化输出到控制台。             |
| proc.c        | 进程和调度。                     |
| sleeplock.c   | 让出 CPU 的锁。                  |
| spinlock.c    | 不让出 CPU 的锁。                |
| start.c       | 机器模式的早期引导代码。         |
| string.c      | C 字符串和字节数组库。           |
| swtch.S       | 线程切换。                       |
| syscall.c     | 将系统调用分派给处理函数。       |
| sysfile.c     | 与文件相关的系统调用。           |
| sysproc.c     | 与进程相关的系统调用。           |
| trampoline.S  | 用户和内核之间切换的汇编代码。   |
| trap.c        | 处理并返回陷阱和中断的 C 代码。  |
| uart.c        | 串行端口控制台设备驱动程序。     |
| virtio_disk.c | 磁盘设备驱动程序。               |
| vm.c          | 管理页表和地址空间。             |

## [Lab: system calls](https://pdos.csail.mit.edu/6.S081/2020/labs/syscall.html)

## System call tracing ([moderate](https://pdos.csail.mit.edu/6.S081/2020/labs/guidance.html))

### /home/topeet/project/xv6-labs-2020/Makefile

```makefile
    $U/_wc\
    $U/_zombie\
    $U/_trace\
```

### /home/topeet/project/xv6-labs-2020/user/usys.pl

```perl
	entry("trace");
```

### ~/project/xv6-labs-2020/kernel/proc.c

在fork()函数内部添加如下代码：

```c
  // copy saved user registers.
  *(np->trapframe) = *(p->trapframe);
  np->tracemask = p->tracemask;

  // Cause fork to return 0 in the child.
  np->trapframe->a0 = 0;
```

### ~/project/xv6-labs-2020/kernel/proc.h

在结构体`proc`中添加`tracemask`属性

```c
  char name[16];               // Process name (debugging)
  int tracemask; // Trace Mask
};
```

### ~/project/xv6-labs-2020/kernel/syscall.c

处理系统调用（system call)的核心代码， 主要作用是实现系统调用的**参数提取**，**处理**和**结果返回**。

```c
    [SYS_mkdir] sys_mkdir,
    [SYS_close] sys_close,
    [SYS_trace] sys_trace};
```

按照参数顺序保存每个系统调用的名称，便于调试和输出。

```c
char *sys_name[] = {
    "fork",
    "exit",
    "wait",
    "pipe",
    "read",
    "kill",
    "exec",
    "fstat",
    "chdir",
    "dup",
    "getpid",
    "sbrk",
    "sleep",
    "uptime",
    "open",
    "write",
    "mknod",
    "unlink",
    "link",
    "mkdir",
    "close",
    "trace"};
```

在`syscall`函数中添加对应的逻辑：如果tracemask参数对应需要输出对应的system call， 则在调用之后根据tracemask打印输出。

```c
  if (num > 0 && num < NELEM(syscalls) && syscalls[num])
  {
    p->trapframe->a0 = syscalls[num]();

    if (p->tracemask >> (num) & 0x01 == 1)
    {
      printf("%d: syscall %s -> %d\n", p->pid, sys_name[num - 1], p->trapframe->a0);
    }
  }
```

### ~/project/xv6-labs-2020/kernel/syscall.h



```c
#define SYS_mkdir 20
#define SYS_close 21
#define SYS_trace 22
```

### ~/project/xv6-labs-2020/kernel/sysproc.c

当命令行输入命令后执行此函数

```c
uint64
sys_trace(void)
{
  int n;
  argint(0, &n);
  myproc()->tracemask = n;
  return 0;
}
```

### ~/project/xv6-labs-2020/user/user.h

```c
int sleep(int);
int uptime(void);
int trace(int);
```

## Sysinfo ([moderate](https://pdos.csail.mit.edu/6.S081/2020/labs/guidance.html))

实现`sysinfo`，同时重构上面的 `trace` 

### ~/project/xv6-labs-2020/Makefile

```makefile
	$U/_sysinfotest\
```

### ~/project/xv6-labs-2020/kernel/defs.h

```c
// sysinfo.c
int             process_number();
int             amount_free_memory(void);
```

### ~/project/xv6-labs-2020/kernel/kalloc.c

```c
// get free memory
int
amount_free_memory(void){
    struct run *r;
    r = kmem.freelist;
    int cnt = 0;
    for (; r; r = r->next) {
      cnt++;
    }
    return cnt*PGSIZE;
}
```

### ~/project/xv6-labs-2020/kernel/proc.c

```c
#include "sysinfo.h"
```

### ~/project/xv6-labs-2020/kernel/proc.h

```c
  char name[16];               // Process name (debugging)
  int tracemask; // Trace memory
};

struct sysinfo;
int trace(int mask);
void gather_sysinfo(struct sysinfo *info);
```

### ~/project/xv6-labs-2020/kernel/syscall.c

```c
    [SYS_trace] sys_trace,
    [SYS_sysinfo] sys_sysinfo
    };
```

```c
    "close",
    "trace",
    "sysinfo"
    };
```

```c
extern uint64 sys_uptime(void);
extern uint64 sys_trace(void);
extern uint64 sys_sysinfo(void);
```

### ~/project/xv6-labs-2020/kernel/syscall.h

```c
#define SYS_close  21
#define SYS_trace  22
#define SYS_sysinfo 23
```

### ~/project/xv6-labs-2020/kernel/sysproc.c

```c
#include "sysinfo.h"
```

### ~/project/xv6-labs-2020/user/user.h

```c
struct rtcdate;
struct sysinfo;
```

### ~/project/xv6-labs-2020/user/usys.pl

```c
entry("uptime");
entry("trace");
entry("sysinfo");
```

