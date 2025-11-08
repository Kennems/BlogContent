---
title : 'Kotlin协程(3)'
date : 2024-08-13T22:30:13+08:00
lastmod: 2024-08-13T22:20:13+08:00
description : "Kotlin协程(3)" 
image : img/cat.jpg
draft : false    
categories : ["Kotlin学习笔记"]
tags : ["Kotlin"]
---
# Kotlin协程(3)

## 操作符

### 过渡流操作符

- 可以使用操作符转换符，就像使用集合与序列一样
- 过渡操作符应用于上游流，并返回下游流。
- 这些操作符也是冷操作符，就像流一样。这类操作符本身不是挂起函数。
- 它运行的速度很快，返回新的转换流的定义。

### transform()

```kotlin
    // 定义一个挂起函数，模拟发送请求并返回响应
    suspend fun performRequest(request: Int): String {
        delay(1000) // 模拟网络延迟，延迟 1000 毫秒（1 秒）
        return "response $request" // 返回一个响应字符串
    }

    // 测试函数，使用 JUnit 测试框架
    @Test
    fun `test transform flow operator`() = runBlocking {
        // 将整数范围转换为 Flow
        (1..3).asFlow()
            .transform { request -> // 使用 transform 运算符转换流的元素
                emit("Making request $request") // 发射一个字符串，表示正在发送请求
                emit(performRequest(request)) // 发射 performRequest 的返回值
            }
            .collect { v -> // 收集流的元素
                println(v) // 打印收集到的元素
            }
    }
```

### take()

```kotlin
// 定义一个简单的 Flow，产生 Int 类型的值
fun numbers() = flow<Int> {
    try {
        emit(1) // 发射第一个整数 1
        emit(2) // 发射第二个整数 2
        println("This line will not execute") // 这一行代码不会被执行，因为 collect 操作会提前中止 Flow
        emit(3) // 发射第三个整数 3（不会执行）
    } finally {
        // 在 Flow 被收集完成或取消时执行
        println("Finally in numbers") // 打印 "Finally in numbers"，表示 finally 块的执行
    }
}

// 测试函数，使用 JUnit 测试框架
@Test
fun `test limit length operator`() = runBlocking {
    // 调用 numbers 函数，限制收集的元素数量为 2
    numbers()
        .take(2) // 使用 take 运算符，只收集前 2 个元素
        .collect { v -> // 收集 Flow 中的元素
            println(v) // 打印每个收集到的元素
        }
}
```

### 末端操作符

- 末端操作符是在流上用于启动流收集的挂起函数。collect是最基础的末端操作符，但是还有另外一些更方便使用的末端操作符
  - 转化为各种集合，例如`toList`和`toSet`
  - 获取第一个（first）值与确保流发射单个（single）值的操作符
  - 使用reduce与fold将流规约到单个值

### 组合多个流

- 就像`Kotlin`标准库中的`Sequence.zip`拓展函数一样，流拥有一个zip操作符用于组合两个流中的相关值

```kotlin
    @Test
    fun `test zip2`() = runBlocking {
        // 创建一个整数流，发射 1 到 3 的数字，每个数字发射之间延迟 300 毫秒
        val numbs = (1..3).asFlow().onEach {
            delay(300) // 模拟延迟
        }

        // 创建一个字符串流，发射 "one"、"two" 和 "three" 的字符串，每个字符串发射之间延迟 400 毫秒
        val strs = flowOf("one", "two", "three").onEach {
            delay(400) // 模拟延迟
        }

        // 记录测试开始时间
        val startTime = System.currentTimeMillis()

        // 使用 zip 操作符将两个流的元素配对
        numbs.zip(strs) { a, b -> "$a -> $b" } // 将 numbs 和 strs 的每对元素组合成字符串
            .collect { v -> // 收集配对后的元素
                // 打印配对后的元素和从测试开始到当前时间的时间差
                println("$v, consume time : ${System.currentTimeMillis() - startTime} ms ")
            }
    }
```

### 展平流

- 流表示异步接收的值序列，所以很容易遇到这样的情况：每个值都会触发对另一个值序列的请求，然而，由于流具有异步的性质，因此需要不同的展平模式，为此，存在一系列的流展平操作符：
  - `flatMapConcat` 连接模式
  - `flatMapMerge` 合并模式
  - `flatMapLatest` 最新展平模式

