---
title : 'Lopper & Handler'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Lopper & Handler

![image-20250215164359613](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164359613.png)

**Looper** 是 Android 中用于线程消息循环的核心组件，它让线程可以接收和处理消息（`Message`）或任务（`Runnable`）。它通过一个消息队列（`MessageQueue`）来管理这些消息和任务，线程从中取出消息逐一处理。 在Android中：

- 主线程（UI线程）默认会配置一个**Looper**和**MessageQueue**，主线程可以更新UI和处理事件
- 工作线程（非主线程）默认没有Looper，所以如果需要在子线程中处理消息，就需要手动创建一个Looper。

### Looper 的核心概念

1. **MessageQueue**：存储待处理的消息。
2. **Handler**：用来发送和处理消息，绑定到某个线程的 **Looper**。
3. **Looper**：负责不断轮询消息队列，并将消息交给相应的 **Handler** 处理。

简单来说，**Looper** 就是线程消息循环的核心工具，让线程能够持续处理消息或任务。

```java
import android.os.Handler;
import android.os.Looper;
import android.os.Message;

public class LooperExample {

    public static void main(String[] args) {
        // 启动子线程
        Thread thread = new Thread(() -> {
            // 准备 Looper
            Looper.prepare();

            // 创建 Handler，用于接收和处理消息
            Handler handler = new Handler(Looper.myLooper()) {
                @Override
                public void handleMessage(Message msg) {
                    // 处理消息
                    switch (msg.what) {
                        case 1:
                            System.out.println("Received message: " + msg.obj);
                            break;
                        default:
                            System.out.println("Unknown message");
                            break;
                    }
                }
            };

            // 发送消息到 Handler
            Message message = Message.obtain();
            message.what = 1;
            message.obj = "Hello from the main thread!";
            handler.sendMessage(message);

            // 开启消息循环
            Looper.loop();

            // 注意：Looper.loop() 会阻塞在这里，直到 Looper 被退出。
        });

        thread.start();
    }
}
```

### 关键方法

1. `Looper.prepare()`
   - 初始化当前线程的 Looper，必须在该线程调用 `Looper.loop()` 前调用。
   - 一个线程只能调用一次。
2. `Looper.loop()`
   - 开启消息循环，持续从消息队列中取消息进行处理。
   - 调用这个方法后线程会进入一个“无限循环”的状态，直到手动退出 Looper。
3. `Looper.myLooper()`
   - 获取当前线程的 Looper 对象。
4. `Looper.getMainLooper()`
   - 获取主线程的 Looper 对象。
5. `Looper.quit()`
   - 退出 Looper，停止消息循环。