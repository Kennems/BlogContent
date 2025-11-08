---
title : 'Kotlin协程(2)'
date : 2024-08-09T22:30:13+08:00
lastmod: 2024-08-13T22:20:13+08:00
description : "Kotlin协程(2)" 
image : img/cat.jpg
draft : false    
categories : ["Kotlin学习笔记"]
tags : ["Kotlin"]
---
# Kotlin协程(2)

## 异常处理的必要性 

```kotlin
    @OptIn(ExperimentalStdlibApi::class) // 标记该代码使用了实验性标准库 API
    @Test // 表示这是一个测试函数
    fun `test CoroutineContext extend2`() = runBlocking { // 使用 runBlocking 启动协程
        // 创建一个协程异常处理器，用于捕获协程中的异常
        val coroutineExceptionHandler = CoroutineExceptionHandler { _, exception ->
            println("Caught : $exception") // 打印捕获到的异常
        }

        // 创建一个协程作用域，包含一个 Job，使用主线程调度器和异常处理器
        val scope = CoroutineScope(
            Job() + Dispatchers.Main + coroutineExceptionHandler // Job() 用于管理协程的生命周期
        )

        // 在 IO 线程上启动一个新的协程
        val job = scope.launch(Dispatchers.IO) {
            // 获取当前协程上下文中的调度器
            val dispatcher = coroutineContext[CoroutineDispatcher]
            // 打印当前使用的调度器
            println("Current dispatcher: $dispatcher")
        }

        // 等待协程完成
        job.join() // 确保主协程在子协程完成后再继续
    }
```

### 异常的传播

- 协程构建器有两种形式：1、**自动传播异常（launch与actor）**2、**向用户暴露异常（async与produce）**， 当这些构建器用于创建一个**根协程**时（该协程不是另一个协程的子协程），前者这类构建器，异常会在它发生的第一时间被抛出，而后者则依赖用户来最终消费异常，例如通过await或receive。

```kotlin
    // 根协程
    @Test
    @OptIn(DelicateCoroutinesApi::class)
    fun `test exception propagation`() = runBlocking<Unit> {
        // 在 GlobalScope 中启动一个新协程，返回一个 Job 对象
        val job = GlobalScope.launch {
            try {
                // 在协程中抛出一个 IndexOutOfBoundsException
                throw IndexOutOfBoundsException()
            } catch (e: Exception) {
                // 捕获异常并打印一条消息，指示捕获了 IndexOutOfBoundsException
                println("IndexOutOfBoundsException")
                // 如果需要可以选择打印堆栈轨迹
                // e.printStackTrace()
            }
        }

        // 等待协程完成
        job.join()

        // 在 GlobalScope 中启动一个新协程，并返回一个 Deferred 对象
        val deffer = GlobalScope.async {
            // 在协程中抛出一个 ArithmeticException
            throw ArithmeticException()
        }

        try {
            // 等待异步任务完成并获取结果，如果任务抛出异常，这里会重新抛出异常
            deffer.await()
        } catch (e: Exception) {
            // 捕获异常并打印一条消息，指示捕获了 ArithmeticException
            println("ArithmeticException")
            // 如果需要可以选择打印堆栈轨迹
            // e.printStackTrace()
        }
    }
```

### 非根协程的异常

- 其他协程所创建的协程中，产生的异常总是会被传播

```kotlin
    /*
    非根协程，job是scope内的子协程，所以会直接消费掉异常
     */
    @Test
    fun `test exception propagation2`() = runBlocking {
        // 创建一个新的 CoroutineScope，使用 Job 作为其上下文元素
        val scope = CoroutineScope(Job())

        // 在 scope 中启动一个新协程，返回一个 Job 对象
        val job = scope.launch {
            // 在协程中启动一个异步任务
            async {
                // 在异步任务中抛出一个 IllegalArgumentException
                throw IllegalArgumentException()
            }
        }

        // 等待 launch 启动的协程完成
        job.join()
    }
```

### 异常的传播特性

- 当一个协程由于一个异常而运行失败时，它会传播这个异常并传递给它的父级。接下来，父级会进行下面几步操作：
  - 取消它自己的子级
  - 取消它自己
  - 将异常传播并传递给它的父级

#### SupervisorJob

- 使用`SupervisorJob`时，**一个子协程的运行失败不会影响其他子协程**，`SupervisorJob`不会传播异常给它的父级，它会让子协程自己处理异常。
- **需求**：在作用域内定义作业的UI组件，如果任何一个UI的子作业执行失败了，它并不总是有必要取消整个UI组件，但是如果UI组件被销毁了，由于它的结果不再被需要了，它就有必要使所有的子作业执行失败。