```kotlin
// 使用 flatMapConcat 操作符的测试函数
@Test
fun `test flatMapConcat`() = runBlocking<Unit> {
    val startTime = System.currentTimeMillis() // 记录开始时间

    // 创建一个流，包含数字 1 到 3
    (1..3)
        .asFlow() // 将数字转换为流
        .onEach { delay(100) } // 在每个元素上施加 100 毫秒的延迟
        // 使用 flatMapConcat 将每个元素转换为新的流并串联
        .flatMapConcat { requestFlow(it) }
        .collect { v ->
            // 收集并打印每个元素的值以及从开始到现在的消耗时间
            println("$v, consume time : ${System.currentTimeMillis() - startTime} ms ")
        }
}

// 使用 flatMapMerge 操作符的测试函数
@Test
fun `test flatMapMerge`() = runBlocking<Unit> {
    val startTime = System.currentTimeMillis() // 记录开始时间

    // 创建一个流，包含数字 1 到 3
    (1..3)
        .asFlow() // 将数字转换为流
        .onEach { delay(100) } // 在每个元素上施加 100 毫秒的延迟
        // 使用 flatMapMerge 将每个元素转换为新的流并并发执行
        .flatMapMerge { requestFlow(it) }
        .collect { v ->
            // 收集并打印每个元素的值以及从开始到现在的消耗时间
            println("$v, consume time : ${System.currentTimeMillis() - startTime} ms ")
        }
}

// 使用 flatMapLatest 操作符的测试函数
@Test
fun `test flatMapLatest`() = runBlocking<Unit> {
    val startTime = System.currentTimeMillis() // 记录开始时间

    // 创建一个流，包含数字 1 到 3
    (1..3)
        .asFlow() // 将数字转换为流
        .onEach { delay(100) } // 在每个元素上施加 100 毫秒的延迟
        // 使用 flatMapLatest 将每个元素转换为新的流，仅保留最新流
        .flatMapLatest { requestFlow(it) }
        .collect { v ->
            // 收集并打印每个元素的值以及从开始到现在的消耗时间
            println("$v, consume time : ${System.currentTimeMillis() - startTime} ms ")
        }
}
```

### 流的异常处理

- 当运算符中的发射器或代码抛出异常时，有几种处理异常的方法：
  - try/catch块
  - catch函数

```kotlin
    /**
     * 测试用例 `test exception1`：
     * 使用 runBlocking 启动协程，收集 simpleFlow 的值。
     * 如果值大于 1，则抛出异常并捕获。
     */
    @Test
    fun `test exception1`() = runBlocking<Unit> {
        try {
            simpleFlow().collect { v ->
                println(v)
                check(v <= 1) { "Collected $v" }
            }
        } catch (e: Throwable) {
            println("Caught $e")
        }
    }

    /**
     * 测试用例 `test exception2`：
     * 创建一个流，发射一个值后抛出异常。
     * 使用 catch 操作符处理异常。
     */
    @Test
    fun `test exception2`() = runBlocking<Unit> {
        flow {
            emit(1)
            throw ArithmeticException("Div 0")
        }
            .catch { e: Throwable ->
                println("Caught: $e")
            }
            .flowOn(Dispatchers.IO)
            .collect {
                println(it)
            }
    }
```

### 流的完成

- 当流收集完成时（普通情况或异常情况），它可能需要执行一个动作
  - 命令是`finally`块
  - `onCompletion`声明式处理

```kotlin
    // 定义一个函数，返回1到3的值作为 Flow
    fun simpleFlow2() = (1..3).asFlow()

    @Test
// 测试在 finally 块中完成流的处理
    fun `test flow complete in finally`() = runBlocking {
        try {
            // 收集 simpleFlow2 流中的每个值
            simpleFlow2().collect {
                println(it)
            }
        } finally {
            // 无论流的收集过程是否成功，这条消息都会在最后打印
            println("Done!")
        }
    }

    // 定义一个函数，返回一个 Flow<Int> 类型的流
    fun simpleFlow3() = flow<Int> {
        // 发射（emit）第一个值
        emit(1)
        // 发射一个异常来中断流
        throw RuntimeException()
    }

    @Test
// 测试使用 onCompletion 操作符处理流完成
    fun `test flow complete in onCompletion`() = runBlocking {
        simpleFlow3()
            // 在流完成时调用的操作符
            .onCompletion { exception ->
                if (exception != null) {
                    // 如果有异常，打印流以异常方式完成
                    println("Flow completed exceptionally")
                }
            }
            // 捕获流中的异常
            .catch { exception ->
                println("Caught $exception")
            }
            // 收集 simpleFlow3 流中的每个值
            .collect {
                println(it)
            }
    }
```

## 通道-多路复用-并发安全

### Channel

- Channel是一个并发安全的队列，它可以用来连接协程，实现不同协程的通信。

