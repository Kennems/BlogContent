---
title : 'Android内存管理知识点'
date : 2024-07-04T22:30:13+08:00
lastmod: 2024-07-041T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android内存管理知识点

### 问题 1: 什么是垃圾回收机制？

解答： 垃圾回收（GC）是由Java虚拟机（JVM）垃圾回收器提供的一种对内存回收的机制。它会在内存空间不足或者内存占用过高的时候，自动回收那些没有引用的对象，以释放内存资源。垃圾回收的主要目标是自动管理内存，避免内存泄漏和内存溢出。

### 问题 2: JVM的内存模型有哪些部分组成？

解答： JVM内存模型主要分为以下几部分：

- **程序计数器（Program Counter Register）**：记录当前线程执行的字节码指令地址。
- **本地方法栈（Native Method Stacks）**：用于执行本地方法。
- **JVM栈（JVM Stacks）**：每个线程一个栈，存储局部变量、操作数栈、动态链接和方法出口等。
- **堆（Heap）**：所有线程共享的内存区域，用于存储所有对象实例和数组。堆又分为年轻代（Young Generation）和老年代（Old Generation），其中年轻代又划分为Eden区、S0区和S1区。
- **方法区（Method Area）**：存储已被虚拟机加载的类信息、常量、静态变量和即时编译后的代码等。
- **运行时常量池（Runtime Constant Pool）**：方法区的一部分，用于存储编译期生成的各种字面量和符号引用。
- **直接内存（Direct Memory）**：并不是JVM规范的一部分，但被广泛使用，主要用于NIO（New Input/Output）中的缓冲区。

### 问题 3: 什么是引用计数算法？

解答： 引用计数算法是一种判断对象是否是垃圾的算法。对象被创建后，系统会给该对象初始化一个引用计数器，当该对象被引用时，计数器+1；当引用失效时，计数器-1。当计数器为0时，表示该对象不再被使用，可以进行回收。

### 问题 4: 引用计数算法的优缺点是什么？

解答：

- **优点**：判定比较简单，效率高。
- **缺点**：无法避免循环引用。例如，当对象A和对象B相互引用时，即使它们都不再被其他对象引用，其引用计数器也不会变为0，导致内存泄漏。

### 问题 5: 如何处理引用计数算法的循环引用问题？

解答： 引用计数算法无法直接解决循环引用问题。通常需要结合其他算法，如标记-清除算法（Mark-Sweep），来处理循环引用。标记-清除算法通过从根对象出发，遍历所有可达对象并进行标记，然后回收未被标记的对象。

### 补充问题 6: 常见的垃圾回收器有哪些？

解答：

- **Serial 收集器**：单线程收集，适用于单核处理器和内存较小的场景。
- **ParNew 收集器**：Serial 收集器的多线程版本，常用于多处理器系统。
- **Parallel Scavenge 收集器**：关注吞吐量，适用于后台运算而不要求高响应速度的场景。
- **CMS（Concurrent Mark-Sweep）收集器**：关注低延迟，适用于需要较快响应的应用。
- **G1（Garbage First）收集器**：适用于多核处理器和大内存场景，能够提供高吞吐量和低延迟。

### 补充问题 7: 如何检测和解决内存泄漏？

解答：

- **工具**：使用MAT（Memory Analyzer Tool）、VisualVM、JProfiler等工具。
- **方法**：通过分析内存快照，找到长时间存在的对象，检查它们的引用链，找出可能的内存泄漏点。
- **编码规范**：及时释放不再使用的资源，避免长时间持有对象引用，使用弱引用（WeakReference）、软引用（SoftReference）和虚引用（PhantomReference）来辅助垃圾回收。

### 面试提示

- 面试官可能会深入探讨垃圾回收器的具体实现，如Serial、Parallel、CMS、G1等不同类型的垃圾回收器。
- 可能会涉及内存泄漏的具体场景及排查工具和方法，如MAT（Memory Analyzer Tool）。
- 了解其他垃圾回收算法及其优缺点，如标记-清除算法、标记-压缩算法、分代收集算法等。
- 熟悉各种垃圾回收调优参数及其配置方法，如-Xms、-Xmx、-XX、-XX 等。

### 可达性分析法

**问题 1: 什么是可达性分析法？**

**解答：** 可达性分析法是一种垃圾回收算法，通过从根对象（GC Roots）开始，沿着引用链遍历整个对象图。如果一个对象到GC Roots没有任何引用链相连，则认为该对象不可达，可以被垃圾回收。

**问题 2: GC Roots包含哪些对象？**

**解答：** GC Roots包含以下几类对象：

- 虚拟机栈（Java线程栈）中引用的对象：例如栈帧中的本地变量表。
- 方法区中类静态属性引用的对象：例如静态变量引用的对象。
- 方法区中常量引用的对象：例如常量池中的引用。
- 本地方法栈中JNI（Native方法）引用的对象。

**问题 3: 在可达性分析中，如果一个对象不可达，是否立即被回收？**

