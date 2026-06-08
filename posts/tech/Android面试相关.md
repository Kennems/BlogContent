---
title : 'Android面试相关'
date : 2026-03-22T20:43:04+08:00
lastmod: 2026-05-04T12:51:46+08:00
description : "- 说下Activity生命周期"
image : img/cat.jpg
draft : false
categories : ["Android"]
tags : ["学习笔记", "Android"]
---
# Android面试相关

## 1.Activity

- **说下Activity生命周期**

  Activity 有 7 个核心生命周期回调：

  1. `onCreate()` — 系统首次创建 Activity 时调用，进行基础初始化（绑定数据、关联 ViewModel、实例化变量）
  2. `onStart()` — Activity 进入"已启动"状态，对用户可见
  3. `onResume()` — Activity 进入前台，可与用户交互
  4. `onPause()` — 用户离开 Activity 的第一个信号，Activity 不再处于前台但仍可能可见（多窗口模式）。执行非常短暂，不要做耗时操作
  5. `onStop()` — Activity 不再可见，可执行较重的关闭操作（如保存数据到数据库）
  6. `onRestart()` — 已停止的 Activity 重新被带回前台时调用（在 onStop 和 onStart 之间）
  7. `onDestroy()` — Activity 被销毁前调用，释放所有资源

  生命周期流程：`Created → Started → Resumed → Paused → Stopped → Destroyed`
  从停止状态恢复：`Stopped → onRestart() → Started → Resumed`

  > 参考：[Android Developers - Activity lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)

- **Activity A 启动另一个Activity B 会调用哪些方法？如果B是透明主题的又或者是个DialogActivity呢**

  1. A 启动 B 的调用顺序：A.onPause() → B.onCreate() → B.onStart() → B.onResume() → A.onStop()（当 A 不再可见时）
  2. 如果 B 是透明主题或 DialogActivity：A 不会调用 onStop()，因为 A 仍然可见。调用顺序为：A.onPause() → B.onCreate() → B.onStart() → B.onResume()

- **说下onSaveInstanceState()方法的作用 ? 何时会被调用？**

  `onSaveInstanceState()` 用于保存 Activity 的临时 UI 状态（如用户输入、滚动位置等）。在以下情况会被调用：
  - 系统可能销毁 Activity 之前（如旋转屏幕、多窗口模式切换）
  - 用户按 Home 键或切换到其他应用时
  - 系统内存不足需要回收后台 Activity 时

  调用时机：在 `onStop()` **之前**调用。

  注意：当用户主动关闭 Activity（按返回键或调用 finish()）时，**不会**调用此方法。

  > 参考：[Android Developers - Activity lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)

- **Activity的启动流程**

  Activity 的启动流程涉及多个系统组件：
  1. 调用 `startActivity()` → 通过 Binder 通知 AMS（ActivityManagerService）
  2. AMS 检查权限、解析 Intent、确定目标 Activity
  3. AMS 通过 ApplicationThread 通知目标进程
  4. 目标进程的 ActivityThread 收到消息后，通过 Handler 切换到主线程
  5. 依次调用目标 Activity 的 `onCreate()`、`onStart()`、`onResume()`

- **onSaveInstanceState(),onRestoreInstanceState的调用时机**

  - `onSaveInstanceState()`：在 `onStop()` **之前**调用
  - `onRestoreInstanceState()`：在 `onStart()` 之后调用，只有在确实有保存的状态时才会被调用（Bundle 不为 null）

  > 参考：[Android Developers - Activity lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)

- **activity的启动模式和使用场景**

  有四种启动模式：
  1. **standard**（默认）：每次启动都创建新实例，适用于大多数场景
  2. **singleTop**：如果栈顶已有实例，则调用 `onNewIntent()` 而非创建新实例。适用于搜索结果页等不需要重复创建的场景
  3. **singleTask**：在任务栈中只存在一个实例，启动时会清除其上方所有 Activity。适用于应用主页
  4. **singleInstance**：独占一个任务栈，适用于来电显示、闹钟等独立功能

- **Activity A跳转Activity B，再按返回键，生命周期执行的顺序**

  1. A 跳转 B：A.onPause() → B.onCreate() → B.onStart() → B.onResume() → A.onStop()
  2. 按返回键：B.onPause() → A.onStart()（如果 A 被回收则走 onCreate）→ A.onResume() → B.onDestroy()

- **横竖屏切换,按home键,按返回键,锁屏与解锁屏幕,跳转透明Activity界面,启动一个 Theme 为 Dialog 的 Activity，弹出Dialog时Activity的生命周期**

  | 场景 | 生命周期变化 |
  |------|-------------|
  | 横竖屏切换 | onPause() → onStop() → onDestroy() → onCreate() → onStart() → onResume() |
  | 按 Home 键 | onPause() → onStop() → onSaveInstanceState() |
  | 按返回键 | onPause() → onStop() → onDestroy() |
  | 锁屏 | onPause() → onSaveInstanceState()（不一定调用 onStop） |
  | 解锁屏幕 | onStart() → onResume() |
  | 跳转透明 Activity | A.onPause() → B.onCreate() → B.onStart() → B.onResume()（A 不调用 onStop） |
  | Dialog 主题 Activity | 同透明 Activity，A 不调用 onStop |
  | 弹出普通 Dialog | 不触发任何生命周期变化 |

- **onStart 和 onResume、onPause 和 onStop 的区别**

  - `onStart()` vs `onResume()`：onStart 时 Activity 对用户可见；onResume 时 Activity 到达前台可与用户交互
  - `onPause()` vs `onStop()`：onPause 时 Activity 不再处于前台但仍可能可见（如多窗口模式）；onStop 时 Activity 完全不可见

  > 参考：[Android Developers - Activity lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)

- **Activity之间传递数据的方式Intent是否有大小限制，如果传递的数据量偏大，有哪些方案**

  Intent 传递数据有大小限制（通常约 1MB），超过会抛出 `TransactionTooLargeException`。解决方案：
  1. 使用全局变量或单例传递
  2. 使用 EventBus 或 LiveData 等事件总线
  3. 使用数据库或文件存储
  4. 使用 SharedPreferences
  5. 使用 ViewModel（同一 Activity 内的 Fragment 间）

- **Activity的onNewIntent()方法什么时候会执行**

  `onNewIntent()` 在以下情况执行：
  - Activity 的启动模式为 singleTop 且已在栈顶
  - Activity 的启动模式为 singleTask 且已存在
  - Activity 的启动模式为 singleInstance 且已存在

- **显示启动和隐式启动**

  - **显式启动**：明确指定目标 Activity 的类名，如 `Intent(this, TargetActivity::class.java)`
  - **隐式启动**：通过 Action、Category、Data 等条件匹配目标 Activity，如 `Intent(Intent.ACTION_VIEW)`

- **scheme使用场景,协议格式,如何使用**

  Scheme 是一种页面内跳转协议，格式：`scheme://host:port/path?query`
  - 使用场景：H5 与 Native 互调、App 间跳转、推送通知跳转
  - 使用方式：在 AndroidManifest 中配置 intent-filter，通过 Intent 解析 URI 跳转