```kotlin
    @Test
    fun `test know channel`() = runBlocking<Unit> {
        // 创建一个整数类型的通道，用于在不同的协程之间传递数据
        val channel = Channel<Int>()

        // 在全局范围内启动一个生产者协程，负责向通道发送数据
        val producer = GlobalScope.launch {
            var i = 0
            while (true) {
                // 每次发送前暂停1秒钟
                delay(1000)
                // 递增计数器并将值发送到通道
                channel.send(++i)
                println("send $i")
            }
        }

        // 在全局范围内启动一个消费者协程，负责从通道接收数据
        val consumer = GlobalScope.launch {
            while (true) {
                // 接收通道中的数据
                val element = channel.receive()
                println("receive $element")
            }
        }

        // 等待生产者和消费者协程结束
        joinAll(producer, consumer)
    }
```

### Channel的容量

- Channel实际上就是一个队列，队列中一定存在缓冲区，一旦这个缓冲区满了，并且一直没有人调用receive并取走函数，send就需要挂起。故意让接收端的节奏放慢，发现send总是会挂起，直到receive之后才会继续往下执行。

### 迭代channel

### produce 与 actor

- 构造生产者与消费者的便捷方法
- 可以通过produce方法启动一个生产者协程，并返回一个`ReceiveChannel`， 其他协程就可以用这个Channel来接收数据了。反之，可以用actor启动一个消费者协程。

```kotlin
    @OptIn(DelicateCoroutinesApi::class)
    @Test
    fun `test fast consumer channel`() = runBlocking {
        // 创建一个发送通道，它通过actor协程进行数据接收和处理
        val sendChannel: SendChannel<Int> = GlobalScope.actor {
            while (true) {
                // 接收并处理通道中的数据
                val element = receive()
                println(element)
            }
        }

        // 在全局范围内启动一个生产者协程，负责向通道发送数据
        val producer = GlobalScope.launch {
            for (i in 0..3) {
                // 向通道发送数据
                sendChannel.send(i)
            }
        }

        // 等待生产者协程完成
        producer.join()
    }
```

### Channel的关闭

- produce和actor返回的Channel都会随着对应的协程执行完毕而关闭，也正是这样，Channel才被称为**热数据流**。
- 对于Channel，如果我们调用了它的close方法，它会立即停止接收新元素，也就是说这是它的`isClosedForSend`会立即返回true。而由于Channel缓冲区的存在，这时候可能还有一些元素没有被处理完，因此要等所有的元素都被读取之后`isClosedForSend`才会返回true。
- Channel的生命周期最好由主导方来维护，建议由主导的一方实现关闭。

```kotlin
    @Test
    fun `test close channel`() = runBlocking {
        // 创建一个无界限的整数类型的通道，用于在不同的协程之间传递数据
        val channel = Channel<Int>(Channel.UNLIMITED)

        // 在全局范围内启动一个生产者协程，负责向通道发送数据
        val producer = GlobalScope.launch {
            List(3) {
                channel.send(it)
                println("send $it")
            }
            // 发送完成后关闭通道
            channel.close()
            // 打印通道的关闭状态
            println(
                """close channel.
            | -ClosedForSend : ${channel.isClosedForSend}
            | -ClosedForReceive: ${channel.isClosedForReceive}
        """.trimMargin()
            )
        }

        // 在全局范围内启动一个消费者协程，负责从通道接收数据
        val consumer = GlobalScope.launch {
            for (element in channel) {
                println("receive $element")
                delay(1000)
            }
            // 消费完成后打印通道的关闭状态
            println(
                """close channel.
            | -ClosedForSend : ${channel.isClosedForSend}
            | -ClosedForReceive: ${channel.isClosedForReceive}
        """.trimMargin()
            )
        }

        // 等待生产者和消费者协程结束
        joinAll(producer, consumer)
    }
```

### BroadcastChannel

发送端和接收端在`Channel`中存在一对多的情形，从数据处理本身来讲，虽然有多个接收端，但是同一个元素只会被一个接收端读到。广播则不然，多个接收端不存在互斥行为。

```kotlin
    // 选择使用过时和精细的协程 API
    @OptIn(ObsoleteCoroutinesApi::class, DelicateCoroutinesApi::class)
    @Test
    fun `test broadcastChannel`() = runBlocking<Unit> {
        // 创建一个新的通道，使用无限缓冲区容量
        val channel = Channel<Int>()

        // 将通道转换为广播通道，可以有多个订阅者
        val broadcastChannel = channel.broadcast(3)

        // 生产者协程，将一系列整数发送到广播通道
        val producer = GlobalScope.launch {
            // 发送 3 个整数到广播通道，每次发送之间有延迟
            List(3) {
                delay(100)
                broadcastChannel.send(it)
            }
            // 在发送完所有整数后关闭广播通道
            broadcastChannel.close()
        }

        // 启动 3 个协程，每个协程订阅广播通道
        List(3) { index: Int ->
            GlobalScope.launch {
                // 每个协程打开广播通道的一个订阅
                val receiveChannel = broadcastChannel.openSubscription()
                // 迭代接收的广播通道中的值
                for (i in receiveChannel) {
                    println("[#$index] received : $i")
                }
            }
        }.joinAll() // 等待所有协程完成
    }
```