**解答：** 不一定。即使一个对象在可达性分析中被判定为不可达，它也不会立即被回收。它将首先进入一个“缓冲阶段”（如在Java中会经历两次标记过程），以防止对象在缓冲阶段中被错误地回收。只有在缓冲阶段结束后，且对象仍然不可达时，才会被标记为真正的垃圾，进行回收。

### Java 中的对象引用

**问题 4: Java中的对象引用有几种类型？**

**解答：** Java中的对象引用有四种类型：

- 强引用（Strong Reference）：
  - 示例：`Object obj = new Object();`
  - 特点：最常见的引用类型，垃圾回收器不会回收这类对象，即使内存不足，也会抛出OutOfMemoryError错误。
- 软引用（Soft Reference）：
  - 特点：比强引用弱一些，当内存不足时，垃圾回收器会回收软引用指向的对象，以避免内存溢出。常用于实现缓存。
  - 示例：`SoftReference<Object> softRef = new SoftReference<>(new Object());`
- 弱引用（Weak Reference）：
  - 特点：比软引用更弱，只要垃圾回收器运行，不论内存是否充足，都会回收弱引用指向的对象。常用于防止内存泄漏。
  - 示例：`WeakReference<Object> weakRef = new WeakReference<>(new Object());`
- 虚引用（Phantom Reference）：
  - 特点：最弱的引用，不能通过虚引用来访问对象，唯一的作用是在对象被回收时收到一个系统通知，通常用来实现一些清理机制。
  - 示例：`PhantomReference<Object> phantomRef = new PhantomReference<>(new Object(), new ReferenceQueue<>());`

**问题 5: 软引用和弱引用的区别是什么？**

**解答：**

- 软引用：
  - 在内存不足时，垃圾回收器会回收软引用指向的对象，以避免内存溢出。
  - 适用于实现缓存，当内存足够时，保留对象；当内存不足时，回收对象。
- 弱引用：
  - 只要垃圾回收器运行，不论内存是否充足，都会回收弱引用指向的对象。
  - 适用于防止内存泄漏，如避免长生命周期对象引用短生命周期对象。

**问题 6: 什么是虚引用，有什么用处？**

**解答：**

- 虚引用（Phantom Reference）：
  - 也称为幽灵引用或幻影引用，是最弱的一种引用关系。
  - 作用：不能通过虚引用访问对象，主要用于在对象被垃圾回收时收到一个系统通知，从而进行相关清理操作。它为对象设置一个虚引用关联，这样对象被收集器回收时，会收到通知。

### 面试提示

- 面试官可能会深入探讨可达性分析法的具体实现和优化策略。
- 了解如何在实际项目中应用不同类型的引用，特别是软引用和弱引用的应用场景。
- 可以准备一些实际案例，说明如何使用这些引用类型来优化内存管理和防止内存泄漏。

### Java中的对象引用

**问题 1: Java中的对象引用有几种类型？请解释它们的特点。**

**解答：** Java中的对象引用有四种类型：

- **强引用（Strong Reference）**：
  - 示例：`Object obj = new Object();`
  - 特点：最常见的引用类型，垃圾回收器不会回收这类对象，即使内存不足，也会抛出OutOfMemoryError错误。
- **软引用（Soft Reference）**：
  - 特点：比强引用弱一些，当内存不足时，垃圾回收器会回收软引用指向的对象，以避免内存溢出。常用于实现缓存。
  - 示例：`SoftReference<Object> softRef = new SoftReference<>(new Object());`
- **弱引用（Weak Reference）**：
  - 特点：比软引用更弱，只要垃圾回收器运行，不论内存是否充足，都会回收弱引用指向的对象。常用于防止内存泄漏。
  - 示例：`WeakReference<Object> weakRef = new WeakReference<>(new Object());`
- **虚引用（Phantom Reference）**：
  - 特点：最弱的引用，不能通过虚引用来访问对象，唯一的作用是在对象被回收时收到一个系统通知，通常用来实现一些清理机制。
  - 示例：`PhantomReference<Object> phantomRef = new PhantomReference<>(new Object(), new ReferenceQueue<>());`

**问题 2: 软引用和弱引用的区别是什么？**

**解答：**

- **软引用**：
  - 在内存不足时，垃圾回收器会回收软引用指向的对象，以避免内存溢出。
  - 适用于实现缓存，当内存足够时，保留对象；当内存不足时，回收对象。
- **弱引用**：
  - 只要垃圾回收器运行，不论内存是否充足，都会回收弱引用指向的对象。
  - 适用于防止内存泄漏，如避免长生命周期对象引用短生命周期对象。

**问题 3: 什么是虚引用，有什么用处？**

**解答：**

- **虚引用（Phantom Reference）**也称为幽灵引用或幻影引用，是最弱的一种引用关系。
- 作用：不能通过虚引用访问对象，主要用于在对象被垃圾回收时收到一个系统通知，从而进行相关清理操作。虚引用与一个引用队列（ReferenceQueue）一起使用，当垃圾回收器准备回收一个对象时，如果发现它有虚引用，会在回收对象之前把这个虚引用加入到引用队列中。