```kotlin
    @Test
    fun `test supervisorJob`() = runBlocking<Unit> {
        // 创建一个新的 CoroutineScope，使用 SupervisorJob 作为其上下文元素
        val supervisor = CoroutineScope(SupervisorJob())
        // 如果使用普通的 Job，注释掉上一行并启用以下行
        // val supervisor = CoroutineScope(Job())

        // 在 supervisor 作用域中启动第一个子协程，返回一个 Job 对象
        val job1 = supervisor.launch {
            // 延迟 100 毫秒以模拟一些工作
            delay(100)
            println("child 1")
            // 在第一个子协程中抛出一个 IllegalArgumentException
            throw IllegalArgumentException()
        }

        // 在 supervisor 作用域中启动第二个子协程，返回一个 Job 对象
        val job2 = supervisor.launch {
            try {
                // 无限延迟以模拟长时间运行的任务
                delay(Long.MAX_VALUE)
            } finally {
                // 当协程取消时，最终会打印 "child 2 finished"
                println("child 2 finished")
            }
        }

        // 以下是可选部分，如果需要，可以取消注释
        // 延迟 200 毫秒后取消 supervisor 作用域内的所有协程
        // delay(200)
        // supervisor.cancel()

        // 等待 job1 和 job2 都完成
        joinAll(job1, job2)
    }
```

#### SupervisorScope

- 当作业自身执行失败的时候，所以子作业将会被全部取消

```kotlin
@Test
fun `test supervisorScope`() = runBlocking<Unit> {
    // 使用 supervisorScope 启动一个新的协程作用域
    supervisorScope {
        // 在 supervisorScope 中启动一个子协程，返回一个 Job 对象
        launch {
            // 延迟 100 毫秒以模拟一些工作
            delay(100)
            // 输出 "child 1"，表示子协程已完成其任务
            println("child 1")
            // 在协程中抛出一个 IllegalArgumentException，以测试异常传播
            throw IllegalArgumentException()
        }

        // 在 supervisorScope 中启动另一个协程
        try {
            // 延迟一个非常长的时间，模拟长时间运行的任务
            delay(Long.MAX_VALUE)
        } finally {
            // 当 supervisorScope 被取消时（由于异常），最终会打印 "child 2 finished."
            println("child 2 finished.")
        }
    }
}
```

```kotlin
    @Test
    fun `test supervisorScope2`() = runBlocking<Unit> {
        // 使用 supervisorScope 来启动一个协程作用域
        supervisorScope {
            // 在 supervisorScope 中启动一个子协程
            val child = launch {
                try {
                    // 输出 "The child is sleeping" 表示子协程进入睡眠状态
                    println("The child is sleeping")
                    // 延迟无限长的时间，模拟长时间运行的任务
                    delay(Long.MAX_VALUE)
                } finally {
                    // 当子协程被取消时，最终会打印 "The child is cancelled"
                    println("The child is cancelled")
                }
            }

            // 让出线程以确保子协程启动并进入延迟状态
            yield()

            // 在 supervisorScope 中抛出一个异常
            println("Throwing an exception from the scope")
            throw AssertionError()  // 抛出一个断言错误，用于测试作用域的异常处理
        }
    }
```

## 异常的捕获

- 使用`CoroutineExcepitonHandler`对协程的异常进行捕获
- 以下的条件被满足时，异常就会被捕获：
  - **时机**：异常是被自动抛出异常的协程所抛出的（使用launch，而不是async时）；
  - **位置**：在`CoroutineScope`的`CoroutineContext`中或在一个根协程（`CoroutineScope`或者`supervisorScope`的直接子协程）中。

## Android中全局异常处理

- 全局异常处理器可以获取到所有协程未处理的未捕获异常，不过它并不能对异常进行捕获，虽然不能阻止程序崩溃，全局异常处理器在程序调试和异常上报等场景中仍然有非常大的用处。
- 我们需要在`classpath`下面创建META-INF/services目录，并在其中创建一个名为`kotlinx.coroutines.CoroutineExceptionHandler`的文件，文件内容就是我们的全局异常处理器的全类名。

## 取消与异常