- **ANR 的四种场景**

  1. **输入事件超时**：5 秒内未响应输入事件（按键、触摸）
  2. **BroadcastReceiver 超时**：前台 BroadcastReceiver 的 `onReceive()` 必须在 5 秒内完成
  3. **Service 超时**：`Service.onCreate()`、`onStartCommand()`、`onBind()` 必须在几秒内完成；使用 `startForegroundService()` 必须在 5 秒内调用 `startForeground()`
  4. **JobScheduler 超时**：`JobService.onStartJob()` 必须在几秒内返回

  > 参考：[Android Developers - ANR](https://developer.android.com/topic/performance/vitals/anr)

- **onCreate和onRestoreInstance方法中恢复数据时的区别**

  - `onCreate()`：需要判断 Bundle 是否为 null
  - `onRestoreInstanceState()`：Bundle 一定不为 null，无需判断

- **Activity间传递数据的方式**

  1. Intent 传递（putExtra）
  2. Bundle 传递
  3. 全局变量/单例
  4. SharedPreferences
  5. 数据库
  6. EventBus/LiveData
  7. 文件传递

- **跨App启动Activity的方式,注意事项**

  方式：
  1. 隐式 Intent
  2. Scheme 协议
  3. PendingIntent

  注意事项：
  - Android 5.0+ 不能隐式启动其他 App 的 Service
  - 需要添加 `FLAG_ACTIVITY_NEW_TASK` 标志
  - 注意权限检查

- **Activity任务栈是什么**

  任务栈（Task Stack/Back Stack）是一种后进先出（LIFO）的数据结构，用于管理 Activity。用户按返回键时，栈顶 Activity 被弹出并销毁，显示前一个 Activity。

- **有哪些Activity常用的标记位Flags**

  - `FLAG_ACTIVITY_NEW_TASK`：在新的任务栈中启动 Activity
  - `FLAG_ACTIVITY_SINGLE_TOP`：类似 singleTop 启动模式
  - `FLAG_ACTIVITY_CLEAR_TOP`：清除目标 Activity 上方的所有 Activity
  - `FLAG_ACTIVITY_CLEAR_TASK`：清空任务栈
  - `FLAG_ACTIVITY_NO_HISTORY`：Activity 不保留在任务栈中

- **Activity的数据是怎么保存的,进程被Kill后,保存的数据怎么恢复的**

  1. 保存：通过 `onSaveInstanceState()` 将数据保存到 Bundle 中，系统通过 Binder 将数据存储到 AMS 进程
  2. 恢复：进程重建后，AMS 通过 Binder 将之前保存的 Bundle 传递回来，在 `onCreate()` 或 `onRestoreInstanceState()` 中恢复
## 2.Service

- **service 的生命周期，两种启动方式的区别**

  Service 有两种启动方式，生命周期不同：

  **startService 启动：** onCreate() → onStartCommand() → onDestroy()
  **bindService 启动：** onCreate() → onBind() → onUnbind() → onDestroy()

  | 特性 | startService | bindService |
  |------|-------------|-------------|
  | 生命周期 | 运行到被显式停止 | 只在绑定期间存活 |
  | 通信方式 | 单向（基于 Intent） | 双向（通过 IBinder 接口） |
  | 多次调用 | 多次 onStartCommand() | 多个客户端可同时绑定 |
  | 停止方式 | stopSelf() 或 stopService() | 所有客户端解绑后自动销毁 |
  | 使用场景 | 后台独立任务 | 需要与组件交互 |

- **如何保证Service不被杀死 ？**

  1. **前台服务**：通过 `startForeground()` 显示持久通知，系统很少杀死前台服务
  2. **返回 START_STICKY**：`onStartCommand()` 返回此值，系统会在资源可用时重建服务
  3. **双进程守护**：通过两个 Service 相互拉活（不推荐，Android 高版本已限制）
  4. **使用 WorkManager**：对于需要保证执行的任务，使用 Jetpack WorkManager
  5. **账号同步**：利用 AccountSync 机制保活（不推荐）

- **Service与Activity怎么实现通信**

  1. **通过 Binder**：bindService 返回自定义 Binder 对象，Activity 通过它调用 Service 方法
  2. **通过 Messenger**：使用 Handler 创建 Messenger，实现跨进程通信
  3. **通过 AIDL**：适用于复杂的跨进程通信
  4. **通过广播**：Service 发送广播，Activity 注册接收器
  5. **通过 EventBus/LiveData**：使用事件总线或 LiveData 观察数据变化

- **IntentService是什么,IntentService原理，应用场景及其与Service的区别**

  **注意：IntentService 已在 Android 11 废弃**，推荐使用 WorkManager 替代。

  - IntentService 是 Service 的子类，用于处理异步请求
  - 原理：内部使用 HandlerThread 处理请求，每次处理一个 Intent，处理完自动停止
  - 与 Service 区别：IntentService 自动创建工作线程、自动处理 Intent 队列、自动停止
  - 应用场景：文件下载、图片处理等一次性后台任务

- **Service 的 onStartCommand 方法有几种返回值?各代表什么意思?**

  三种返回值：
  1. `START_NOT_STICKY`：服务被杀死后不会重建，除非有待处理的 Intent
  2. `START_STICKY`：服务被杀死后系统重建，但不保留之前的 Intent（适合媒体播放器）
  3. `START_REDELIVER_INTENT`：服务被杀死后系统重建，并重新传递最后一个 Intent（适合下载）

- **bindService和startService混合使用的生命周期以及怎么关闭**

  混合使用时生命周期：
  1. 先 startService：onCreate() → onStartCommand()
  2. 再 bindService：onBind()（不再调用 onCreate）
  3. 关闭时需要同时调用 stopService() 和 unbindService()
  4. 只有两者都调用后，Service 才会走 onDestroy()

- **用过哪些系统Service ？**

  常用系统服务：
  - `WindowManager`：管理窗口
  - `ActivityManager`：管理 Activity 和进程
  - `NotificationManager`：管理通知
  - `AlarmManager`：定时任务
  - `PowerManager`：电源管理
  - `LocationManager`：定位服务
  - `ConnectivityManager`：网络连接
  - `Vibrator`：震动服务
  - `SensorManager`：传感器管理
  - `ClipboardManager`：剪贴板

- **了解ActivityManagerService吗？发挥什么作用**

  AMS 是 Android 系统的核心服务之一，主要作用：
  1. **Activity 管理**：负责 Activity 的启动、切换、生命周期管理
  2. **进程管理**：创建和销毁进程，管理进程优先级
  3. **任务栈管理**：维护 Activity 任务栈
  4. **内存管理**：在内存不足时杀死低优先级进程
  5. **权限检查**：检查组件间调用的权限

  AMS 通过 Binder 机制与应用进程通信，是系统服务与应用之间的桥梁。

## 3.BroadcastReceiver

- **广播的分类和使用场景**

  广播分为两种：
  1. **有序广播**：通过 `sendOrderedBroadcast()` 发送，按优先级依次传递，接收器可以传播结果或终止广播
  2. **普通广播**：通过 `sendBroadcast()` 发送，同时发送给所有接收器，效率高但无法终止

  使用场景：
  - 系统广播：开机、网络变化、电量变化等
  - 应用内通信：组件间消息传递
  - 跨应用通信：通知其他应用数据更新

- **广播的两种注册方式的区别**

  1. **动态注册**（代码注册）：
     - 使用 `registerReceiver()` 注册
     - 只在注册的 Context 存活期间有效
     - 需要手动调用 `unregisterReceiver()` 取消注册
     - 可以注册所有广播

  2. **静态注册**（Manifest 注册）：
     - 在 AndroidManifest.xml 中声明
     - 应用未启动也能接收广播
     - Android 8.0+ 限制：大部分隐式广播不允许静态注册

- **广播发送和接收的原理**

  1. 发送方调用 `sendBroadcast()` → 通过 Binder 通知 AMS
  2. AMS 查找所有匹配的接收器
  3. 对于动态注册的接收器：直接回调 `onReceive()`
  4. 对于静态注册的接收器：AMS 通过 ApplicationThread 通知目标进程，创建进程后回调 `onReceive()`
  5. `onReceive()` 在主线程执行，必须快速完成

- **本地广播和全局广播的区别**

  | 特性 | 本地广播 | 全局广播 |
  |------|---------|---------|
  | 范围 | 应用内 | 系统级 |
  | 安全性 | 高（不泄露数据） | 低（可能被其他应用接收） |
  | 效率 | 高（无需跨进程） | 低（需要 IPC） |
  | 实现 | LocalBroadcastManager（已废弃） | Context.registerReceiver() |

  **注意**：LocalBroadcastManager 已废弃，推荐使用 LiveData、Flow 或应用内事件总线替代。

## 4.ContentProvider

- **什么是ContentProvider及其使用**

  ContentProvider 是 Android 四大组件之一，用于在不同应用间共享数据。它提供统一的接口来访问结构化数据。

  使用步骤：
  1. 创建 ContentProvider 子类，实现 CRUD 方法
  2. 在 AndroidManifest.xml 中注册，声明 authorities
  3. 其他应用通过 ContentResolver 访问数据

  ```kotlin
  // 查询数据
  val cursor = contentResolver.query(
      Uri.parse("content://com.example.provider/users"),
      arrayOf("id", "name"),
      "id > ?",
      arrayOf("0"),
      "name ASC"
  )
  ```

- **ContentProvider的权限管理**

  1. **读写权限**：通过 `android:readPermission` 和 `android:writePermission` 属性控制
  2. **临时权限**：通过 `grantUriPermission` 授予临时访问权限
  3. **路径级权限**：通过 `<path-permission>` 对不同路径设置不同权限
  4. **运行时权限**：Android 6.0+ 需要动态申请危险权限

- **ContentProvider,ContentResolver,ContentObserver之间的关系**

  - **ContentProvider**：数据提供者，实现 CRUD 操作
  - **ContentResolver**：数据访问者，通过 URI 访问 ContentProvider
  - **ContentObserver**：数据观察者，监听数据变化并收到通知

  关系：ContentResolver 通过 URI 找到对应的 ContentProvider，ContentObserver 通过 ContentResolver 注册监听。

- **ContentProvider的实现原理**

  1. ContentProvider 在应用启动时由 ActivityThread 创建
  2. 通过 Binder 机制暴露接口给其他应用
  3. ContentResolver 通过 URI 解析找到对应的 ContentProvider
  4. 跨进程调用通过 Binder 完成

- **ContentProvider的优点**

  1. 统一的数据访问接口
  2. 跨进程数据共享
  3. 细粒度的权限控制
  4. 数据变化通知机制
  5. 与 Android 系统组件无缝集成

- **Uri 是什么**

  URI（统一资源标识符）用于标识 ContentProvider 中的数据，格式：
  ```
  content://authority/path/id
  ```

  - `content://`：协议前缀
  - `authority`：ContentProvider 的唯一标识
  - `path`：数据表名
  - `id`：具体记录 ID

  示例：`content://com.example.provider/users/1`

## 5.Handler

- **Handler的实现原理**

  Handler 机制基于四个核心组件：
  1. **Handler**：消息发送者和处理者
  2. **MessageQueue**：消息队列，按时间排序存储消息
  3. **Looper**：消息循环，不断从 MessageQueue 取消息
  4. **Message**：消息载体

  工作流程：Handler.sendMessage() → MessageQueue.enqueueMessage() → Looper.loop() 取出消息 → Handler.dispatchMessage() → handleMessage()

- **子线程中能不能直接new一个Handler,为什么主线程可以主线程的Looper第一次调用loop方法,什么时候,哪个类**

  子线程不能直接 new Handler，因为子线程没有 Looper。主线程可以是因为：
  1. 主线程的 Looper 在 `ActivityThread.main()` 中创建
  2. `Looper.prepareMainLooper()` 创建主线程 Looper
  3. `Looper.loop()` 开始消息循环

  子线程使用 Handler 需要先调用 `Looper.prepare()` 和 `Looper.loop()`。

- **Handler导致的内存泄露原因及其解决方案**

  原因：非静态内部类持有外部类引用，Handler 的生命周期比 Activity 长。

  解决方案：
  1. 使用静态内部类 + 弱引用
  2. 在 onDestroy() 中调用 `handler.removeCallbacksAndMessages(null)`
  3. 使用 Lifecycle-aware 的 Handler

- **一个线程可以有几个Handler,几个Looper,几个MessageQueue对象**

  - Handler：可以有多个
  - Looper：只能有一个（通过 ThreadLocal 保证）
  - MessageQueue：只能有一个（一个 Looper 对应一个 MessageQueue）

- **Message对象创建的方式有哪些 & 区别？**

  1. `new Message()`：直接创建，不推荐
  2. `Message.obtain()`：从消息池获取，推荐使用
  3. `Handler.obtainMessage()`：从消息池获取，自动设置 target

  推荐使用 `Message.obtain()`，避免重复创建对象。

- **Message.obtain()怎么维护消息池的Handler 有哪些发送消息的方法**

  消息池是一个单链表结构，最大容量 50 个。`obtain()` 从链表头部取出消息，`recycle()` 将消息放回链表头部。

  Handler 发送消息的方法：
  - `sendMessage(Message)`
  - `sendMessageDelayed(Message, long)`
  - `sendMessageAtTime(Message, long)`
  - `post(Runnable)`
  - `postDelayed(Runnable, long)`
  - `postAtTime(Runnable, long)`

- **Handler的post与sendMessage的区别和应用场景**

  - `post()`：发送 Runnable，最终封装成 Message
  - `sendMessage()`：直接发送 Message

  本质相同，post 内部会将 Runnable 封装成 Message。应用场景：
  - post：适合执行简单任务
  - sendMessage：适合需要携带数据的消息

- **handler postDealy后消息队列有什么变化，假设先 postDelay 10s, 再postDelay 1s, 怎么处理这2条消息**

  MessageQueue 按消息的执行时间排序（而非插入顺序）。先 postDelay 10s 再 postDelay 1s，1s 的消息会排在前面先执行。

- **MessageQueue是什么数据结构**

  MessageQueue 是一个按时间排序的**单链表**结构。消息按 `when`（执行时间）排序，时间小的排在前面。

  > 参考：[Android Developers - Handler](https://developer.android.com/reference/android/os/Handler)

- **Handler怎么做到的一个线程对应一个Looper，如何保证只有一个MessageQueue ThreadLocal在Handler机制中的作用**

  通过 `ThreadLocal` 实现：
  1. 每个线程有一个 ThreadLocal 对象
  2. `Looper.prepare()` 将 Looper 存储到 ThreadLocal
  3. 同一个线程再次调用 `prepare()` 会抛出异常，保证唯一性
  4. 一个 Looper 对应一个 MessageQueue

- **HandlerThread是什么 & 好处 &原理 & 使用场景**

  HandlerThread 是带有 Looper 的线程：
  - 原理：继承 Thread，在 `run()` 中创建 Looper 并开启消息循环
  - 好处：避免频繁创建/销毁线程，提供串行执行能力
  - 使用场景：后台串行任务、IntentService 内部使用

- **IdleHandler及其使用场景**

  IdleHandler 是 MessageQueue 中的空闲回调，在消息队列空闲时执行。

  使用场景：
  1. 延迟初始化
  2. 性能监控
  3. 页面绘制完成后执行任务

- **消息屏障,同步屏障机制**

  消息屏障是一种优先处理异步消息的机制：
  1. 插入消息屏障（`postSyncBarrier()`）后，MessageQueue 优先处理异步消息
  2. 同步消息被阻塞，直到屏障移除
  3. 用于保证 UI 绘制消息（异步）优先执行

- **子线程能不能更新UI**

  原则上不能，但有特殊情况：
  1. ViewRootImpl 的 `checkThread()` 检查线程
  2. 在 `onCreate()` 中可以更新 UI，因为此时 ViewRootImpl 还未创建
  3. 使用 `View.post()` 或 `runOnUiThread()` 可以在子线程更新 UI

- **为什么Android系统不建议子线程访问UI**

  1. Android UI 控件不是线程安全的
  2. 多线程并发访问会导致数据不一致
  3. 加锁会影响性能

- **Android中为什么主线程不会因为Looper.loop()里的死循环卡死**

  1. Looper.loop() 的死循环是消息循环的核心
  2. 没有消息时，线程通过 `epoll` 机制阻塞在 `nativePollOnce()`
  3. 有消息时被唤醒处理消息
  4. 这种阻塞不会消耗 CPU 资源

- **MessageQueue#next 在没有消息的时候会阻塞，如何恢复？**

  1. 通过 `nativePollOnce()` 阻塞在 native 层
  2. 新消息插入时调用 `nativeWake()` 唤醒
  3. 唤醒后重新取消息

- **Handler消息机制中，一个looper是如何区分多个Handler的**

  每个 Message 都有 `target` 字段，指向发送它的 Handler。Looper 取出消息后，调用 `msg.target.dispatchMessage()` 分发给对应的 Handler。

- **当Activity有多个Handler的时候，怎么样区分当前消息由哪个Handler处理**

  通过 Message 的 `target` 字段区分，每个 Message 在发送时就已经绑定了对应的 Handler。

- **处理message的时候怎么知道是去哪个callback处理的**

  `dispatchMessage()` 的处理顺序：
  1. Message.callback（Runnable）
  2. Handler.mCallback
  3. Handler.handleMessage()

- **Looper.quit/quitSafely的区别**

  - `quit()`：立即退出，丢弃所有未处理的消息
  - `quitSafely()`：处理完已有消息后退出

- **通过Handler如何实现线程的切换**

  1. 在主线程创建 Handler
  2. 在子线程通过该 Handler 发送消息
  3. 消息在主线程的 Looper 中处理，实现线程切换

- **Handler 如何与 Looper 关联的**

  Handler 构造时获取当前线程的 Looper：`mLooper = Looper.myLooper()`

- **Looper 如何与 Thread 关联的**

  通过 ThreadLocal：`ThreadLocal.set(looper)` 将 Looper 存储到当前线程的 ThreadLocalMap

- **Looper.loop()源码**

  核心逻辑：
  ```java
  for (;;) {
      Message msg = queue.next(); // 阻塞取消息
      if (msg == null) return;
      msg.target.dispatchMessage(msg); // 分发消息
      msg.recycleUnchecked(); // 回收消息
  }
  ```

- **MessageQueue的enqueueMessage()方法如何进行线程同步的**

  使用 `synchronized(this)` 对 MessageQueue 对象加锁，保证线程安全。

- **MessageQueue的next()方法内部原理**

  1. 无限循环
  2. 调用 `nativePollOnce()` 阻塞或超时
  3. 检查消息是否到期
  4. 到期则返回消息，否则继续阻塞

- **子线程中是否可以用MainLooper去创建Handler，Looper和Handler是否一定处于一个线程**

  可以用 MainLooper 创建 Handler，此时 Handler 处理消息在主线程。Looper 和 Handler 不一定在同一线程。

- **ANR和Handler的联系**

  ANR 发生在主线程 Handler 处理消息超时：
  - 输入事件 5 秒无响应
  - BroadcastReceiver 5 秒（前台）
  - Service 几秒（生命周期方法）
  - startForegroundService 5 秒内必须调用 startForeground()

  > 参考：[Android Developers - ANR](https://developer.android.com/topic/performance/vitals/anr)

## 6.View绘制

- **View绘制流程**

  View 绘制三大步骤：
  1. **Measure**：测量 View 的宽高
  2. **Layout**：确定 View 的位置
  3. **Draw**：绘制 View 的内容

  流程：ViewRootImpl.performTraversals() → performMeasure() → performLayout() → performDraw()

- **MeasureSpec是什么**

  MeasureSpec 是一个 32 位 int 值，高 2 位为模式，低 30 位为大小：
  - `EXACTLY`：精确值（match_parent 或具体 dp）
  - `AT_MOST`：最大值（wrap_content）
  - `UNSPECIFIED`：无限制（ScrollView 中的子 View）

- **子View创建MeasureSpec创建规则是什么**

  | 父 View 模式 | 子 View 尺寸 | 子 View MeasureSpec |
  |--------------|-------------|---------------------|
  | EXACTLY | 具体值 | EXACTLY + 具体值 |
  | EXACTLY | match_parent | EXACTLY + 父剩余空间 |
  | EXACTLY | wrap_content | AT_MOST + 父剩余空间 |
  | AT_MOST | 具体值 | EXACTLY + 具体值 |
  | AT_MOST | match_parent | AT_MOST + 父剩余空间 |
  | AT_MOST | wrap_content | AT_MOST + 父剩余空间 |

- **自定义Viewwrap_content不起作用的原因**

  默认情况下，wrap_content 和 match_parent 效果相同。需要重写 `onMeasure()` 处理 AT_MOST 模式。

- **在Activity中获取某个View的宽高有几种方法**

  1. `View.post()`：在 View 绘制完成后获取
  2. `ViewTreeObserver.OnGlobalLayoutListener`：布局完成后回调
  3. `View.measure()`：手动测量（不推荐）
  4. `onWindowFocusChanged()`：窗口焦点变化时获取

- **为什么onCreate获取不到View的宽高**

  因为 View 的测量在 `onResume()` 之后的 `performTraversals()` 中执行，onCreate 时 View 尚未测量。

- **View#post与Handler#post的区别**

  - `View.post()`：在 View 附加到窗口后执行，保证 View 已初始化
  - `Handler.post()`：立即发送到消息队列

- **Android绘制和屏幕刷新机制原理**

  1. View 调用 `invalidate()` 标记脏区域
  2. ViewRootImpl 调度绘制任务
  3. 通过 Choreographer 在 VSYNC 信号到来时执行绘制
  4. 绘制内容通过 Surface 提交给 SurfaceFlinger
  5. SurfaceFlinger 合成并显示到屏幕

- **Choreography原理**

  Choreographer 是 Android 的帧调度器：
  1. 接收 VSYNC 信号
  2. 按顺序处理 INPUT、ANIMATION、TRAVERSAL 回调
  3. 保证 UI 更新与屏幕刷新同步

- **什么是双缓冲**

  双缓冲使用两个缓冲区：前台缓冲区显示内容，后台缓冲区绘制下一帧。绘制完成后交换缓冲区，避免闪烁。

- **为什么使用SurfaceView**

  SurfaceView 有独立的 Surface，可以在子线程绘制，不影响主线程，适合频繁刷新的场景（如视频播放、游戏）。

- **什么是SurfaceView**

  SurfaceView 是拥有独立 Surface 的 View，可以在子线程中进行绘制，通过 SurfaceHolder 接口管理 Surface。

- **View和SurfaceView的区别**

  | 特性 | View | SurfaceView |
  |------|------|-------------|
  | 绘制线程 | 主线程 | 子线程 |
  | Surface | 共享 | 独立 |
  | 刷新效率 | 低 | 高 |
  | 适用场景 | 静态 UI | 频繁刷新 |

- **SurfaceView为什么可以直接子线程绘制**

  因为 SurfaceView 有独立的 Surface，不通过 ViewRootImpl 的主线程绘制流程，可以直接在子线程锁定 Canvas 进行绘制。

- **SurfaceView、TextureView、SurfaceTexture、GLSurfaceView**

  - **SurfaceView**：独立 Surface，性能好但无法做动画
  - **TextureView**：支持动画和变换，但性能略差
  - **SurfaceTexture**：TextureView 的底层实现
  - **GLSurfaceView**：支持 OpenGL ES 绘制

- **getWidth()方法和getMeasureWidth()区别**

  - `getWidth()`：最终宽度，在 layout 后确定
  - `getMeasuredWidth()`：测量宽度，在 measure 后确定

- **invalidate() 和 postInvalidate() 的区别**

  - `invalidate()`：在主线程调用，触发重绘
  - `postInvalidate()`：在子线程调用，通过 Handler 切换到主线程重绘

- **Requestlayout，onlayout，onDraw，DrawChild区别与联系**

  - `requestLayout()`：请求重新测量和布局
  - `onLayout()`：确定子 View 的位置
  - `onDraw()`：绘制 View 内容
  - `drawChild()`：绘制子 View

- **LinearLayout、FrameLayout 和 RelativeLayout 哪个效率高**

  效率：FrameLayout > LinearLayout > RelativeLayout

  RelativeLayout 需要两次测量，效率最低。LinearLayout 使用 weight 也需要两次测量。

- **LinearLayout的绘制流程**

  1. measure：测量每个子 View，考虑 weight
  2. layout：按方向（水平/垂直）排列子 View
  3. draw：绘制背景、分割线、子 View

- **自定义 View 的流程和注意事项**

  流程：
  1. 继承 View 或现有控件
  2. 重写 `onMeasure()` 处理 wrap_content
  3. 重写 `onLayout()`（ViewGroup）
  4. 重写 `onDraw()` 绘制内容
  5. 处理触摸事件

  注意事项：
  - 处理 padding
  - 支持 wrap_content
  - 处理触摸冲突
  - 避免内存泄漏

- **自定义View如何考虑机型适配**

  1. 使用 dp 而非 px
  2. 使用 ConstraintLayout
  3. 提供不同分辨率的资源
  4. 使用比例布局

- **自定义控件优化方案**

  1. 减少 `invalidate()` 调用
  2. 使用 `clipRect()` 局部刷新
  3. 避免在 `onDraw()` 中创建对象
  4. 使用硬件加速

- **invalidate怎么局部刷新**

  使用 `invalidate(Rect)` 或 `invalidate(l, t, r, b)` 指定脏区域，只重绘该区域。

- **View加载流程（setContentView）**

  1. `Activity.setContentView()` → `PhoneWindow.setContentView()`
  2. 创建 DecorView
  3. 加载布局到 DecorView
  4. 在 `onResume()` 后的 `performTraversals()` 中测量、布局、绘制

## 7.View事件分发

- **View事件分发机制**

  事件分发三个核心方法：
  1. `dispatchTouchEvent()`：分发事件
  2. `onInterceptTouchEvent()`：拦截事件（仅 ViewGroup 有）
  3. `onTouchEvent()`：处理事件

  分发流程：Activity → Window → DecorView → ViewGroup → View

  伪代码：
  ```java
  public boolean dispatchTouchEvent(MotionEvent ev) {
      boolean consume = false;
      if (onInterceptTouchEvent(ev)) {
          consume = onTouchEvent(ev);
      } else {
          consume = child.dispatchTouchEvent(ev);
      }
      return consume;
  }
  ```

- **view的onTouchEvent，OnClickListerner和OnTouchListener的onTouch方法 三者优先级**

  优先级：OnTouchListener.onTouch() > onTouchEvent() > OnClickListener.onClick()

  1. OnTouchListener.onTouch() 返回 true 则不调用 onTouchEvent()
  2. onTouchEvent() 中处理点击事件，调用 OnClickListener.onClick()

- **onTouch 和onTouchEvent 的区别**

  - `onTouch()`：OnTouchListener 接口方法，优先级高
  - `onTouchEvent()`：View 自身方法，处理触摸事件

- **ACTION_CANCEL什么时候触发**

  1. 父 View 拦截事件时，子 View 收到 ACTION_CANCEL
  2. 手指移出 View 区域
  3. Activity 被销毁

- **事件是先到DecorView还是先到Window**

  事件先到 Window（PhoneWindow），再传递给 DecorView。

  流程：Activity.dispatchTouchEvent() → PhoneWindow → DecorView → ViewGroup

- **点击事件被拦截，但是想传到下面的View，如何操作**

  在父 View 的 `onInterceptTouchEvent()` 中：
  - 对 ACTION_DOWN 返回 false
  - 对后续事件返回 true

  这样 ACTION_DOWN 会传递给子 View，后续事件被父 View 拦截。

- **如何解决View的事件冲突**

  1. **内部拦截法**：子 View 调用 `requestDisallowInterceptTouchEvent()`
  2. **外部拦截法**：父 View 在 `onInterceptTouchEvent()` 中判断是否拦截

- **在 ViewGroup 中的 onTouchEvent 中消费 ACTION_DOWN 事件，ACTION_UP事件是怎么传递**

  如果 ViewGroup 消费 ACTION_DOWN，后续事件（ACTION_MOVE、ACTION_UP）都会直接传递给 ViewGroup，不再调用 `onInterceptTouchEvent()`。

- **Activity ViewGroup和View都不消费ACTION_DOWN,那么ACTION_UP事件是怎么传递的**

  如果都不消费 ACTION_DOWN，后续事件不会再传递给该 View 树，ACTION_UP 会传递给 Activity 的 `onTouchEvent()` 处理。

- **同时对父 View 和子 View 设置点击方法，优先响应哪个**

  子 View 优先响应。事件先传递给子 View，子 View 消费后不再传递给父 View。

- **requestDisallowInterceptTouchEvent的调用时机**

  子 View 调用此方法通知父 View 不要拦截事件：
  - 参数 true：父 View 不拦截
  - 参数 false：父 View 可以拦截

  典型场景：子 View 需要处理滑动事件，防止父 View 拦截。

## 8.RecyclerView

- **RecyclerView的多级缓存机制,每一级缓存具体作用是什么,分别在什么场景下会用到哪些缓存**

  RecyclerView 有四级缓存：
  1. **mAttachedScrap / mChangedScrap**：屏幕内缓存，存储当前可见的 ViewHolder
  2. **mCachedViews**：屏幕外缓存，默认大小 2，存储刚移出屏幕的 ViewHolder
  3. **ViewCacheExtension**：自定义缓存（可选）
  4. **RecycledViewPool**：缓存池，默认大小 5，存储已清理的 ViewHolder

  使用场景：
  - mAttachedScrap：列表滚动时快速复用
  - mCachedViews：来回滚动时复用
  - RecycledViewPool：不同列表间共享 ViewHolder

- **RecyclerView的滑动回收复用机制**

  1. 滑动时，移出屏幕的 View 被回收到 mCachedViews
  2. mCachedViews 满后，最早的 ViewHolder 移到 RecycledViewPool
  3. 新进入屏幕的 View 优先从 mCachedViews 获取
  4. 其次从 RecycledViewPool 获取
  5. 都没有则创建新的 ViewHolder

- **RecyclerView的刷新回收复用机制**

  1. `notifyDataSetChanged()` 会将所有 ViewHolder 移到 RecycledViewPool
  2. `notifyItemChanged()` 等局部刷新会保留 mCachedViews
  3. 局部刷新效率更高

- **RecyclerView 为什么要预布局**

  预布局用于支持动画：
  1. 在布局前保存当前状态
  2. 布局后比较新旧状态
  3. 根据差异执行动画

- **ListView 与 RecyclerView区别**

  | 特性 | ListView | RecyclerView |
  |------|----------|--------------|
  | 布局 | 只支持线性 | 线性、网格、瀑布流 |
  | 缓存 | 两级缓存 | 四级缓存 |
  | ViewHolder | 可选 | 必须使用 |
  | 动画 | 不支持 | 支持 |
  | 局部刷新 | 不支持 | 支持 |
  | 性能 | 一般 | 更好 |

- **RecyclerView性能优化**

  1. 使用 DiffUtil 进行局部刷新
  2. 避免在 onBindViewHolder 中创建对象
  3. 使用 setHasFixedSize(true) 优化布局
  4. 使用 RecycledViewPool 共享 ViewHolder
  5. 预加载（LayoutManager.setItemPrefetchEnabled）
## 9.Viewpager&Fragment

- **Fragment的生命周期 & 结合Activity的生命周期**

  Fragment 生命周期：
  onAttach() → onCreate() → onCreateView() → onActivityCreated() → onStart() → onResume() → onPause() → onStop() → onDestroyView() → onDestroy() → onDetach()

  与 Activity 的关系：
  - Activity 的 onCreate 对应 Fragment 的 onAttach → onCreate → onCreateView → onActivityCreated
  - Activity 的 onStart/onResume/onPause/onStop/onDestroy 与 Fragment 对应
  - Activity 销毁时 Fragment 也会销毁

- **Activity和Fragment的通信方式， Fragment之间如何进行通信**

  Activity 与 Fragment 通信：
  1. Bundle：通过 setArguments() 传递
  2. 接口回调：Fragment 定义接口，Activity 实现
  3. ViewModel：共享 ViewModel
  4. Fragment Result API：setFragmentResult()

  Fragment 之间通信：
  1. 共享 ViewModel
  2. Fragment Result API
  3. 通过 Activity 中转

- **getFragmentManager、getSupportFragmentManager 、getChildFragmentManager之间的区别？**

  - `getFragmentManager()`：获取系统 Fragment 管理器（已废弃）
  - `getSupportFragmentManager()`：获取 AndroidX Fragment 管理器
  - `getChildFragmentManager()`：获取子 Fragment 的管理器（嵌套 Fragment）

- **为什么使用Fragment.setArguments(Bundle)传递参数**

  因为 Fragment 会被销毁重建，使用 setArguments() 的 Bundle 会被系统保存和恢复，而构造函数参数会丢失。

- **FragmentPagerAdapter与FragmentStatePagerAdapter的区别与使用场景**

  | 特性 | FragmentPagerAdapter | FragmentStatePagerAdapter |
  |------|---------------------|--------------------------|
  | 销毁 | 只销毁视图 | 销毁整个 Fragment |
  | 内存 | 占用多 | 占用少 |
  | 适用 | 少量页面 | 大量页面 |

- **Fragment懒加载**

  懒加载在 Fragment 可见时才加载数据：
  - 旧方案：`setUserVisibleHint()`（已废弃）
  - 新方案：`setMaxLifecycle()` 或 `FragmentTransaction.setMaxLifecycle()`

  实现：在 onResume() 或 onStart() 中判断是否可见，可见时加载数据。

- **ViewPager2与ViewPager区别**

  | 特性 | ViewPager | ViewPager2 |
  |------|-----------|------------|
  | 内部实现 | 自定义 ViewGroup | RecyclerView |
  | 方向 | 只支持水平 | 支持水平和垂直 |
  | 缓存 | 自定义 | RecyclerView 缓存 |
  | Fragment | FragmentPagerAdapter | FragmentStateAdapter |
  | 局部刷新 | 不支持 | 支持 |

- **Fragment嵌套问题**

  嵌套 Fragment 注意事项：
  1. 使用 `getChildFragmentManager()` 而非 `getParentFragmentManager()`
  2. ViewPager2 + Fragment 使用 `FragmentStateAdapter`
  3. 嵌套过深会影响性能

## 10.WebView

- **如何提高WebView加载速度**

  1. **预加载 WebView**：在 Application 中初始化 WebView
  2. **缓存**：启用 WebView 缓存
  3. **并行加载**：本地资源与网络资源并行加载
  4. **优化资源**：压缩 JS/CSS，使用 WebP 图片
  5. **离线包**：将常用资源打包到本地

- **WebView与 js的交互**

  1. **Android 调用 JS**：
     - `loadUrl("javascript:jsMethod()")`
     - `evaluateJavascript()`（推荐，可获取返回值）

  2. **JS 调用 Android**：
     - `addJavascriptInterface()`（注意安全问题）
     - `shouldOverrideUrlLoading()` 拦截 URL
     - `onJsAlert()`/`onJsConfirm()` 拦截 JS 弹窗

- **WebView的漏洞**

  1. **addJavascriptInterface 漏洞**：Android 4.2 以下远程代码执行
  2. **WebView 任意文件访问**：通过 file 协议访问本地文件
  3. **WebView 域控制不严格**：白名单绕过

  解决方案：
  - 使用 `@JavascriptInterface` 注解
  - 禁用 file 协议
  - 使用白名单校验域名

- **JsBridge原理**

  JsBridge 是 JS 与 Native 通信的桥梁：
  1. **URL 拦截方案**：JS 通过 iframe 发送 URL，Native 拦截
  2. **JavaScript 注入方案**：Native 注入 JS 代码到 WebView
  3. **addJavascriptInterface 方案**：直接暴露 Java 对象给 JS

  常用库：JsBridge、DSBridge

## 11.动画

- **动画的类型**

  Android 动画分为三种：
  1. **帧动画**：逐帧播放图片
  2. **补间动画**：平移、旋转、缩放、透明度变化
  3. **属性动画**：真正改变 View 属性

- **补间动画和属性动画的区别**

  | 特性 | 补间动画 | 属性动画 |
  |------|---------|---------|
  | 实现 | 只改变绘制效果 | 真正改变属性值 |
  | 事件 | 点击事件位置不变 | 点击事件跟随变化 |
  | 范围 | View | 任意对象 |
  | 效果 | 平移、旋转、缩放、透明度 | 任意属性 |

- **ObjectAnimator，ValueAnimator及其区别**

  - `ValueAnimator`：属性值渐变，需要手动更新 UI
  - `ObjectAnimator`：继承 ValueAnimator，自动更新对象属性

  ```kotlin
  // ValueAnimator
  ValueAnimator.ofFloat(0f, 100f).apply {
      addUpdateListener { animation ->
          val value = animation.animatedValue as Float
          view.translationX = value
      }
      start()
  }

  // ObjectAnimator
  ObjectAnimator.ofFloat(view, "translationX", 0f, 100f).start()
  ```

- **TimeInterpolator插值器，自定义插值器**

  插值器控制动画的变化速率：
  - `LinearInterpolator`：匀速
  - `AccelerateInterpolator`：加速
  - `DecelerateInterpolator`：减速
  - `AccelerateDecelerateInterpolator`：先加速后减速

  自定义插值器实现 `TimeInterpolator` 接口的 `getInterpolation()` 方法。

- **TypeEvaluator估值器**

  估值器计算属性值：
  - `IntEvaluator`：整数估值器
  - `FloatEvaluator`：浮点数估值器
  - `ArgbEvaluator`：颜色估值器

  自定义估值器实现 `TypeEvaluator` 接口的 `evaluate()` 方法。

## 12.Bitmap

- **Bitmap 内存占用的计算**

  公式：宽 × 高 × 每像素字节数

  | 格式 | 每像素字节数 |
  |------|-------------|
  | ARGB_8888 | 4 字节 |
  | RGB_565 | 2 字节 |
  | ARGB_4444 | 2 字节 |
  | ALPHA_8 | 1 字节 |

  注意：实际内存还与设备密度有关。

- **getByteCount() & getAllocationByteCount()的区别**

  - `getByteCount()`：当前像素数据占用的字节数
  - `getAllocationByteCount()`：像素数据实际分配的字节数（可能更大）

  复用 Bitmap 时，getAllocationByteCount() 可能大于 getByteCount()。

- **Bitmap的压缩方式**

  1. **质量压缩**：`compress()` 方法，不改变尺寸
  2. **采样率压缩**：`inSampleSize`，降低分辨率
  3. **缩放压缩**：`createScaledBitmap()`，缩放图片

- **LruCache & DiskLruCache原理**

  - **LruCache**：内存缓存，基于 LinkedHashMap，最近最少使用淘汰
  - **DiskLruCache**：磁盘缓存，基于文件系统

  LruCache 核心：`removeEldestEntry()` 判断是否移除最老条目。

- **如何设计一个图片加载库**

  核心功能：
  1. **三级缓存**：内存 → 磁盘 → 网络
  2. **异步加载**：线程池管理
  3. **图片解码**：采样率、格式选择
  4. **生命周期管理**：绑定 Activity/Fragment 生命周期
  5. **图片变换**：圆角、裁剪等

  参考 Glide、Coil 等库的设计。

- **有一张非常大的图片,如何去加载这张大图片**

  使用 `BitmapRegionDecoder` 局部加载：
  1. 获取图片尺寸
  2. 计算采样率，加载缩略图
  3. 根据需要加载局部区域

- **如果把drawable-xxhdpi下的图片移动到drawable-xhdpi下，图片内存是如何变的。**

  图片内存会变大。因为 xhdpi 密度低于 xxhdpi，系统会放大图片以匹配设备密度。

  计算：新内存 = 原内存 × (目标密度 / 原密度)²

- **如果在hdpi、xxhdpi下放置了图片，加载的优先级。如果是400_800，1080_1920，加载的优先级。**

  加载优先级：
  1. 先匹配设备密度对应的目录
  2. 没有则向上查找更高密度
  3. 再没有则向下查找更低密度

  对于 1080×1920 设备（xxhdpi）：
  - 优先加载 xxhdpi 目录的图片
  - 没有则加载 xxxhdpi 或 xhdpi 目录的图片

## 13.mvc&mvp&mvvm

- **MVC及其优缺点**

  MVC（Model-View-Controller）：
  - Model：数据模型
  - View：视图（XML 布局）
  - Controller：控制器（Activity/Fragment）

  优点：简单直观
  缺点：Activity 职责过重，耦合严重

- **MVP及其优缺点**

  MVP（Model-View-Presenter）：
  - Model：数据模型
  - View：视图（Activity/Fragment）
  - Presenter：中间人，处理业务逻辑

  优点：解耦，便于测试
  缺点：接口过多，Presenter 生命周期管理复杂

- **MVVM及其优缺点**

  MVVM（Model-View-ViewModel）：
  - Model：数据模型
  - View：视图
  - ViewModel：持有 UI 数据，生命周期感知

  优点：数据驱动，自动更新 UI，生命周期安全
  缺点：数据绑定调试困难

- **MVP如何管理Presenter的生命周期，何时取消网络请求**

  1. 在 View（Activity/Fragment）的 onDestroy() 中调用 Presenter 的 onDestroy()
  2. Presenter 的 onDestroy() 中取消网络请求、清理资源
  3. 使用 CompositeDisposable 管理 RxJava 订阅
  4. 使用 Lifecycle-aware 的 Presenter

## 14.Binder

- **Android中进程和线程的关系,区别**

  - 进程：独立的内存空间，独立的资源
  - 线程：共享进程的内存和资源
  - 区别：进程间通信需要 IPC，线程间通信可直接访问共享变量

- **为何需要进行IPC,多进程通信可能会出现什么问题**

  需要 IPC 的原因：
  1. 不同应用间数据共享
  2. 多进程架构提高稳定性

  多进程问题：
  1. 静态变量不共享
  2. 文件同步问题
  3. SharedPreferences 不可靠
  4. 线程同步机制失效

- **Android中IPC方式有几种、各种方式优缺点**

  | 方式 | 优点 | 缺点 |
  |------|------|------|
  | Intent/Bundle | 简单 | 数据量有限 |
  | 文件共享 | 简单 | 并发问题 |
  | Messenger | 轻量 | 只能串行 |
  | AIDL | 功能强大 | 复杂 |
  | ContentProvider | 标准化 | 只适合数据共享 |
  | Socket | 灵活 | 需要网络权限 |

- **为何新增Binder来作为主要的IPC方式**

  1. 性能好：只需一次数据拷贝
  2. 安全：支持权限校验
  3. 使用方便：面向对象接口

- **什么是Binder**

  Binder 是 Android 的跨进程通信机制：
  - 本质：驱动程序（/dev/binder）
  - 作用：不同进程间的数据传输

- **Binder的原理**

  1. Client 通过 Binder 代理（Proxy）发送请求
  2. 请求通过 Binder 驱动传递到 Server
  3. Server 通过 Binder 实体（Stub）处理请求
  4. 结果通过 Binder 驱动返回给 Client

- **Binder Driver 如何在内核空间中做到一次拷贝的？**

  使用 `mmap()` 内存映射：
  1. 接收方在内核空间创建一块映射内存
  2. 发送方的数据直接拷贝到这块内存
  3. 接收方可以直接访问，无需第二次拷贝

- **使用Binder进行数据传输的具体过程**

  1. 发送方将数据序列化到 Parcel
  2. 通过 Binder 驱动调用 `transact()`
  3. 驱动将数据拷贝到接收方的映射内存
  4. 接收方的 `onTransact()` 被调用
  5. 接收方从 Parcel 反序列化数据

- **Binder框架中ServiceManager的作用**

  ServiceManager 是 Binder 的核心组件：
  1. 注册服务：Server 注册服务到 ServiceManager
  2. 查询服务：Client 通过 ServiceManager 获取服务
  3. 管理服务：维护服务名与 Binder 引用的映射

- **什么是AIDL**

  AIDL（Android Interface Definition Language）是 Android 的接口定义语言，用于定义跨进程通信的接口。

- **AIDL使用的步骤**

  1. 创建 .aidl 文件定义接口
  2. 编译生成 Stub 和 Proxy 类
  3. Server 实现 Stub 接口
  4. Client 通过 Proxy 调用

- **AIDL支持哪些数据类型**

  1. 基本数据类型
  2. String、CharSequence
  3. List、Map
  4. Parcelable 对象
  5. AIDL 接口

- **AIDL的关键类，方法和工作流程**

  - `IInterface`：接口基类
  - `Stub`：服务端抽象类
  - `Proxy`：客户端代理类
  - `asInterface()`：将 IBinder 转换为接口
  - `onTransact()`：处理客户端请求

- **如何优化多模块都使用AIDL的情况**

  1. 合并 AIDL 接口
  2. 使用 ContentProvider 包装
  3. 使用 Messenger 简化通信

- **使用 Binder 传输数据的最大限制是多少，被占满后会导致什么问题**

  限制：约 1MB（所有事务共享）

  被占满后：
  1. `TransactionTooLargeException` 异常
  2. 其他事务无法执行

- **Binder 驱动加载过程中有哪些重要的步骤**

  1. 打开 `/dev/binder` 设备
  2. `mmap()` 映射内存
  3. 设置进程上下文
  4. 初始化 Binder 线程池

- **系统服务与bindService启动的服务的区别**

  | 特性 | 系统服务 | bindService 服务 |
  |------|---------|-----------------|
  | 进程 | system_server | 应用进程 |
  | 启动 | 系统启动时 | 应用启动时 |
  | 生命周期 | 系统级 | 应用级 |

- **Activity的bindService流程**

  1. Activity 调用 `bindService()`
  2. AMS 查找或创建 Service
  3. Service 的 `onBind()` 返回 IBinder
  4. 通过 `ServiceConnection.onServiceConnected()` 传递给 Activity

- **不通过AIDL，手动编码来实现Binder的通信**

  1. 定义接口继承 IInterface
  2. 实现 Stub 抽象类
  3. 实现 Proxy 代理类
  4. 在 `onTransact()` 中处理请求

## 15.内存泄漏&内存溢出

- **什么是OOM & 什么是内存泄漏以及原因**

  OOM（Out of Memory）：内存溢出，申请的内存超过系统可用内存。

  内存泄漏：程序不再使用的对象无法被 GC 回收。

  原因：
  1. 静态变量持有 Activity 引用
  2. 非静态内部类持有外部类引用
  3. 未关闭的资源（Cursor、Stream）
  4. 未取消的监听器

- **Thread是如何造成内存泄露的，如何解决？**

  原因：非静态内部类 Thread 持有外部类引用，Thread 生命周期比 Activity 长。

  解决方案：
  1. 使用静态内部类
  2. 使用 WeakReference 持有外部类
  3. 在 onDestroy() 中终止线程

- **Handler导致的内存泄露的原因以及如何解决**

  原因：非静态内部类 Handler 持有外部类引用，Message 持有 Handler 引用。

  解决方案：
  1. 使用静态内部类 + WeakReference
  2. 在 onDestroy() 中调用 `removeCallbacksAndMessages(null)`

- **如何加载Bitmap防止内存溢出**

  1. 使用 `inSampleSize` 采样压缩
  2. 使用 `inBitmap` 复用内存
  3. 选择合适的像素格式（RGB_565）
  4. 使用图片加载库（Glide、Coil）

- **MVP中如何处理Presenter层以防止内存泄漏的**

  1. Presenter 使用 WeakReference 持有 View
  2. 在 View 销毁时调用 Presenter 的 onDestroy()
  3. 使用 Lifecycle-aware 的 Presenter

## 16.性能优化

- **内存优化**

  1. 避免内存泄漏（静态引用、内部类）
  2. 使用 WeakReference、SoftReference
  3. 及时释放资源（Cursor、Stream）
  4. 使用 Profiler 分析内存
  5. 优化 Bitmap 内存

- **启动优化**

  1. 减少 Application.onCreate() 工作
  2. 延迟初始化
  3. 使用 SplashScreen
  4. 多线程初始化
  5. 使用 App Startup 库

- **布局加载和绘制优化**

  1. 减少布局层级
  2. 使用 ConstraintLayout
  3. 使用 ViewStub 延迟加载
  4. 使用 merge 标签
  5. 避免过度绘制

- **卡顿优化**

  1. 避免主线程耗时操作
  2. 使用 StrictMode 检测
  3. 使用 Profiler 分析
  4. 优化 RecyclerView
  5. 使用 Choreographer 监控帧率

- **网络优化**

  1. 使用 HTTP/2
  2. 启用 GZIP 压缩
  3. 使用缓存
  4. 减少请求次数
  5. 使用 CDN

## 17.Window&WindowManager

- **什么是Window**

  Window 是 Android 中窗口的抽象，是 View 的容器。每个 Activity 对应一个 Window（PhoneWindow）。

- **什么是WindowManager**

  WindowManager 是管理 Window 的系统服务，负责 Window 的添加、删除、更新。

- **什么是ViewRootImpl**

  ViewRootImpl 是 View 树的根节点，负责：
  1. View 的测量、布局、绘制
  2. 事件分发
  3. 与 WindowManager 通信

- **什么是DecorView**

  DecorView 是 Activity 的根 View，包含标题栏和内容区域。通过 `setContentView()` 设置的内容添加到 DecorView 中。

- **Activity，View，Window三者之间的关系**

  - Activity 包含 Window（PhoneWindow）
  - Window 包含 DecorView
  - DecorView 包含 View 树

- **DecorView什么时候被WindowManager添加到Window中**

  在 `ActivityThread.handleResumeActivity()` 中，调用 `WindowManager.addView()` 将 DecorView 添加到 Window。

## 18.WMS

- **什么是WMS**

  WMS（WindowManagerService）是 Android 系统的核心服务，负责管理所有窗口的创建、销毁、布局和显示。

- **WMS是如何管理Window的**

  1. 管理 Window 的添加、删除、更新
  2. 计算 Window 的布局
  3. 管理 Window 的 Z 序
  4. 处理 Window 的动画

- **IWindowSession是什么，WindowSession的创建过程是怎样的**

  IWindowSession 是应用与 WMS 通信的 Binder 接口：
  1. 应用通过 WindowManager 获取 IWindowSession
  2. 每个应用进程有一个 Session
  3. 通过 Session 执行 Window 操作

- **WindowState是什么**

  WindowState 是 WMS 中 Window 的表示：
  - 包含 Window 的属性、位置、大小
  - 管理 Window 的状态

- **Android窗口大概分为几种？分组原理是什么**

  窗口类型：
  1. 应用窗口（1-99）
  2. 子窗口（1000-1999）
  3. 系统窗口（2000-2999）

  分组原理：按类型和 Z 序排列，系统窗口在最上层。

- **Dialog的Context只能是Activity的Context，不能是Application的Context**

  因为 Dialog 需要 Activity 的 Token，Application 没有 Token。

- **App应用程序如何与SurfaceFlinger通信的**

  通过 Binder 和共享内存：
  1. App 通过 SurfaceControl 与 SurfaceFlinger 通信
  2. 使用 BufferQueue 传递图形缓冲区

- **View 的绘制是如何把数据传递给 SurfaceFlinger 的**

  1. View 绘制到 Canvas
  2. Canvas 通过 Surface 锁定缓冲区
  3. 缓冲区通过 BufferQueue 传递给 SurfaceFlinger
  4. SurfaceFlinger 合成并显示

- **共享内存的具体实现是什么**

  使用 `ashmem`（Anonymous Shared Memory）：
  1. 创建共享内存区域
  2. 映射到进程地址空间
  3. 通过 Binder 传递文件描述符

- **relayout是如何向SurfaceFlinger申请Surface**

  1. 调用 `SurfaceControl.nativeCreate()`
  2. 通过 Binder 调用 SurfaceFlinger
  3. SurfaceFlinger 创建 Surface
  4. 返回 Surface 给应用

- **什么是Surface**

  Surface 是图形缓冲区的容器：
  - 包含一个或多个 BufferQueue
  - 用于接收绘制数据
  - 传递给 SurfaceFlinger 合成显示

## 19.AMS

- **ActivityManagerService是什么？什么时候初始化的？有什么作用？**

  AMS 是 Android 系统的核心服务，在 SystemServer 中初始化。作用：
  1. 管理 Activity、Service、BroadcastReceiver 的生命周期
  2. 管理进程的创建和销毁
  3. 管理任务栈

- **ActivityThread是什么?ApplicationThread是什么?他们的区别**

  - ActivityThread：应用的主线程，负责 Activity 的生命周期
  - ApplicationThread：ActivityThread 的内部类，是 AMS 与应用通信的 Binder 接口

  区别：ActivityThread 是主线程，ApplicationThread 是 Binder 服务端。

- **Instrumentation是什么？和ActivityThread是什么关系？**

  Instrumentation 负责监控应用与系统的交互：
  - 创建 Activity、Application
  - 调用生命周期方法
  - 用于测试框架

  关系：ActivityThread 通过 Instrumentation 创建和管理组件。

- **ActivityManagerService和zygote进程通信是如何实现的**

  通过 Socket 通信：
  1. AMS 通过 Socket 发送进程创建请求
  2. Zygote 接收请求并 fork 子进程
  3. 子进程执行 ActivityThread.main()

- **ActivityRecord、TaskRecord、ActivityStack，ActivityStackSupervisor，ProcessRecord**

  - ActivityRecord：Activity 的记录
  - TaskRecord：任务栈的记录
  - ActivityStack：Activity 栈
  - ActivityStackSupervisor：管理所有 ActivityStack
  - ProcessRecord：进程记录

- **ActivityManager、ActivityManagerService、ActivityManagerNative、ActivityManagerProxy的关系**

  - ActivityManager：客户端 API
  - ActivityManagerService：服务端实现
  - ActivityManagerNative：Binder 基类
  - ActivityManagerProxy：客户端代理

- **手写实现简化版AMS**

  简化版 AMS 需要实现：
  1. Activity 注册和查找
  2. 任务栈管理
  3. 生命周期调度
  4. 进程间通信

## 20.系统启动

- **android系统启动流程**

  1. Boot ROM → Boot Loader
  2. Linux 内核启动
  3. init 进程启动
  4. Zygote 进程启动
  5. SystemServer 启动
  6. Launcher 启动

- **SystemServer，ServiceManager，SystemServiceManager的关系**

  - SystemServer：系统服务进程，启动各种系统服务
  - ServiceManager：管理 Binder 服务的注册和查找
  - SystemSystemServiceManager：管理系统服务的生命周期

- **孵化应用进程这种事为什么不交给SystemServer来做，而专门设计一个Zygote**

  1. Zygote 预加载资源，加速应用启动
  2. Zygote 使用 fork()，共享内存
  3. 隔离系统服务和应用进程

- **Zygote的IPC通信机制为什么使用socket而不采用binder**

  1. Zygote 启动时 Binder 还未初始化
  2. Socket 更简单可靠
  3. 避免 Binder 的复杂性

## 21.App启动&打包&安装

- **应用启动流程**

  1. 点击图标 → Launcher 调用 startActivity()
  2. AMS 查找进程，不存在则通过 Zygote 创建
  3. ActivityThread.main() 执行
  4. 创建 Application、Activity
  5. 执行 onCreate() → onStart() → onResume()

- **apk组成和Android的打包流程**

  APK 组成：
  - AndroidManifest.xml
  - classes.dex
  - res/ 资源文件
  - lib/ so 库
  - META-INF/ 签名

  打包流程：
  1. aapt 编译资源
  2. aidl 编译接口
  3. javac 编译代码
  4. dx 转换 dex
  5. apkbuilder 打包
  6. 签名对齐

- **Android的签名机制，签名如何实现的,v2相比于v1签名机制的改变**

  V1 签名：
  - 对每个文件单独签名
  - 不保护 APK 元数据

  V2 签名：
  - 对整个 APK 签名
  - 安全性更高
  - 验证更快

- **APK的安装流程**

  1. 复制 APK 到 /data/app/
  2. 解析 AndroidManifest.xml
  3. 优化 dex（dex2oat）
  4. 注册组件
  5. 发送安装完成广播

## 22.序列化

- **什么是序列化**

  序列化是将对象转换为字节流的过程，反序列化是将字节流转换为对象。

- **为什么需要使用序列化和反序列化**

  1. 网络传输
  2. 持久化存储
  3. 进程间通信

- **序列化的有哪些好处**

  1. 数据持久化
  2. 数据传输
  3. 深拷贝

- **Serializable 和 Parcelable 的区别**

  | 特性 | Serializable | Parcelable |
  |------|-------------|------------|
  | 实现 | 标记接口 | 实现接口 |
  | 性能 | 慢（反射） | 快 |
  | 用途 | 持久化 | IPC |
  | 开销 | 大 | 小 |

- **什么是serialVersionUID**

  serialVersionUID 是序列化版本号，用于验证序列化和反序列化的类是否兼容。

- **为什么还要显示指定serialVersionUID的值?**

  1. 避免自动生成的版本号不一致
  2. 保证向后兼容
  3. 控制反序列化行为

## 23.模块化&组件化

- **什么是模块化**

  模块化是将应用拆分为独立的功能模块，每个模块可以独立开发、测试、编译。

- **什么是组件化**

  组件化是将应用拆分为独立的业务组件，每个组件可以独立运行。

- **组件化优点和方案**

  优点：
  1. 并行开发
  2. 独立测试
  3. 代码复用
  4. 按需加载

  方案：
  1. 路由框架（ARouter）
  2. 接口下沉
  3. 事件总线

- **组件独立调试**

  1. 配置独立的 Application
  2. 配置独立的 AndroidManifest.xml
  3. 配置独立的 Gradle 构建

- **组件间通信**

  1. 路由框架（ARouter）
  2. 接口暴露
  3. 事件总线（EventBus）
  4. 广播

- **Application动态加载**

  1. 各组件提供初始化接口
  2. 主 Application 反射调用
  3. 使用 ARouter 自动注册

- **ARouter原理**

  ARouter 是阿里开源的路由框架：
  1. 编译时扫描注解，生成路由表
  2. 运行时通过路由表查找目标
  3. 通过 Intent 跳转

  核心：APT 生成路由表，运行时加载。

## 24.热修复&插件化

- **插件化的定义**

  插件化是将应用拆分为宿主和插件，插件可以动态加载和卸载。

- **插件化的优势**

  1. 减少 APK 体积
  2. 按需下载
  3. 热更新
  4. 模块解耦

- **插件化框架对比**

  | 框架 | 特点 |
  |------|------|
  | DynamicLoadApk | 早期框架 |
  | DroidPlugin | 功能强大 |
  | VirtualApp | 虚拟化方案 |
  | RePlugin | 360 开源 |

- **插件化流程**

  1. 下载插件 APK
  2. 加载插件类
  3. 加载插件资源
  4. 启动插件 Activity

- **插件化类加载原理**

  1. 创建 DexClassLoader 加载插件 dex
  2. 将插件 ClassLoader 添加到 PathClassLoader
  3. 通过双亲委派机制加载类

- **插件化资源加载原理**

  1. 创建 AssetManager 加载插件资源
  2. 创建 Resources 包装 AssetManager
  3. 合并宿主和插件资源

- **插件化Activity加载原理**

  1. 预注册占位 Activity
  2. 启动占位 Activity
  3. 在占位 Activity 中加载真正的 Activity
  4. 替换生命周期回调

- **热修复和插件化区别**

  | 特性 | 热修复 | 插件化 |
  |------|-------|-------|
  | 目的 | 修复 Bug | 功能扩展 |
  | 范围 | 代码修复 | 资源、so、代码 |
  | 触发 | 紧急修复 | 按需加载 |

- **热修复原理**

  1. **类加载方案**：将修复的类放在 dex 前面，优先加载
  2. **底层替换方案**：替换 ArtMethod
  3. **Instant Run 方案**：参考 Instant Run 机制

## 25.AOP

- **AOP是什么**

  AOP（Aspect-Oriented Programming，面向切面编程）是一种编程思想，用于处理横切关注点（如日志、权限、埋点）。AOP 通过将通用逻辑从业务代码中抽离，实现代码解耦。

  核心概念：
  - **Aspect（切面）**：横切关注点的模块化
  - **Pointcut（切入点）**：匹配连接点的表达式
  - **Advice（通知）**：在切入点执行的代码（Before、After、Around）

- **AOP的优点**

  1. 代码解耦：将通用逻辑从业务代码中分离
  2. 关注点分离：业务逻辑与横切逻辑分离
  3. 代码复用：通用逻辑可复用
  4. 易于维护：修改通用逻辑不影响业务代码

- **AOP的实现方式,APT,AspectJ,ASM,epic,hook**

  | 方式 | 特点 | 实现原理 |
  |------|------|----------|
  | APT | 编译时生成代码 | 注解处理器在编译时生成 Java 文件 |
  | AspectJ | 编译时/加载时织入 | 通过字节码织入（基于 BCEL），支持 source weaving 和 bytecode weaving |
  | ASM | 字节码操作 | 直接操作 .class 文件的字节码 |
  | Epic | 运行时 hook | 基于 ART 方法替换 |
  | Xposed | 系统级 hook | 修改系统 Zygote 进程，注入 hook 逻辑 |

  > 参考：[AspectJ - Wikipedia](https://en.wikipedia.org/wiki/AspectJ)

## 26.Jetpack

- **Navigation**

  Navigation 是 Jetpack 的导航组件：
  - 管理 Fragment 切换
  - 支持深链接
  - 支持动画过渡
  - 支持参数传递

- **DataBinding**

  DataBinding 是数据绑定库：
  - 将数据绑定到 XML 布局
  - 减少样板代码
  - 支持双向绑定
  - 支持表达式

- **Viewmodel**

  ViewModel 是管理 UI 数据的组件：
  - 生命周期感知
  - 配置变更时保留数据
  - 与 LiveData 配合使用

- **livedata**

  LiveData 是可观察的数据持有者：
  - 生命周期感知
  - 自动取消订阅
  - 避免内存泄漏

- **liferecycle**

  Lifecycle 是生命周期管理组件：
  - 感知 Activity/Fragment 生命周期
  - 自动管理资源
  - 与 LiveData、ViewModel 配合

## 27.开源框架

- **Okhttp源码流程,线程池**

  流程：
  1. 构建 Request
  2. 通过 Dispatcher 调度请求
  3. 通过拦截器链处理请求
  4. 通过连接池获取连接
  5. 发送请求并接收响应

  线程池：
  - 核心线程数：0
  - 最大线程数：64
  - 空闲存活时间：60 秒

- **Okhttp拦截器,addInterceptor 和 addNetworkdInterceptor区别**

  - `addInterceptor`：应用拦截器，最先执行
  - `addNetworkInterceptor`：网络拦截器，在连接前执行

  区别：应用拦截器可以修改请求，网络拦截器只能观察。

- **Okhttp责任链模式**

  Okhttp 使用责任链模式处理请求：
  1. RetryAndFollowUpInterceptor：重试
  2. BridgeInterceptor：添加请求头
  3. CacheInterceptor：缓存
  4. ConnectInterceptor：连接
  5. CallServerInterceptor：发送请求

- **Okhttp缓存怎么处理**

  1. CacheInterceptor 处理缓存
  2. 根据 Cache-Control 决定是否使用缓存
  3. 缓存策略：网络优先、缓存优先

- **Okhttp连接池和socket复用**

  连接池管理 TCP 连接：
  1. 复用相同 host 的连接
  2. 空闲连接保活
  3. 清理过期连接

- **Glide怎么绑定生命周期**

  1. 通过 RequestManager 感知生命周期
  2. Activity/Fragment 销毁时自动取消请求
  3. 使用 Lifecycle 组件

- **Glide缓存机制,内存缓存，磁盘缓存**

  内存缓存：
  - ActiveResources：正在使用的资源
  - LruResourceCache：最近使用的资源

  磁盘缓存：
  - Data：原始数据
  - Resource：解码后的资源
  - Transformed：变换后的资源

- **Glide与Picasso的区别**

  | 特性 | Glide | Picasso |
  |------|-------|---------|
  | 生命周期 | 支持 | 不支持 |
  | 缓存 | 更智能 | 简单 |
  | GIF | 支持 | 不支持 |
  | 体积 | 较大 | 较小 |

- **LruCache原理**

  LruCache 基于 LinkedHashMap：
  1. 访问顺序排序
  2. 超过容量移除最老条目
  3. `removeEldestEntry()` 控制淘汰

- **Retrofit源码流程,动态代理**

  流程：
  1. 定义接口
  2. Retrofit.create() 使用动态代理
  3. 解析注解生成 ServiceMethod
  4. 通过 OkHttp 发送请求

  动态代理：`Proxy.newProxyInstance()` 创建接口代理。

- **LeakCanary弱引用,源码流程**

  原理：
  1. 监控 Activity/Fragment 销毁
  2. 使用 WeakReference 持有对象
  3. 检测弱引用是否被回收
  4. 未回收则 dump 内存分析

- **Eventbus**

  EventBus 是事件总线：
  1. 注册订阅者
  2. 发送事件
  3. 接收事件
  4. 支持线程切换

- **Rxjava**

  RxJava 是响应式编程库：
  1. Observable：被观察者
  2. Observer：观察者
  3. Scheduler：线程调度
  4. 操作符：数据变换

# 2023最新Android中高级面试题汇总+解析

## Java面试相关

## 1.HashMap

- **HashMap原理**

  HashMap 基于数组 + 链表 + 红黑树（JDK 1.8）实现：
  - 数组：存储元素，每个位置称为"桶"
  - 链表：解决 hash 冲突
  - 红黑树：链表长度超过 8 时转换，提高查找效率

- **HashMap 有用过吗？您能给我说说他的主要用途吗？**

  HashMap 用于存储键值对，主要用途：
  1. 快速查找（O(1) 时间复杂度）
  2. 缓存实现
  3. 统计计数
  4. 配置管理

- **您能说说 HashMap 常用操作的底层实现原理吗？如存储 put(K key, V value)，查找 get(Object key)，删除 remove(Object key)，修改 replace(K key, V value)等操作**

  - `put()`：计算 hash → 找到桶位置 → 链表/红黑树插入 → 扩容判断
  - `get()`：计算 hash → 找到桶位置 → 链表/红黑树查找
  - `remove()`：计算 hash → 找到桶位置 → 链表/红黑树删除
  - `replace()`：先查找再替换

- **hash 冲突（或者叫 hash 碰撞）是什么？为什么会出现这种现象，如何解 决 hash 冲突？**

  hash 冲突：不同的 key 计算出相同的 hash 值。

  原因：hash 函数的输出空间有限。

  解决方案：
  1. 链地址法（HashMap 使用）
  2. 开放地址法
  3. 再 hash 法

- **HashMap 的容量为什么一定要是 2 的 n 次方？**

  为了 hash 值均匀分布：
  1. `(n - 1) & hash` 等价于 `hash % n`
  2. 位运算比取模快
  3. 保证 hash 值均匀分布

- **您能说说 HashMap 和 HashTable 的区别吗？**

  | 特性 | HashMap | HashTable |
  |------|---------|-----------|
  | 线程安全 | 不安全 | 安全（synchronized） |
  | null 键值 | 允许 | 不允许 |
  | 性能 | 高 | 低 |
  | 继承 | AbstractMap | Dictionary |

- **HashMap中put()如何实现的**

  1. 计算 key 的 hash 值
  2. `(n - 1) & hash` 计算桶位置
  3. 桶为空直接插入
  4. 桶不为空：链表或红黑树插入
  5. 判断是否需要扩容

- **HashMap中get()如何实现的**

  1. 计算 key 的 hash 值
  2. `(n - 1) & hash` 计算桶位置
  3. 遍历链表或红黑树查找

- **为什么HashMap线程不安全**

  1. 多线程 put 可能导致数据覆盖
  2. 扩容时可能导致死循环（JDK 1.7）
  3. size 不准确

- **HashMap1.7和1.8有哪些区别**

  | 特性 | JDK 1.7 | JDK 1.8 |
  |------|---------|---------|
  | 数据结构 | 数组 + 链表 | 数组 + 链表 + 红黑树 |
  | 插入方式 | 头插法 | 尾插法 |
  | 扩容 | 先扩容再插入 | 先插入再扩容 |
  | hash 计算 | 4 次位运算 | 1 次位运算 + 1 次异或 |

- **解决hash冲突的时候，为什么用红黑树**

  红黑树查找时间复杂度为 O(log n)，链表为 O(n)。链表过长时性能下降。

- **红黑树的效率高，为什么一开始不用红黑树存储**

  1. 红黑树维护成本高（旋转、变色）
  2. 链表长度短时效率差不多
  3. 空间占用更大

- **不用红黑树，用二叉查找树可以不**

  二叉查找树可能退化为链表（O(n)），红黑树保证 O(log n)。

- **为什么阀值是8才转为红黑树**

  泊松分布：链表长度为 8 的概率约为 0.00000006，非常小。

- **为什么退化为链表的阈值是6**

  避免频繁转换（7 附近震荡）。

- **hash冲突有哪些解决办法**

  1. 链地址法
  2. 开放地址法
  3. 再 hash 法
  4. 公共溢出区

- **HashMap在什么条件下扩容**

  1. 元素数量超过 `容量 × 负载因子`（默认 0.75）
  2. 链表长度超过 8 且容量小于 64

- **HashMap中hash函数怎么实现的，还有哪些hash函数的实现方式**

  JDK 1.8：`(h = key.hashCode()) ^ (h >>> 16)`

  其他 hash 函数：
  - 除留余数法
  - 平方取中法
  - 折叠法

- **为什么不直接将hashcode作为哈希值去做取模,而是要先高16位异或低16位**

  让高位也参与运算，减少 hash 冲突。

- **为什么扩容是2的次幂**

  保证 `(n - 1) & hash` 等价于 `hash % n`。

- **链表的查找的时间复杂度是多少**

  O(n)

- **红黑树**

  红黑树是一种自平衡二叉查找树：
  1. 节点是红色或黑色
  2. 根节点是黑色
  3. 叶子节点是黑色
  4. 红色节点的子节点是黑色
  5. 从根到叶子的黑色节点数相同

## 2.ArrayList

- **ArrayList定义**

  ArrayList 是基于动态数组实现的 List 接口，支持随机访问。

- **ArrayList 的构造器**

  1. `ArrayList()`：默认容量 10
  2. `ArrayList(int initialCapacity)`：指定初始容量
  3. `ArrayList(Collection<? extends E> c)`：从集合创建

- **add 方法源码分析**

  1. 检查是否需要扩容
  2. 如果需要，扩容为原来的 1.5 倍
  3. 将元素添加到数组末尾

- **get 方法源码分析**

  1. 检查索引是否越界
  2. 返回数组对应位置的元素

- **set 方法源码分析**

  1. 检查索引是否越界
  2. 替换数组对应位置的元素
  3. 返回旧值

- **ArrayList和LinkedList的区别，以及应用场景**

  | 特性 | ArrayList | LinkedList |
  |------|-----------|------------|
  | 数据结构 | 动态数组 | 双向链表 |
  | 随机访问 | O(1) | O(n) |
  | 插入删除 | O(n) | O(1) |
  | 内存 | 连续 | 分散 |

  应用场景：
  - ArrayList：频繁随机访问
  - LinkedList：频繁插入删除

## 3.LinkedList

- **LinkedList 定义**

  LinkedList 是基于双向链表实现的 List 接口，支持高效的插入删除。

- **LinkedList 支持的操作**

  1. 增：add、addFirst、addLast
  2. 删：remove、removeFirst、removeLast
  3. 查：get、getFirst、getLast
  4. 改：set

- **Node 类**

  ```java
  private static class Node<E> {
      E item;
      Node<E> next;
      Node<E> prev;
  }
  ```

- **addFirst 源码分析**

  1. 创建新节点
  2. 新节点的 next 指向原头节点
  3. 原头节点的 prev 指向新节点
  4. 更新头节点为新节点

- **getFirst 方法源码分析**

  1. 检查链表是否为空
  2. 返回头节点的值

- **removeFirst 方法源码分析**

  1. 检查链表是否为空
  2. 保存头节点的值
  3. 更新头节点为下一个节点
  4. 清除原头节点的引用

- **add(int index, E e)方法源码分析**

  1. 检查索引是否越界
  2. 如果 index 在前半部分，从头遍历
  3. 如果 index 在后半部分，从尾遍历
  4. 在指定位置插入新节点
## 4.HashSet 源码

- **属性**

  HashSet 内部使用 HashMap 实现：
  ```java
  private transient HashMap<E,Object> map;
  private static final Object PRESENT = new Object();
  ```

- **构造方法**

  1. `HashSet()`：默认容量 16，负载因子 0.75
  2. `HashSet(int initialCapacity)`：指定初始容量
  3. `HashSet(int initialCapacity, float loadFactor)`：指定容量和负载因子

- **添加元素**

  `add()` 方法：调用 `map.put(e, PRESENT)`，如果 key 已存在返回 false。

- **删除元素**

  `remove()` 方法：调用 `map.remove(o)`。

- **查询元素**

  `contains()` 方法：调用 `map.containsKey(o)`。

- **遍历元素**

  1. 迭代器：`iterator()`
  2. 增强 for 循环
  3. forEach 方法

- **全部**

  HashSet 的所有操作都委托给 HashMap，时间复杂度为 O(1)。
## 5.内存模型

- **内存模型产生背景**

  1. CPU 和内存速度差异大
  2. 缓存一致性问题
  3. 指令重排序优化

- **物理机的并发问题**

  1. 缓存一致性：多核 CPU 缓存不一致
  2. 指令重排序：编译器和处理器优化导致执行顺序变化

- **Java 内存模型的组成分析**

  1. 主内存：共享变量存储位置
  2. 工作内存：线程私有，存储共享变量的副本
  3. 线程对变量的操作必须在工作内存中进行

- **Java 内存间的交互操作**

  1. lock：锁定主内存变量
  2. unlock：解锁主内存变量
  3. read：从主内存读取
  4. load：加载到工作内存
  5. use：使用变量
  6. assign：赋值
  7. store：存储到主内存
  8. write：写入主内存

- **Java 内存模型运行规则**

  1. 不允许 read 和 load、store 和 write 单独出现
  2. 不允许线程丢弃最近的 assign
  3. 不允许线程无原因地（无 assign）同步数据
  4. 变量必须在主内存中产生
  5. 一个变量同一时刻只允许一个线程 lock
  6. 必须 unlock 后才能再次 lock
## 6.垃圾回收算法（JVM）

- **JVM的内存模型,每个里面都保存的什么**

  1. **堆**：对象实例、数组
  2. **方法区**：类信息、常量、静态变量
  3. **虚拟机栈**：局部变量、操作数栈、方法出口
  4. **本地方法栈**：Native 方法
  5. **程序计数器**：当前线程执行的字节码行号

- **类加载机制的几个阶段加载、验证、准备、解析、初始化、使用、卸载**

  1. **加载**：查找并加载类的二进制数据
  2. **验证**：确保类的正确性
  3. **准备**：为类变量分配内存并设置默认值
  4. **解析**：将符号引用转换为直接引用
  5. **初始化**：执行类构造器方法
  6. **使用**：使用类
  7. **卸载**：类被 GC 回收

- **对象实例化时的顺序**

  1. 分配内存
  2. 初始化零值
  3. 设置对象头
  4. 执行构造方法

- **类加载器,双亲委派及其优势**

  类加载器：
  1. 启动类加载器（Bootstrap ClassLoader）
  2. 扩展类加载器（Extension ClassLoader）
  3. 应用类加载器（Application ClassLoader）

  双亲委派：先委托父加载器加载，父加载器无法加载时才自己加载。

  优势：
  1. 避免类重复加载
  2. 保护核心类安全

- **垃圾回收机制**

  1. **引用计数法**：有引用计数 +1，失效 -1，为 0 时回收（有循环引用问题）
  2. **可达性分析**：从 GC Roots 出发，不可达的对象回收

  垃圾回收算法：
  1. **标记-清除**：标记后清除，产生内存碎片
  2. **标记-整理**：标记后整理，无碎片但效率低
  3. **复制算法**：将内存分为两块，只使用一块，效率高但空间浪费
  4. **分代收集**：新生代用复制算法，老年代用标记-清除或标记-整理

- **谈谈对 JVM 的理解?**

  JVM 是 Java 虚拟机，负责执行字节码：
  1. 类加载机制
  2. 内存管理
  3. 垃圾回收
  4. 即时编译（JIT）

- **JVM 内存区域，开线程影响哪块区域内存？**

  开线程影响：
  1. **虚拟机栈**：每个线程一个栈
  2. **本地方法栈**：每个线程一个栈
  3. **程序计数器**：每个线程一个计数器

  不影响：堆、方法区（线程共享）

- **对 Dalvik、ART 虚拟机有什么了解？对比**

  | 特性 | Dalvik | ART |
  |------|--------|-----|
  | 编译方式 | JIT（即时编译） | AOT（预编译） |
  | 性能 | 一般 | 更好 |
  | 内存占用 | 较少 | 较多 |
  | 安装速度 | 快 | 慢（需要编译） |
  | 运行速度 | 一般 | 更快 |

## 7.多线程

- **谈一谈java线程模型**

  Java 线程模型：
  1. 用户级线程（ULT）：用户空间实现
  2. 内核级线程（KLT）：内核空间实现
  3. Java 使用 KLT 模型

- **Java中创建线程的方式,Callable,Runnable,Future,FutureTask**

  1. 继承 Thread 类
  2. 实现 Runnable 接口
  3. 实现 Callable 接口（有返回值）
  4. 使用线程池

  - Callable：有返回值，可抛异常
  - Runnable：无返回值
  - Future：异步计算结果
  - FutureTask：Future 的实现类

- **线程的几种状态**

  1. NEW：新建
  2. RUNNABLE：可运行
  3. BLOCKED：阻塞
  4. WAITING：等待
  5. TIMED_WAITING：超时等待
  6. TERMINATED：终止

- **谈谈线程死锁，如何有效的避免线程死锁？**

  死锁：两个或多个线程互相等待对方释放资源。

  避免方法：
  1. 按顺序获取锁
  2. 设置超时时间
  3. 使用 tryLock
  4. 避免嵌套锁

- **如何实现多线程中的同步**

  1. synchronized 关键字
  2. Lock 接口
  3. volatile 关键字
  4. 原子类（Atomic）
  5. 并发工具类（CountDownLatch、Semaphore 等）

- **synchronized和Lock的使用、区别,原理；**

  | 特性 | synchronized | Lock |
  |------|-------------|------|
  | 实现 | JVM 层面 | API 层面 |
  | 锁释放 | 自动 | 手动 |
  | 可中断 | 不可 | 可以 |
  | 公平锁 | 非公平 | 可选 |
  | 条件变量 | 无 | 支持多个 |

- **volatile，synchronized和volatile的区别？为何不用volatile替代synchronized？**

  | 特性 | volatile | synchronized |
  |------|----------|-------------|
  | 原子性 | 不保证 | 保证 |
  | 可见性 | 保证 | 保证 |
  | 有序性 | 保证 | 保证 |
  | 性能 | 好 | 差 |

  不能替代：volatile 不保证原子性。

- **锁的分类，锁的几种状态，CAS原理**

  锁分类：
  1. 公平锁/非公平锁
  2. 可重入锁/不可重入锁
  3. 独享锁/共享锁
  4. 乐观锁/悲观锁

  锁状态：
  1. 无锁
  2. 偏向锁
  3. 轻量级锁
  4. 重量级锁

  CAS（Compare And Swap）：比较并交换，原子操作。

- **为什么会有线程安全？如何保证线程安全**

  原因：多线程并发访问共享资源。

  保证方法：
  1. synchronized
  2. Lock
  3. volatile
  4. 原子类
  5. 并发容器

- **sleep()与wait()区别,run和start的区别,notify和notifyall区别,锁池,等待池**

  - sleep()：Thread 方法，不释放锁
  - wait()：Object 方法，释放锁

  - run()：线程执行的任务
  - start()：启动线程

  - notify()：唤醒一个等待线程
  - notifyAll()：唤醒所有等待线程

  - 锁池：等待获取锁的线程
  - 等待池：调用 wait() 的线程

- **Java多线程通信**

  1. wait/notify
  2. Condition
  3. CountDownLatch
  4. CyclicBarrier
  5. Semaphore

- **为什么Java用线程池**

  1. 减少线程创建销毁开销
  2. 控制并发数量
  3. 统一管理线程
  4. 提高响应速度

- **Java中的线程池参数,共有几种**

  核心参数：
  1. corePoolSize：核心线程数
  2. maximumPoolSize：最大线程数
  3. keepAliveTime：空闲线程存活时间
  4. workQueue：任务队列
  5. threadFactory：线程工厂
  6. handler：拒绝策略

  内置线程池：
  1. FixedThreadPool：固定线程数
  2. CachedThreadPool：可缓存线程池
  3. SingleThreadExecutor：单线程池
  4. ScheduledThreadPool：定时线程池

- **说下 Java 中的线程创建方式，线程池的工作原理**

  线程池工作原理：
  1. 提交任务
  2. 如果核心线程未满，创建核心线程执行
  3. 如果核心线程已满，放入队列
  4. 如果队列已满，创建非核心线程执行
  5. 如果线程已满，执行拒绝策略

## 8.注解

- **注解的分类和底层实现原理**

  分类：
  1. 标准注解：@Override、@Deprecated、@SuppressWarnings
  2. 元注解：@Target、@Retention、@Documented、@Inherited
  3. 自定义注解

  底层原理：
  - 注解本质是接口，继承 java.lang.annotation.Annotation
  - 通过反射获取注解信息
  - 运行时注解通过动态代理实现

- **自定义注解**

  ```java
  @Target(ElementType.METHOD)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface MyAnnotation {
      String value() default "";
      int priority() default 0;
  }
  ```

## 9.反射

- **什么是反射**

  反射是 Java 在运行时获取类信息并操作类成员的机制：
  - 获取类的 Class 对象
  - 获取类的属性、方法、构造器
  - 动态创建对象、调用方法、访问属性

- **反射机制的相关类**

  1. `Class`：类的元数据
  2. `Field`：类的属性
  3. `Method`：类的方法
  4. `Constructor`：类的构造器

- **反射中如何获取Class类的实例**

  1. `Class.forName("全限定类名")`
  2. `对象.getClass()`
  3. `类名.class`
  4. `ClassLoader.loadClass("全限定类名")`

- **如何获取一个类的属性对象 & 构造器对象 & 方法对象**

  ```java
  // 获取属性
  Field field = clazz.getField("name");
  Field[] fields = clazz.getFields();
  Field declaredField = clazz.getDeclaredField("name");
  Field[] declaredFields = clazz.getDeclaredFields();

  // 获取构造器
  Constructor constructor = clazz.getConstructor(String.class);
  Constructor[] constructors = clazz.getConstructors();
  Constructor declaredConstructor = clazz.getDeclaredConstructor(String.class);
  Constructor[] declaredConstructors = clazz.getDeclaredConstructors();

  // 获取方法
  Method method = clazz.getMethod("getName");
  Method[] methods = clazz.getMethods();
  Method declaredMethod = clazz.getDeclaredMethod("getName");
  Method[] declaredMethods = clazz.getDeclaredMethods();
  ```

- **Class.getField和Class.getDeclaredField的区别，getDeclaredMethod和getMethod的区别**

  - `getField()`：获取 public 属性（包括继承的）
  - `getDeclaredField()`：获取所有属性（不包括继承的）

  - `getMethod()`：获取 public 方法（包括继承的）
  - `getDeclaredMethod()`：获取所有方法（不包括继承的）

- **反射机制的优缺点**

  优点：
  1. 动态性：运行时获取类信息
  2. 灵活性：可以访问私有成员
  3. 扩展性：支持框架设计

  缺点：
  1. 性能开销大
  2. 破坏封装性
  3. 编译时无法检查错误

## 10.泛型

- **泛型概念的提出（为什么需要泛型）？**

  1. 编译时类型检查
  2. 避免强制类型转换
  3. 代码复用

- **什么是泛型？**

  泛型是参数化类型，将类型作为参数传递：
  - 泛型类：`class MyClass<T>`
  - 泛型接口：`interface MyInterface<T>`
  - 泛型方法：`<T> T myMethod(T t)`

- **自定义泛型接口、泛型类和泛型方法**

  ```java
  // 泛型接口
  public interface Generator<T> {
      T next();
  }

  // 泛型类
  public class Box<T> {
      private T t;
      public void set(T t) { this.t = t; }
      public T get() { return t; }
  }

  // 泛型方法
  public <T> T getObject(Class<T> clazz) throws Exception {
      return clazz.newInstance();
  }
  ```

- **类型通配符**

  1. `<?>`：无界通配符
  2. `<? extends T>`：上界通配符（协变）
  3. `<? super T>`：下界通配符（逆变）

## 11.设计模式

- 你所知道的设计模式有哪些
- 单例设计模式
- 工厂设计模式
- 建造者模式（Builder）
- 适配器设计模式
- 装饰模式（Decorator）
- 策略模式（strategy）
- 观察者模式（Observer）

好的，下面是用 **Java** 实现的设计模式满分答案，包括每个设计模式的定义、使用场景以及代码示例：

------

### 1. 你所知道的设计模式有哪些

设计模式通常分为三大类：

- **创建型模式**：
  - **单例模式（Singleton）**
  - **工厂模式（Factory）**
  - **抽象工厂模式（Abstract Factory）**
  - **建造者模式（Builder）**
  - **原型模式（Prototype）**
- **结构型模式**：
  - **适配器模式（Adapter）**
  - **装饰器模式（Decorator）**
  - **外观模式（Facade）**
  - **桥接模式（Bridge）**
  - **组合模式（Composite）**
  - **享元模式（Flyweight）**
  - **代理模式（Proxy）**
- **行为型模式**：
  - **策略模式（Strategy）**
  - **观察者模式（Observer）**
  - **命令模式（Command）**
  - **责任链模式（Chain of Responsibility）**
  - **状态模式（State）**
  - **模板方法模式（Template Method）**
  - **访问者模式（Visitor）**
  - **中介者模式（Mediator）**
  - **备忘录模式（Memento）**

------

### 2. 单例设计模式（Singleton）

- **定义**：单例模式确保一个类只有一个实例，并提供一个全局访问点来获取该实例。
- **使用场景**：当需要确保类有且只有一个实例时，例如数据库连接、线程池等。
- **实现方式**：可以通过**饿汉式**和**懒汉式**来实现。

```java
// 饿汉式
public class Singleton {
    private static final Singleton instance = new Singleton();
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        return instance;
    }
}
// 懒汉式（线程安全）
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

------

### 3. 工厂设计模式（Factory）

- **定义**：工厂模式提供一个接口用于创建对象，但不暴露创建的具体实现。
- **使用场景**：当创建对象的过程较为复杂，或者需要创建多个类型的对象时，工厂模式可以提供解耦。

```java
// Animal接口
public interface Animal {
    void speak();
}

// Dog类
public class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("Woof!");
    }
}

// Cat类
public class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("Meow!");
    }
}