### Android内存泄漏

**问题 1: 什么是内存泄漏？内存泄漏的原因是什么？**

**解答：** 内存泄漏是指程序中已分配的内存无法被释放或回收，导致内存资源逐渐耗尽的问题。内存泄漏的主要原因是短生命周期的对象被长生命周期的对象持有，导致短生命周期的对象无法回收。例如，Activity、Fragment、View或Service持有了一个短生命周期的对象并且未及时释放。

**问题 2: Android中哪些对象被长生命周期对象持有会导致内存泄漏？**

**解答：** 在Android中，以下对象被长生命周期对象持有会导致内存泄漏：

- **Activity**
- **Fragment**
- **View**
- **Service**

**问题 3: 如何检测和解决内存泄漏问题？**

**解答：** 可以使用工具如LeakCanary来检测和解决内存泄漏问题。LeakCanary是Square公司为Android开发者提供的一个自动检测内存泄漏的工具。它基于MAT进行Android应用程序内存泄漏的自动化检测。通过集成LeakCanary提供的jar包到自己的工程中，一旦检测到内存泄漏，LeakCanary会dump Memory信息，并通过另一个进程分析内存泄漏的信息并展示出来，帮助开发者及时发现和定位内存泄漏问题。

### 面试提示

- 面试官可能会深入探讨引用类型的具体实现和使用场景，特别是软引用和弱引用在实际项目中的应用。
- 对于内存泄漏部分，准备一些实际案例说明如何检测和解决内存泄漏问题，以及如何使用LeakCanary等工具进行内存管理优化。
- 了解内存泄漏的常见场景，如静态持有Activity、匿名内部类或非静态内部类、未取消的Handler或AsyncTask等。

### ANR（Application Not Responding）

**问题 1: 什么是ANR？**

**解答：** ANR的全称是Application Not Responding，即应用程序无响应。它指的是在Android应用的UI线程（主线程）中，有特定的消息（例如按键、触摸事件、广播、服务等）没有在规定时间内处理完毕，从而触发了ANR异常。

Android系统通过消息处理机制，确保在规定的时间间隔内处理消息，如果超过这个时间，系统将认为应用程序无响应，并触发ANR异常。

**问题 2: ANR有哪几种类型？请详细解释每种类型。**

**解答：** ANR有三种类型：

1. **KeyDispatchTimeout**：
   - 描述：当按键或触摸事件没有在特定时间内无响应时触发。
   - 默认时间：一般Android平台默认超时时间是5秒，但有些平台会修改这个时间，例如MTK平台超时时间为8秒。
2. **BroadcastTimeout**：
   - 描述：当BroadcastReceiver在规定时间内无法处理完广播消息时触发。
   - 默认时间：前台广播超时时间是10秒，后台广播超时时间是60秒。
3. **ServiceTimeout**：
   - 描述：当Service在规定时间内无法处理完操作时触发。
   - 默认时间：前台Service超时时间是20秒，后台Service超时时间是200秒。

**问题 3: 如何检测ANR问题？**

**解答：** ANR发生时，系统会在日志中输出错误信息，并生成一个`/data/anr/traces.txt`的文件。这个文件中包含当前应用进程和系统进程的函数堆栈信息，是分析ANR原因的关键文件。通过检查这个文件，可以看到当前的CPU使用率等信息，这些都是重要的线索。

**问题 4: 主线程执行耗时操作会导致ANR，请解释并举例说明。**

**解答：** 主线程（UI线程）执行耗时操作，例如大量计算、循环、递归、I/O操作等，会占用主线程的时间，导致无法及时处理UI更新和用户输入，从而引发ANR。

示例代码：

```
javaCopy codemBind.textView.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        while (true) {
            Log.i("TAG", "主线程执行耗时操作");
            try {
                Thread.sleep(500);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
});
```

**问题 5: 死锁会导致ANR，请解释并举例说明。**

**解答：** 死锁是指两个或多个线程互相等待对方释放资源，导致线程永久阻塞。主线程长时间无法获取到所需的资源锁，导致无法响应用户操作，从而引发ANR。

示例代码：

```java
javaCopy codepublic class MainThreadAnrActivity extends BaseActivity<ActivityHandlerBinding> {
    private final Object object = new Object();
    private final String TAG = MainThreadAnrActivity.class.getSimpleName();
    private boolean taskComplete = false;

    @Override
    protected void bindListener() {
        super.bindListener();
        mBind.textView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                synchronized (object) {
                    Log.i(TAG, "死锁导致ANR " + taskComplete);
                }
            }
        });
        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (object) {
                    try {
                        Thread.sleep(30 * 1000);
                        taskComplete = true;
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();
    }
}
```

### 面试提示

- 面试官可能会深入探讨ANR的具体类型和触发条件，了解如何分析和解决ANR问题。
- 准备一些实际项目中的ANR案例，说明如何检测、分析和解决这些问题。
- 熟悉ANR相关的日志文件（如`traces.txt`）的结构和内容，能够根据日志进行问题定位和分析。