- 取消与异常紧密相关，协程内部使用`CancellationExcepiton`来进行取消，这个异常会被忽略。
- 当子协程被取消时，不会取消它的父协程
- 如果一个协程遇到了`CancellationExcepiton`以外的异常，它将使用该异常取消它的父协程。当父协程的所有子协程都结束后，异常才会被父协程处理。

```kotlin
    @Test
    fun `test cancel and exception`() = runBlocking<Unit> {
        // 启动一个协程作为父协程
        val job = launch {
            // 在父协程中启动一个子协程
            val child = launch {
                try {
                    // 子协程尝试延迟执行，模拟长时间运行的任务
                    delay(Long.MAX_VALUE)
                } finally {
                    // 当子协程被取消时，执行此块
                    println("Child is cancelled")
                }
            }
            // 让父协程暂时让出线程，给其他协程执行的机会
            yield()
            println("Cancelling child")
            // 取消子协程，并等待其执行完 finally 块
            child.cancelAndJoin()
            // 再次让出线程
            yield()
            println("Parent is not cancelled")
        }
        // 等待父协程完成执行
        job.join()
    }
```

```kotlin
    @Test
    fun `test cancel and exception2`() = runBlocking<Unit> {
        // 定义一个 CoroutineExceptionHandler 来处理未捕获的异常
        val handler = CoroutineExceptionHandler { _, exception ->
            println("Caught: $exception")
        }

        // 使用 GlobalScope 启动一个顶层协程，并应用异常处理器
        val job = GlobalScope.launch(handler) {
            // 第一个子协程，模拟一个需要很长时间的任务
            launch {
                try {
                    // 使用一个很长的延迟来模拟长时间运行的任务
                    delay(Long.MAX_VALUE)
                } finally {
                    // 确保在协程被取消时，执行以下代码
                    withContext(NonCancellable) {
                        println("Children are cancelled, but exceptions are not handled")
                        delay(100) // 确保输出语句有机会执行
                        println("The first child finished its non cancellable block")
                    }
                }
            }

            // 第二个子协程，在短暂延迟后抛出一个异常
            launch {
                delay(10)
                println("Second child throws an exception")
                throw ArithmeticException() // 抛出一个异常来测试异常处理
            }
        }

        // 等待父协程完成
        job.join()
    }
```

## 异常聚合

- 当协程的多个子协程因为异常而失败时，一般情况下取第一个异常进行处理。在第一异常之后发生的所有其他异常，都将绑定到第一个异常之上。

```kotlin
    @Test
    fun `test exception aggregation`() = runBlocking<Unit> {
        // 定义一个 CoroutineExceptionHandler 来处理未捕获的异常
        val handler = CoroutineExceptionHandler { _, exception ->
            // 输出被捕获的主异常和抑制的异常（如果有）
            println("Caught: $exception, Suppressed: ${exception.suppressed.contentToString()}")
        }

        // 使用 GlobalScope 启动一个顶层协程，并应用异常处理器
        val job = GlobalScope.launch(handler) {
            // 第一个子协程
            launch {
                try {
                    // 模拟长时间运行的任务
                    delay(Long.MAX_VALUE)
                } finally {
                    // 在取消时抛出 ArithmeticException
                    throw ArithmeticException()
                }
            }

            // 第二个子协程
            launch {
                try {
                    // 进行短暂的延迟
                    delay(100)
                } finally {
                    // 在取消时抛出 IOException
                    throw IOException()
                }
            }

            // 第三个子协程
            launch {
                try {
                    // 进行更短暂的延迟
                    delay(10)
                } finally {
                    // 在取消时抛出 IndexOutOfBoundsException
                    throw IndexOutOfBoundsException()
                }
            }
        }

        // 等待父协程完成，确保所有子协程的异常都被处理
        job.join()
    }
```

## Flow-异步流

```kotlin
    // 定义一个名为 simpleFlow 的挂起函数，返回一个 Flow<Int>
    suspend fun simpleFlow() = flow<Int> {
        // 使用 for 循环遍历从 1 到 100 的整数
        for (i in 1..100) {
            delay(1000) // 延迟 1 秒钟，模拟异步任务的延迟
            emit(i)     // 向下游发射当前整数
        }
    }

    // 这是一个测试函数，演示了异步、非阻塞式的行为
    @Test
    fun `test multiple values3`() = runBlocking<Unit> {
        // 启动一个新的协程，不阻塞主线程
        launch {
            // 使用 for 循环打印信息，展示主线程没有被阻塞
            for (k in 1..3) {
                println("I am not blocked $k")
                delay(1500) // 延迟 1.5 秒钟
            }
        }

        // 收集 simpleFlow() 发射的值
        simpleFlow().collect { value ->
            println(value) // 打印每个收集到的值
        }
    }

```

