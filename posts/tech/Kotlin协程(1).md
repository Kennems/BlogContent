---
title : 'Kotlin协程(1)'
date : 2024-08-06T22:30:13+08:00
lastmod: 2024-08-13T22:20:13+08:00
description : "Kotlin协程(1)" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Kotlin"]
---
# Kotlin协程(1)

看明白，讲清楚。

协程处理的问题：

- 处理耗时任务， 这种任务常常会阻塞主线程
- 保证主线程安全，即确保安全的从主线程调用任何`suspend`函数

## 异步任务

- 协程让异步逻辑同步化，杜绝回调地狱
- 协程最核心的点就是：函数或一段程序能够被挂起，稍后再在挂起的位置恢复

## 协程的挂起与恢复

- 常规函数基础操作包括 ： `invoke(call)` 和 `return`, 协程新增了 `suspend` 和 `resume ` ；
  - `suspend` —— 也称为挂起或暂停，用于暂停执行当前协程，并保存所有局部变量
  - `resume`——用于让已暂停的协程从其暂停处继续执行

### 挂起函数

- 使用suspend关键字修饰的函数叫做挂起函数
- 挂起函数只能在协程体内或其他挂起函数内调用

### Kotlin协程的两部分

- **基础设施层次**，标准库的协程API， 主要对协程提供了概念和语义上最基本的支持
- **业务框架层**，协程的上层框架支持

### 调度器

所有协程必须在调度器中运行，即使它们在主线程上运行也是如此。

#### Dispatchers.Main

Android上的主线程，用来处理UI交互和一些轻量级任务

- 调用suspend函数
- 调用UI函数
- 更新LiveData

#### Dispatchers.IO

专为磁盘和网络IO进行了优化

- 数据库
- 文件读写
- 网络处理

#### Dispatchers.Default

非主线程，专为CPU密集型任务进行了优化

- 数组排序
- JSON数据解析
- 处理差异判断

### 任务泄漏

类似内存泄漏

结构化并发： 

取消任务：当某项任务不再需要时取消它

- 追踪任务，当任务正在执行时，追踪它
- 发出错误信号，当协程失败时， 发出错误信号表明有错误发生

### Coroutine Scope

- 定义协程必须指定其`CoroutineScope`， 它会跟踪所有协程，同样它还可以取消由它启动的所有协程

- 重用的相关API有：
  - `GlobalScope`：生命周期是process级别的，即使Activity， Fragment 已经被销毁，协程仍然在执行
  - `MainScope`，在Activity中使用，可以在`onDestroy()`中取消协程
  - `viewModelScope`，只能在`ViewModel`中使用，绑定`ViewModel`的声明周期
  - `lifecycleScope`，只能在Activity，Fragment中使用，会绑定Activity 和 Fragment的生命周期

## 协程使用

#### retrofit API

```kotlin
// 定义 UserServiceApi 接口，用于声明与用户相关的API请求
interface UserServiceApi {

    // 声明一个 GET 请求，用于加载指定名称的用户信息
    // @Query 注解表示请求中的查询参数 "name"，会附加到URL中
    // 返回类型是 Call<User>，表示一个异步的 HTTP 请求，可以通过 enqueue 方法执行
    @GET("user")
    fun loadUser(@Query("name") name: String): Call<User>

    // 声明一个 GET 请求，使用 Kotlin 的协程机制
    // suspend 关键字表示这是一个挂起函数，可以在协程中被调用
    // 返回类型是 User，表示直接返回解析后的用户对象
    @GET("user")
    suspend fun getUser(@Query("name") name: String): User
}

// 创建一个懒加载的 userServiceApi 实例，使用 Retrofit 构建器
val userServiceApi: UserServiceApi by lazy {
    // 使用 Retrofit.Builder 构建 Retrofit 实例
    val retrofit = retrofit2.Retrofit.Builder()
        // 设置 API 请求的基础 URL
        .baseUrl("https://apifoxmock.com/m1/4944163-4601795-default/kotlinstudyserver/")
        // 添加一个转换工厂，这里使用 MoshiConverterFactory 来解析 JSON 数据
        .addConverterFactory(MoshiConverterFactory.create())
        // 构建 Retrofit 实例
        .build()

    // 创建并返回 UserServiceApi 的具体实现
    retrofit.create(UserServiceApi::class.java)
}
```

#### User

```kotlin
data class User(val name: String, val address: String)
```

#### UserRepository.kt

```kotlin
class UserRepository {
    // 声明一个挂起函数 getUser，用于获取用户信息
    // 该函数在协程中执行
    suspend fun getUser(name: String): User {
        // 调用 userServiceApi 的 getUser 方法，传递用户名称作为参数
        // userServiceApi 是一个 API 接口实例，返回的 User 对象表示用户信息
        return userServiceApi.getUser(name)
    }
}
```