### 多路复用

- 数据通信系统或计算机网络系统中，传输媒体的带宽或容量往往会大于传输单一信号的需求，为了有效地利用通信线路，希望一个信道同时传输多路信号，这就是多路复用技术（Multiplexing）

### 复用多个await

- 两个API分别从网络和本地缓存获取数据，期望哪个先返回就先用哪个做展示

```kotlin
    @Test
    fun `test select channel`() = runBlocking<Unit> {
        // 创建一个包含两个整型通道的列表
        val channels = listOf(Channel<Int>(), Channel<Int>())

        // 第一个全局协程，在延迟100毫秒后向第一个通道发送200
        GlobalScope.launch {
            delay(100)
            channels[0].send(200)
        }

        // 第二个全局协程，在延迟50毫秒后向第二个通道发送100
        GlobalScope.launch {
            delay(50)
            channels[1].send(100)
        }

        // 使用select表达式从通道中接收一个值
        val result = select<Int?> {
            // 对每个通道应用onReceive子句
            channels.forEach { channel ->
                channel.onReceive { value ->
                    value  // 返回接收到的值
                }
            }
        }

        // 打印从通道中接收到的值
        println(result)
    }
```

### SelectClause

- `SelectClause0` : 对应事件没有返回值，例如 join 没有返回值，那么`onJoin`就是`SelectClauseN`类型。使用时，`onJoin`的参数是一个无参函数。
- `SelectClause1`：对应事件有返回值，前面的`onAwait`和`onReceive`都是此类情况。
- `SelectClause2`：对应事件有返回值，此外还需要一个额外的参数，例如`Channel.onSend`有两个参数，第一个是Channel数据类型的值，表示即将发送的值，第二个是发送成功时的回调参数。

- 如果想要确认挂起函数是否支持select，只需要查看其**是否存在对应的`SelectClauseN`类型**可回调即可。

### 使用Flow实现多路复用

```kotlin
    @Test
    fun `test select flow`() = runBlocking<Unit> {
        // 定义用户名
        val name = "guest"
        // 在协程作用域内运行
        coroutineScope {
            // 创建一个包含两个函数引用的列表
            listOf(::getUserFromLocal, ::getUserFromRemote)
                .map { function ->
                    // 调用每个函数，传入用户名和当前协程作用域
                    function.call(name, this)
                }
                .map { deferred ->
                    // 将每个 Deferred 转换为 Flow，并发射其结果
                    flow { emit(deferred.await()) }
                }
                // 合并所有的 Flow
                .merge()
                // 收集结果并打印每个用户对象
                .collect { user ->
                    println(user)
                }
        }
    }

```

### 协程的并发工具

- 除了在线程中常用的解决并发问题的手段之外，协程框架也提供了一些安全的工具，包括：
  - `Channel`：并发安全的消息通道
  - `Mutex`：轻量级锁，它的lock和unlock从语义上与线程锁比较类似，之所以轻量是因为它在获取不到锁时不会阻塞线程，而是挂起等待锁的释放
  - `Semaphore`：轻量级信号量，信号量可以有多个，协程在获取到信号量后即可执行并发操作。当Semaphore的参数为1时，效果等价于Mutex。

```kotlin
    @Test
    fun `test not concurrent tools`() = runBlocking {
        // 初始化计数器
        var count = 0
        // 初始化一个互斥锁
        var mutex = Mutex()
        // 创建 1000 个协程，每个协程都尝试增加计数器
        List(1000) {
            GlobalScope.launch {
                // 在互斥锁保护下增加计数器，确保线程安全
                mutex.withLock {
                    count += 1
                }
            }
        }.joinAll() // 等待所有协程完成
        // 打印计数器的最终值
        println(count)
    }

    @Test
    fun `test not concurrent tools2`() = runBlocking {
        // 初始化计数器
        var count = 0
        // 初始化一个信号量，允许同时只有一个协程进入临界区
        var semaphore = Semaphore(1)
        // 创建 1000 个协程，每个协程都尝试增加计数器
        List(1000) {
            GlobalScope.launch {
                // 在信号量保护下增加计数器，确保线程安全
                semaphore.withPermit {
                    count += 1
                }
            }
        }.joinAll() // 等待所有协程完成
        // 打印计数器的最终值
        println(count)
    }
```

### 避免访问外部可变状态

- 编写函数时要求它不得访问外部状态，只能基于参数做运算，通过返回值提供运算结果

```kotlin
    @Test
    fun `test avoid access outer variable`() = runBlocking {
        var count = 0
        var result = count + List(1000){
            GlobalScope.async { 1 }
        }.map { it.await() }.sum()
        println(result)
    }
```