// AnimalFactory类
public class AnimalFactory {
    public Animal createAnimal(String animalType) {
        if ("dog".equalsIgnoreCase(animalType)) {
            return new Dog();
        } else if ("cat".equalsIgnoreCase(animalType)) {
            return new Cat();
        }
        return null;
    }
}
```

------

### 4. 建造者模式（Builder）

- **定义**：建造者模式通过将一个复杂对象的构建过程分解为多个简单的步骤，使得客户端可以按步骤生成不同表示的对象。
- **使用场景**：当对象的构建过程非常复杂，或者需要灵活控制构建的步骤时，建造者模式提供了很好的解决方案。

```java
// Product类
public class Product {
    private String part1;
    private String part2;

    public void setPart1(String part1) {
        this.part1 = part1;
    }

    public void setPart2(String part2) {
        this.part2 = part2;
    }

    @Override
    public String toString() {
        return "Product [part1=" + part1 + ", part2=" + part2 + "]";
    }
}

// Builder接口
public interface Builder {
    void buildPart1();
    void buildPart2();
    Product getResult();
}

// ConcreteBuilder类
public class ConcreteBuilder implements Builder {
    private Product product = new Product();

    @Override
    public void buildPart1() {
        product.setPart1("Part 1");
    }

    @Override
    public void buildPart2() {
        product.setPart2("Part 2");
    }