#### MainViewModel.kt

```kotlin
// MainViewModel 继承自 ViewModel，用于管理与用户相关的 UI 数据和业务逻辑
class MainViewModel : ViewModel() {

    // MutableLiveData 用于存储和观察用户数据的 LiveData
    // 可以在 UI 组件中观察 userLiveData，当数据发生变化时更新 UI
    val userLiveData = MutableLiveData<User>()

    // 创建 UserRepository 实例，用于获取用户数据
    private val userRepository = UserRepository()

    // getUser 函数用于从 UserRepository 获取用户数据，并将其存储到 userLiveData 中
    fun getUser(name: String) {
        // 使用 viewModelScope 启动协程，viewModelScope 是一个与 ViewModel 生命周期相关联的协程范围
        viewModelScope.launch {
            // 调用 UserRepository 的 getUser 方法，获取用户信息
            // 将获取的用户信息设置到 userLiveData 中，触发观察者更新 UI
            userLiveData.value = userRepository.getUser(name)
        }
    }
}
```

#### Activity

```kotlin
// MainActivity7 继承自 AppCompatActivity，用于显示主界面
class MainActivity7 : AppCompatActivity() {

    // 使用 viewModels 委托来获取 MainViewModel 实例
    private val mainViewModel: MainViewModel by viewModels()

    // 存储对 TextView 的引用（可选，后续可以用来更新 UI）
    private var nameTextView: TextView? = null

    // 创建一个 MainScope 实例，用于管理协程生命周期
    // 工厂设计模式实例化 MainScope
    private val mainScope = MainScope()

    @SuppressLint("StaticFieldLeak") // SuppressLint 注解用于忽略静态字段泄漏警告
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // 启用边缘到边缘模式，以使布局填充整个屏幕
        enableEdgeToEdge()
        // 设置活动的布局
        setContentView(R.layout.activity_main7)

        // 使用 ViewCompat 和 WindowInsetsCompat 处理系统窗口的内边距
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            // 获取系统窗口的内边距
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            // 设置视图的内边距，避免被系统窗口遮挡
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            // 返回处理后的 insets
            insets
        }

        // 使用 DataBindingUtil 绑定布局文件与视图模型
        val binding =
            DataBindingUtil.setContentView<ActivityMain7Binding>(this, R.layout.activity_main7)
        // 将 ViewModel 绑定到布局
        binding.viewModel = mainViewModel
        // 设置生命周期所有者，使数据绑定可以观察 LiveData
        binding.lifecycleOwner = this

        // 设置提交按钮的点击监听器
        binding.submitButton.setOnClickListener(View.OnClickListener {
            // 当按钮被点击时，调用 ViewModel 的 getUser 方法
            // 这里使用 "ken" 作为查询参数
            mainViewModel.getUser("ken")
        })
    }

    // 活动销毁时取消协程，释放资源
    override fun onDestroy() {
        super.onDestroy()
        // 取消所有在 MainScope 中启动的协程
        mainScope.cancel()
    }
}
```

## 协程构建器

- launch与async构建器都用来启动新协程
  - launch，返回 Job 并且不附带任何结果值
  - async，返回一个Deferred，Deferred也是一个Job，可以使用`.await()`在一个延期的值上得到它的最终结果
- 等待一个作业
  - join和await
  - 组合并发

### 协程的启动模式

- DEFAULT：协程创建后，立即开始调度，在调度前如果协程被取消，其将直接进入取消响应的状态
- ATOMIC：协程创建后，立即开始调度，协程执行到第一个挂起点之前不响应取消
- LAZY：只有协程被需要时，包括主动调用协程的start，join或者await等函数时才会开始调度，如果调度前就被取消，那么该协程将直接进入异常结束状态
- UNDISPATCHED：协程创建后立即在**当前函数调用栈**中执行，直到遇到第一个真正挂起的点

## coroutineScope 与 runBlocking

- runBlocking是常规函数，而coroutineScope是挂起函数
- 它们都会等待其协程体以及所有子协程结束，主要区别在于runBlocking方法会阻塞当前线程来等待，而coroutineScope只是挂起，会释放底层线程用于其他用途

区别：

- `coroutineScope`：一个协程失败了，所有其他兄弟协程也会被取消
- `supervisorScope`：一个协程失败了，不会影响其他兄弟协程

### Job对象

- 对于每一个创建的协程（通过launch或者async），会返回一个Job实例，该实例是协程的唯一标识，并且负责管理协程的生命周期
- 一个任务可以包含一系列状态，新创建（New）、活跃（Active）、完成中（Completing）、已完成（Completed）、取消中（Cancelling）和已取消（Cancelled）。可以访问Job的属性：isActive，isCancelled和isCompleted。