### 冷流

- Flow是一种类似于序列的冷流，flow构建器中的代码直到流被收集的时候才运行

```kotlin
    // 测试函数，演示 Flow 是冷流的概念
    @Test
    fun `test flow is cold`() = runBlocking {
        // 创建一个新的 Flow 实例
        val flow = simpleFlow2()

        // 首次调用 collect，开始收集 Flow 发射的值
        println("Calling collect...")
        flow.collect { value ->
            println(value) // 打印每个收集到的值
        }

        // 再次调用 collect，重新收集 Flow 发射的值
        println("Calling collect again...")
        flow.collect { value ->
            println(value) // 打印每个收集到的值
        }
    }
```

### 流的连续性

- 流的每次单独收集都是按顺序执行的，除非使用特殊操作符
- 从上游到下游每个过渡操作符都会处理每个发射出的值，然后再交给末端操作符。

```kotlin
    // 测试函数，演示流的连续操作
    @Test
    fun `test flow continuation`() = runBlocking {
        // 将 1 到 5 的整数转换成流
        (1..5).asFlow()
            // 过滤流，只保留偶数
            .filter {
                it % 2 == 0
            }
            // 将每个整数转换为字符串
            .map {
                "String $it"
            }
            // 收集流中的每个元素
            .collect { value ->
                println("Collect $value") // 打印收集到的每个元素
            }
    }
```

### 流构建器

- `flowOf`构建器定义了一个发射固定值集的流。

```kotlin
    @Test
    fun `test flow continuation`() = runBlocking {
        // 将 1 到 5 的整数转换成流
        (1..5).asFlow()
            // 过滤流，只保留偶数
            .filter {
                it % 2 == 0
            }
            // 将每个整数转换为字符串
            .map {
                "String $it"
            }
            // 收集流中的每个元素
            .collect { value ->
                println("Collect $value") // 打印收集到的每个元素
            }
    }
```

- 使用`.asFlow()`扩展函数，可以将各种集合与序列转换为流。

```kotlin
    @Test
    fun `test flow builder2`() = runBlocking<Unit> {
        // 创建一个包含三个字符串元素的 Flow："one"、"two" 和 "three"
        flowOf("one", "two", "three")
            // 对 Flow 中的每个元素，应用 1000 毫秒（1 秒）的延迟
            .onEach {
                delay(1000)
            }
            // 收集 Flow 中的每个元素，并将其打印到控制台
            .collect { v ->
                println(v)
            }

        // 将整数范围从 1 到 3 转换为 Flow 并收集每个元素
        (1..3).asFlow()
            .collect {
                println(it)  // 将每个收集到的整数打印到控制台
            }
    }
```

### 流的上下文

- 流的收集总是在调用协程的上下文中发生，流的该属性称为**上下文保存**
- `flow{...}`构建器中的代码必须遵循上下文保存属性，并且不允许从其他上下文中发射（`emit`）
- `flowOn`操作符，该函数用于更改流发射的上下文。

```kotlin
    // 定义一个简单的 Flow，返回 Int 类型的值
    fun simpleFlow5() = flow<Int> {
        // 打印当前线程的名称，表明 Flow 开始执行
        println("Flow started ${Thread.currentThread().name}")
        // 生成从 1 到 3 的整数
        for (i in 1..3) {
            // 延迟 1000 毫秒（1 秒）模拟耗时操作
            delay(1000)
            // 发射（emit）当前整数
            emit(i)
        }
        // 设置 Flow 在默认的调度器（Dispatcher）上运行
    }.flowOn(Dispatchers.Default)

    // 测试函数，使用 JUnit 测试框架
    @Test
    fun `test flow context3`() = runBlocking<Unit> {
        // 收集 Flow 中发射的值
        simpleFlow5().collect { v ->
            // 打印当前线程的名称和收集到的值
            println("Collected $v ${Thread.currentThread().name}")
        }
    }
```

### 启动流

使用`launchIn`替换`collect`，可以在单独的协程中启动流的收集