    @Override
    public Product getResult() {
        return product;
    }
}

// Director类
public class Director {
    private Builder builder;

    public Director(Builder builder) {
        this.builder = builder;
    }

    public Product construct() {
        builder.buildPart1();
        builder.buildPart2();
        return builder.getResult();
    }
}
```

------

### 5. 适配器设计模式（Adapter）

- **定义**：适配器模式通过创建一个适配器类，将不兼容的接口转换为客户端所期望的接口。
- **使用场景**：常见于需要与第三方库或老旧系统进行集成时。

```java
// Target接口
public interface Target {
    void request();
}

// Adaptee类
public class Adaptee {
    public void specificRequest() {
        System.out.println("Specific request");
    }
}

// Adapter类
public class Adapter implements Target {
    private Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void request() {
        adaptee.specificRequest();
    }
}
```

------

### 6. 装饰模式（Decorator）

- **定义**：装饰模式允许动态地为一个对象添加新的功能，而无需改变其结构。
- **使用场景**：当需要给某个对象增加额外功能时，使用装饰模式可以不修改原有代码。

```java
// Coffee接口
public interface Coffee {
    int cost();
}

// SimpleCoffee类
public class SimpleCoffee implements Coffee {
    @Override
    public int cost() {
        return 5;
    }
}

// MilkDecorator类
public class MilkDecorator implements Coffee {
    private Coffee coffee;