### Job的生命周期

如果协程处于活跃状态，协程运行出错或者调用 `job.cancel()` 都会将当前任务置为取消中（`Cancelling`）状态（`isActive = false`, `isCancelled = true`）。当所有的子协程都完成后，协程会进入已取消（`Cancelled`）。

```kotlin
    @Test
    fun `test scope cancel`() = runBlocking<Unit> {
        // 创建一个 CoroutineScope，指定使用 Default 调度器
        val scope = CoroutineScope(Dispatchers.Default)

        // 启动第一个协程，延迟 1000 毫秒后打印 "job 1"
        scope.launch {
            delay(1000)
            println("job 1")
        }

        // 启动第二个协程，延迟 1000 毫秒后打印 "job 2"
        scope.launch {
            delay(1000)
            println("job 2")
        }

        // 延迟 100 毫秒，确保前面两个协程有时间启动
//        delay(100)
        // 取消 scope 中的所有协程
//        scope.cancel() // 两个均不打印
        // 延迟 1500 毫秒，确保可以看到协程的输出（如果它们没有被取消的话）
        delay(1500) // 两个可以正常打印
    }

    @Test
    fun `test brother cancel`() = runBlocking<Unit> {
        // 创建一个 CoroutineScope，指定使用 Default 调度器
        val scope = CoroutineScope(Dispatchers.Default)

        // 启动第一个协程，延迟 1000 毫秒后打印 "job 1"
        val job1 = scope.launch {
            delay(1000)
            println("job 1")
        }

        // 启动第二个协程，延迟 1000 毫秒后打印 "job 2"
        val job2 = scope.launch {
            delay(1000)
            println("job 2")
        }

        // 延迟 100 毫秒，确保前面两个协程有时间启动
        delay(100)
        // 取消 job1，但不取消 job2
        job1.cancel()
        // 延迟 1500 毫秒，确保可以看到 job2 的输出
        delay(1500)
    }
```

## 取消协程

在`cancel()`后保证协程被取消

```kotlin
    @Test
    fun `test cancel cpu task by isActive`() = runBlocking<Unit> {
        // 获取当前时间的毫秒数，用于计算任务执行的时间点
        val startTime = System.currentTimeMillis()

        // 启动一个协程，运行在默认调度器上（通常是后台线程）
        val job = launch(Dispatchers.Default) {
            var nextPrintTime = startTime // 下次打印信息的时间
            var i = 0 // 计数器，用于限制任务执行次数

            // 循环执行，直到达到5次或协程被取消
            while (i < 5 && isActive) {
                // 检查是否到达下一个打印时间点
                if (System.currentTimeMillis() >= nextPrintTime) {
                    // 打印当前状态和计数器
                    println("job: I'm sleeping ${i++}")
                    nextPrintTime += 500 // 更新下次打印的时间
                }
            }
        }

        // 延迟 1300 毫秒，模拟主线程的等待时间
        delay(1300)

        // 主线程输出，表示等待的时间结束
        println("main : I'm tired of waiting")

        // 取消 job 协程并等待其完全结束
        job.cancelAndJoin()

        // 主线程输出，表示可以退出了
        println("main : Now I can quit.")
    }

    @Test
    fun `test cancel cpu task by ensureActive`() = runBlocking<Unit> {
        // 获取当前时间的毫秒数，用于计算任务执行的时间点
        val startTime = System.currentTimeMillis()

        // 启动一个协程，运行在默认调度器上（通常是后台线程）
        val job = launch(Dispatchers.Default) {
            var nextPrintTime = startTime // 下次打印信息的时间
            var i = 0 // 计数器，用于限制任务执行次数

            // 循环执行，直到达到5次
            while (i < 5) {
                // 确保协程处于活跃状态，如果协程已取消则会抛出 CancellationException
                ensureActive()

                // 检查是否到达下一个打印时间点
                if (System.currentTimeMillis() >= nextPrintTime) {
                    // 打印当前状态和计数器
                    println("job: I'm sleeping ${i++}")
                    nextPrintTime += 500 // 更新下次打印的时间
                }
            }
        }

        // 延迟 1300 毫秒，模拟主线程的等待时间
        delay(1300)

        // 主线程输出，表示等待的时间结束
        println("main : I'm tired of waiting")

        // 取消 job 协程并等待其完全结束
        job.cancelAndJoin()

        // 主线程输出，表示可以退出了
        println("main : Now I can quit.")
    }
```

### CPU密集型任务取消

- `isActive`是一个可以被使用在`CoroutineScope`中的扩展属性，检查Job是否处于活跃状态
- `ensureActive()`， 如果job处于非活跃状态，这个方法会立即抛出异常

