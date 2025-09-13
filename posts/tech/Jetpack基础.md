---
title : 'Jetpack基础'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Jetpack基础

## `ViewModel`

```kotlin
class MainViewModel(countReserved: Int) : ViewModel() {
    val userLiveData = MutableLiveData<User>()
    private val userIdLiveData = MutableLiveData<String>()

    val userName: LiveData<String> = userLiveData.map { user ->
        "${user.firstName} ${user.lastName}"
    }
    val user: LiveData<User> = userIdLiveData.switchMap { userId ->
        Repository.getUser(userId)
    }

    val count: LiveData<Int>
        get() = _count

    val _count = MutableLiveData<Int>()

    init {
        _count.value = countReserved
    }

    fun plusOne() {
        val count = _count.value ?: 0
        _count.value = count + 1
    }

    fun clear() {
        _count.value = 0
    }

//    fun getUser(userId: String): LiveData<User> {
//        return Repository.getUser(userId)
//    }

    fun getUser(userId: String) {
        userIdLiveData.value = userId
    }
}
```

## `Lifecycles`

可以在`Observer`中查看到对应`MainActivity`声明周期的变化

![image-20250215172015394](C:\Users\Acer\AppData\Roaming\Typora\typora-user-images\image-20250215172015394.png)

```kotlin
/**
 * MyObserver 是一个实现 LifecycleObserver 接口的类，用于观察生命周期变化
 * 并响应生命周期事件，如 ON_START 和 ON_STOP。它可以用于 Activity 或 Fragment
 * 等组件中来监听生命周期事件并执行相应操作。
 *
 * @param lifecycle Activity 或其他组件的生命周期对象，用于注册和管理生命周期事件。
 */
class MyObserver(val lifecycle: Lifecycle) : LifecycleObserver {

    companion object {
        // 定义一个静态常量 TAG，用于日志输出时标识当前类
        private const val TAG = "MyObserver"
    }

    /**
     * 当生命周期事件为 ON_START 时调用，表示组件进入前台并开始交互。
     * 这个方法会在组件被启动并可见时执行。
     */
    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    fun activityStart() {
        // 使用 Log.d 输出日志信息，标识组件已经开始
        Log.d(TAG, "activityStart: ")
    }

    /**
     * 当生命周期事件为 ON_STOP 时调用，表示组件离开前台并进入后台。
     * 这个方法会在组件不再可见或被销毁前调用。
     */
    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    fun activityStop() {
        // 使用 Log.d 输出日志信息，标识组件已经停止
        Log.d(TAG, "activityStop: ")
    }
}
```

在activity中使用：

```kotlin
        lifecycle.addObserver(MyObserver(lifecycle))
```

### Observer 使用

```kotlin
    private void handleNotifyClosed() {
        if (!Utils.areNotificationsEnabled(this)) {
            Observer<Boolean> notifyObserver = new Observer<Boolean>() {
                @Override
                public void onChanged(Boolean isPlaying) {
                    if (isPlaying && NotifyClosedDialog.Companion.canShow()) {
                        Activity topActivity = IMainProcessApplicationHelper.getInstance().getTopActivity();
                        // 锁屏、闹钟、兜底广告页面不展示通知弹窗
                        if (ActivityUtils.curActivityHasAttr(topActivity, R.attr.dialog_background_top_round_corner_attr)) {
                            NotifyClosedDialog notifyClosedDialog = new NotifyClosedDialog();
                            notifyClosedDialog.show(((FragmentActivity) topActivity).getSupportFragmentManager(), "notify");
                            // 弹出一次后移除观察者，防止再次弹出
                            IPlayCenter.getInstance().isPlayingLiveData().removeObserver(this);
                        }
                    }
                }
            };
            IPlayCenter.getInstance().isPlayingLiveData().observeForever(notifyObserver);
        }
    }
```

## `WorkManager`

### 导入依赖

```kotlin
    implementation("androidx.work:work-runtime:2.9.1")
```

### 编写SimpleWorker