```kotlin
    // 定义一个 Flow，产生整数 1 到 3
    fun events() = (1..3)
        .asFlow() // 将整数范围转换为 Flow
        .onEach { delay(100) } // 对于每个元素，延迟 100 毫秒
        .flowOn(Dispatchers.Default) // 在默认的调度器（Dispatcher）上执行上述操作

    // 测试函数，使用 JUnit 测试框架
    @Test
    fun `test flow launch`() = runBlocking {
        // 使用 `launchIn` 在新的协程中收集 Flow
        events()
            .onEach { event ->
                // 打印当前收到的元素
                println(event)
            }
            .launchIn(CoroutineScope(Dispatchers.IO)) // 在 IO 调度器上启动协程来收集 Flow
            .join() // 等待协程完成
    }
```

### 流的取消

- 流采用与协程同样的协作取消。像往常一样，流的收集可以是当流在一个可取消的挂起函数（例如delay）中挂起的时候取消

```kotlin
    fun simpleFlow6() = flow<Int> {
        // 生成从 1 到 3 的整数
        for (i in 1..3) {
            delay(1000) // 延迟 1000 毫秒（1 秒），模拟耗时操作
            emit(i) // 发射（emit）当前整数
            println("Emitting $i") // 打印当前发射的值，显示发射过程
        }
    }

    // 测试函数，使用 JUnit 测试框架
    @Test
    fun `test cancel flow`() = runBlocking<Unit> {
        // 使用 withTimeoutOrNull 来取消协程，在超时后返回 null
        withTimeoutOrNull(2500) {
            // 收集 Flow 中发射的值
            simpleFlow6().collect { v ->
                println(v) // 打印收集到的值
            }
        }
        // 打印 "Done" 表示协程已取消或正常完成
        println("Done!")
    }
```

### 流的取消检测

- 为方便起见，流构建器对每个发射值执行附加的`ensureActive`检测以进行取消，这意味着从`flow{...}`发出的繁忙循环是可以取消的。
- 出于性能原因，大多数其他流操作不会自动执行其他取消检测，在协程处于繁忙循环的情况下，必须明确检测是否取消。
- 通过`cancellable`操作符来执行此操作

```kotlin
    fun simpleFlow7() = flow<Int> {
        for (i in 1..5) {
            emit(i)
            println("Emitting $i")
        }
    }

    @Test
    fun `test cancel flow check`() = runBlocking<Unit> {
        // 取消成功，因为emit会suspend函数，有ensureActive检测，所以cancel后会取消
        simpleFlow7().collect { v ->
            println(v)
            if (v == 3) {
                cancel() // 当值等于3时，取消收集过程
                println("cancelled") // 打印 "cancelled" 表示流已取消
            }
        }
        // 取消失败，其他流没有取消检测
//        (1..5).asFlow().collect { v ->
//            println(v)
//            if (v == 3) {
//                cancel() // 当值等于3时，取消收集过程
//            }
//        }

        // 取消成功，因为添加了.cancellable()
        // 使用 cancellable() 方法允许流在收集过程中被取消
        (1..5)
            .asFlow()
            .cancellable()
            .collect { v ->
            println(v) // 打印当前收集到的值
            if (v == 3) {
                cancel() // 当值等于3时，取消收集过程
            }
        }

//        simpleFlow7()
//            .cancellable()
//            .collect { v ->
//                println(v) // 打印当前收集到的值
//                if (v == 3) {
//                    cancel() // 当值等于3时，取消收集过程
//                    println("cancelled") // 打印 "cancelled" 表示流已取消
//                }
//            }
    }
```

## 背压

- `buffer()`，并发运行流中发射元素的代码。

- `conflate()`，合并发射项目，不对每个值进行处理

- `collectLatest()`，取消并重新发射最后一个值
- 当必须更改`CoroutineDispatcher`时，`flowOn`操作符使用了相同的缓冲机制，但是`buffer`函数显式地请求缓冲而**不改变执行上下文**。

```kotlin
    @Test
    fun `test flow back pressure`() = runBlocking {
        // 使用 measureTimeMillis 计算流的收集时间
        val time = measureTimeMillis {
            simpleFlow8() // 调用定义的简单流
//            .flowOn(Dispatchers.Default) // （可选）使用 Default 调度器在后台线程上执行流
//            .buffer(100) // （可选）使用缓冲策略以在生产者和消费者之间提供 100 个值的缓冲区
//            .conflate() // （可选）在下游繁忙时合并流的值，只处理最新的值
                .collectLatest { v -> // 使用 collectLatest 收集流的最新值
                    delay(300) // 模拟耗时操作，延迟 300 毫秒
                    println("Collected $v ${Thread.currentThread().name}") // 打印收集到的值和线程名称
                }
        }
        println("Collected in $time ms") // 打印总共花费的时间
    }
```