- `yield`函数会检查所在协程的状态，如果已经取消，则抛出`CancellationExcepiton`予以相应。此外，它还会尝试让出线程的执行权，给其他协程提供执行机会。

### 协程取消的副作用

- 在finally中释放资源。

  - ```kotlin
        @Test
        fun `test release resources`() = runBlocking<Unit> {
            val job = launch {
                try {
                    repeat(1000){
                        it->
                        println("job : I'm sleeping $it ...")
                        delay(500L)
                    }
                } catch (e: Exception) {
                    println("Job: I'm running finally")
                    // 释放资源
                }
            }
    
            delay(1300)
            println("main : I'm tired of waiting")
            job.cancelAndJoin()
            println("main : Now I can quit.")
        }
    ```

- use函数：该函数只能被实现了Closeable的对象使用，程序结束的时候会自动调用close方法，适合文件对象。

  - ```kotlin
        @Test
        fun `test use function`() = runBlocking<Unit> {
            // 使用 use 函数自动管理资源
            // use 函数会自动关闭资源，无需手动调用 close
            BufferedReader(FileReader("D:\\test\\test.txt")).use {
                var line: String?
                // 无限循环读取文件内容，直到文件末尾
                while (true) {
                    // 读取一行内容，如果为 null（文件末尾），则退出循环
                    line = it.readLine() ?: break
                    println(line) // 打印每一行内容
                }
            }
        }
    ```

  ### 不能被取消的任务

```kotlin
    @Test
    fun `test cancel with NonCancellable`() = runBlocking<Unit> {
        // 启动一个新的协程
        val job = launch {
            try {
                // 在协程中重复执行任务1000次
                repeat(1000) { it ->
                    println("job : I'm sleeping $it ...")  // 打印当前任务的状态
                    delay(500L)  // 暂停500毫秒
                }
            } finally {
                // 这里的代码块在协程取消时执行
                // 使用 NonCancellable 上下文，确保这个代码块中的任务不会被取消
                withContext(NonCancellable) {
                    println("job: I'm running finally")  // 打印消息，表示进入了 finally 部分
                    delay(1000L)  // 这里的延迟不会被取消，即使协程已经被取消
                    println("job: And I've just delayed for 1 sec ")  // 打印延迟后的消息
                }
            }
        }

        // 主协程延迟1300毫秒，给子协程足够的时间运行
        delay(1300)
        println("main: I'm tired of waiting!")  // 打印主协程等待的消息

        // 取消子协程并等待它完成
        job.cancelAndJoin()
        println("main: Now I can quit.")  // 打印主协程完成后的消息
    }
```

## 超时任务

- 取消协程的理由是有可能超时
- `withTimeoutOrNull` 通过返回null来进行超时操作，从而替代抛出一个异常

```kotlin
    @Test
    fun `test deal with timeout`() = runBlocking {
        // 使用 withTimeout 函数设置超时时间
        withTimeout(1300L) {  // 设置最大执行时间为1300毫秒
            repeat(1000) { i ->  // 计划执行1000次任务
                println("job: I'm sleeping $i ...")  // 打印当前任务的状态
                delay(500L)  // 每个任务暂停500毫秒
            }
        }
    }

    @Test
    fun `test deal with timeout return null`() = runBlocking {
        // 使用 withTimeoutOrNull 函数设置超时时间并处理超时返回 null
        val result = withTimeoutOrNull(1300L) {  // 设置最大执行时间为1300毫秒
            repeat(1000) { i ->  // 计划执行1000次任务
                println("job: I'm sleeping $i ...")  // 打印当前任务的状态
                delay(500L)  // 每个任务暂停500毫秒
            }
            "Done"  // 如果在1300毫秒内完成所有任务，将返回 "Done"
        }

        // 打印 result 结果，如果任务在超时时间内完成，则 result 为 "Done"，否则为 null
        println("Result is $result")
    }
```

## 协程上下文

`CoroutineContext`是一组用于定义协程行为的元素，它由如下几项构成：

- `Job`：控制协程的生命周期
- `CoroutineDispatcher`：向合适的线程分发任务
- `CoroutineName`：协程的名称，调试的时候很有用
- `CoroutineExceptionHandler`：处理未被捕捉的异常

### 组合上下文中的元素

- 有时我们需要在协程上下文中定义多个元素。可以使用 + 操作符来实现。

```kotlin
    @Test
    fun `test CoroutineContext`() = runBlocking<Unit> {
        launch(Dispatchers.Default + CoroutineName("test")) {
            println("I'm working in thread ${Thread.currentThread().name}")
        }
    }
```

### 协程上下文的继承

协程的上下文 = 默认值 + 继承的`CoroutineContext` + 参数