    public MilkDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    @Override
    public int cost() {
        return coffee.cost() + 2;
    }
}
```

------

### 7. 策略模式（Strategy）

- **定义**：策略模式定义了一系列算法，并将每一个算法封装起来，使它们可以互换使用。
- **使用场景**：例如，排序算法的选择、支付方式的切换等。

```java
// Strategy接口
public interface Strategy {
    String execute();
}

// ConcreteStrategyA类
public class ConcreteStrategyA implements Strategy {
    @Override
    public String execute() {
        return "Strategy A";
    }
}

// Context类
public class Context {
    private Strategy strategy;

    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public String executeStrategy() {
        return strategy.execute();
    }
}
```

------

### 8. 观察者模式（Observer）

- **定义**：观察者模式定义了一种一对多的依赖关系，当一个对象状态发生变化时，所有依赖它的对象都会收到通知。
- **使用场景**：如事件驱动编程、UI组件与数据绑定等。

```java
// Observer接口
public interface Observer {
    void update(String state);
}

// Subject类
public class Subject {
    private List<Observer> observers = new ArrayList<>();

    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    public void notifyObservers(String state) {
        for (Observer observer : observers) {
            observer.update(state);
        }
    }
}

// ConcreteObserver类
public class ConcreteObserver implements Observer {
    private String name;

    public ConcreteObserver(String name) {
        this.name = name;
    }

    @Override
    public void update(String state) {
        System.out.println(name + " received state update: " + state);
    }
}
```

