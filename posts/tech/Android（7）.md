---
title : 'Android（7）'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android（7）

- Ordered broadcasts
- Normal broadcasts

## 接受广播系统

发送一条广播，可以被不同的广播接收者所接受，广播接收者收到广播之后，再进行逻辑处理。

## 收发标准广播

![image-20250215164141577](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164141577.png)

### 发送广播的Activity

```kotlin
class BroadStandardActivity : AppCompatActivity() {
    lateinit var standardReceiver: BroadcastReceiver

    private val mBinding: ActivityBroadStandardBinding by lazy {
        ActivityBroadStandardBinding.inflate(layoutInflater)
    }

    @SuppressLint("UnspecifiedRegisterReceiverFlag")
    override fun onStart() {
        super.onStart()
        standardReceiver = StandardReceiver()
        // 创建一个意图过滤器，只处理ACTION_STANDARD的广播
        val filter = IntentFilter(StandardReceiver.ACTION_STANDARD)
        registerReceiver(standardReceiver, filter)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(mBinding.root)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        mBinding.button.setOnClickListener {
            val intent: Intent = Intent(StandardReceiver.ACTION_STANDARD)
            intent.apply {
                putExtra("data", "standard")
            }
            sendBroadcast(intent)
        }
    }

    override fun onStop() {
        super.onStop()
        // 注销接收器
        unregisterReceiver(standardReceiver)
    }
}
```

### 接受广播的Receiver

```kotlin
class StandardReceiver : BroadcastReceiver() {
    companion object {
        const val TAG = "StandardReceiver"
        const val ACTION_STANDARD = "com.zhouguan.learnbroadcasts.action.standard"
    }

    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent != null && intent.action.equals(ACTION_STANDARD)) {
            Log.d(TAG, "onReceive: ${intent.extras?.getString("data")}")
        }
    }
}
```

## 接受有序广播

通过设置接收器的优先级来控制顺序

### 接收器B拦截广播

![image-20250215164130041](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164130041.png)

### 接收器B不拦截广播

![image-20250215164122478](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164122478.png)

### BroadOrderActivity

```kotlin
class BroadOrderActivity : AppCompatActivity() {

    private val mBinding: ActivityBroadOrderBinding by lazy {
        ActivityBroadOrderBinding.inflate(layoutInflater)
    }

    private lateinit var orderedAReceiver: BroadcastReceiver
    private lateinit var orderedBReceiver: BroadcastReceiver

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        enableEdgeToEdge()

        setContentView(mBinding.root)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        mBinding.button.setOnClickListener{
            val intent = Intent(OrderedAReceiver.ACTION_ORDERED)
            sendOrderedBroadcast(intent, null)
        }
    }

    @SuppressLint("UnspecifiedRegisterReceiverFlag")
    override fun onStart() {
        super.onStart()
        orderedAReceiver = OrderedAReceiver()
        orderedBReceiver = OrderedBReceiver()

        val filterA = IntentFilter(OrderedAReceiver.ACTION_ORDERED)
        filterA.priority = 8
        registerReceiver(orderedAReceiver, filterA)

        val filterB = IntentFilter(OrderedAReceiver.ACTION_ORDERED)
        filterB.priority = 9
        registerReceiver(orderedBReceiver, filterB)
    }

    override fun onStop() {
        super.onStop()
        unregisterReceiver(orderedAReceiver)
        unregisterReceiver(orderedBReceiver)
    }
}
```

### OrderedAReceiver

```kotlin
class OrderedAReceiver: BroadcastReceiver()
{
    companion object {
        public const val TAG = "OrderedAReceiver"
        public const val ACTION_ORDERED = "com.zhouguan.learnbroadcasts.receiver.OrderedReceiver"
    }
    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent != null && intent.action.equals(ACTION_ORDERED)){
            Log.d(TAG, "onReceive: receive ordered broadcast")
        }
    }
}
```

### OrderedBReceiver

```kotlin
class OrderedBReceiver: BroadcastReceiver()
{
    companion object {
        public const val TAG = "OrderedBReceiver"
        public const val ACTION_ORDERED = "com.zhouguan.learnbroadcasts.receiver.OrderedReceiver"
    }
    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent != null && intent.action.equals(ACTION_ORDERED)){
            Log.d(TAG, "onReceive: receive ordered broadcast")
//            abortBroadcast()
        }
    }
}
```

## 静态广播（不需要动态注册广播）

![image-20250215164107290](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164107290.png)

首先在AndroidManifest.xml中注册广播

```xml
        <receiver
            android:name=".receiver.ShockReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="com.zhouguan.learnbroadcasts.action.static" />
            </intent-filter>
        </receiver>
```

### BroadStaticActivity (无需注册接收器）

```kotlin
class BroadStaticActivity : AppCompatActivity() {

    // 使用 lazy 延迟初始化视图绑定对象，以优化性能
    private val mBinding: ActivityBroadStaticBinding by lazy {
        ActivityBroadStaticBinding.inflate(
            layoutInflater
        )
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 启用全屏沉浸式体验
        enableEdgeToEdge()

        // 设置当前活动的布局为绑定的根视图
        setContentView(mBinding.root)

        // 设置窗口插图的监听器，用于动态调整视图的内边距以适应系统栏
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        // 为按钮设置点击事件监听器
        mBinding.button.setOnClickListener {
            // 打印日志以记录点击事件
            Log.d("Kennem", "onCreate: send broadcast")

            // 创建一个意图对象
            val intent = Intent()
            // 定义目标广播接收器的完整类名
            val fullName: String = "com.zhouguan.learnbroadcasts.receiver.ShockReceiver"
            // 设置意图的操作标志
            intent.action = ShockReceiver.ACTION_STATIC
            // 创建组件名称以指向特定的广播接收器
            val componentName = ComponentName(this, fullName)
            // 设置意图的组件信息
            intent.component = componentName

            // 发送广播
            sendBroadcast(intent)
        }
    }
}
```

### ShockReceiver

```kotlin
class ShockReceiver : BroadcastReceiver() {

    companion object {
        const val ACTION_STATIC = "com.zhouguan.learnbroadcasts.action.static"
    }

    override fun onReceive(context: Context, intent: Intent) {
        if (intent != null && intent.action.equals(ACTION_STATIC)) {
            Log.d("Kennem", "震动")
            val vibrator = context.getSystemService(Context.VIBRATOR_SERVICE) as android.os.Vibrator
            vibrator.vibrate(1000)
            Log.d("Kennem", "震动结束")
        }
    }
}
```

## 接受系统广播

每一分钟接收一个`Intent.ACTION_TIME_TICK`类型的广播

![image-20250215164050150](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164050150.png)

### SystemMinuteActivity

```kotlin
class SystemMinuteActivity : AppCompatActivity() {
    lateinit var timeReceiver:TimeReceiver

    override fun onStart() {
        super.onStart()
        timeReceiver = TimeReceiver()
        val filter: IntentFilter = IntentFilter(Intent.ACTION_TIME_TICK)
        registerReceiver(timeReceiver, filter)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_system_minute)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }

    override fun onStop() {
        super.onStop()
        unregisterReceiver(timeReceiver)
    }
}
```

### TimeReceiver

```kotlin
class TimeReceiver : BroadcastReceiver() {
    companion object {
        const val TAG = "TimeReceiver"
    }

    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent != null && intent.action == Intent.ACTION_TIME_TICK) {
            Log.d(TAG, "onReceive: ")
        }
    }
}
```

## 接受系统网络变更的广播

### SystemNetworkActivity

```kotlin
class SystemNetworkActivity : AppCompatActivity() {

    companion object{
        const val TAG = "SystemNetworkActivity"
    }

    lateinit var networkReceiver: NetworkReceiver

    override fun onStart() {
        super.onStart()
        networkReceiver =  NetworkReceiver()
        val filter = IntentFilter("android.net.conn.CONNECTIVITY_CHANGE")
        registerReceiver(networkReceiver, filter)
    }

    override fun onStop() {
        super.onStop()
        unregisterReceiver(networkReceiver)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_system_net_work)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

### NetworkReceiver

```kotlin
class NetworkReceiver: BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        if(intent != null){
            val networkInfo = intent.getParcelableExtra<NetworkInfo>("networkInfo")
            val text = String.format("收到一个网络变更广播，网络大类为：%s 网络小类为：%s， 网络状态为：%s",
                networkInfo?.typeName, networkInfo?.subtypeName, networkInfo?.state)

            Log.d("Kennem", "onReceive: $text")
        }
    }
}
```

## 定时管理器AlarmManager

### AlarmActivity

```kotlin
class AlarmActivity : AppCompatActivity() {
    lateinit var alarmReceiver: AlarmReceiver

    private val mBinding: ActivityAlarmBinding by lazy {
        ActivityAlarmBinding.inflate(layoutInflater)
    }

    @SuppressLint("UnspecifiedRegisterReceiverFlag")
    override fun onStart() {
        super.onStart()
        alarmReceiver = AlarmReceiver(applicationContext)
        val fileter = IntentFilter(AlarmReceiver.ALARM_ACTION)
        registerReceiver(alarmReceiver, fileter)
    }

    override fun onStop() {
        super.onStop()
        unregisterReceiver(alarmReceiver)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(mBinding.root)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        mBinding.button.setOnClickListener {
            alarmReceiver.sendAlarm()
        }
    }
}
```

### AlarmReceiver

```kotlin
class AlarmReceiver(val mContext: Context) : BroadcastReceiver() {

    companion object {
        const val ALARM_ACTION = "com.zhouguan.learnbroadcasts.receiver.AlarmReceiver"
    }

    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent != null && intent.action.equals(ALARM_ACTION)) {
            Log.d("Kennem", "收到闹钟广播")
            sendAlarm()
        }
    }

    // 发送闹钟广播
    fun sendAlarm() {
        val intent = Intent(ALARM_ACTION)
        // 延迟意图
        val pendingIntent: PendingIntent = PendingIntent.getBroadcast(
            mContext, 0, intent,
            PendingIntent.FLAG_IMMUTABLE
        )
        val alarmManager = mContext.getSystemService(Context.ALARM_SERVICE) as AlarmManager

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            alarmManager.setAndAllowWhileIdle(AlarmManager.RTC_WAKEUP, 1000, pendingIntent)
        } else {
            alarmManager.set(AlarmManager.RTC_WAKEUP, 1000, pendingIntent)
        }
    }
}
```

### ReturnDesktopActivity

```kotlin
class ReturnDesktopActivity : AppCompatActivity() {
    lateinit var receiver: DesktopReceiver

    @SuppressLint("UnspecifiedRegisterReceiverFlag")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_return_desktop)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        // 注册广播接收器
        receiver = DesktopReceiver(this)
        registerReceiver(receiver, IntentFilter(Intent.ACTION_CLOSE_SYSTEM_DIALOGS))
    }

    override fun onDestroy() {
        super.onDestroy()
        unregisterReceiver(receiver)
    }

    // 定义一个返回到桌面的广播接收器
    class DesktopReceiver(private val activity: ReturnDesktopActivity) : BroadcastReceiver() {
        override fun onReceive(context: Context?, intent: Intent?) {
            if (intent != null && intent.action.equals(Intent.ACTION_CLOSE_SYSTEM_DIALOGS)) {
                val msg = intent.getStringExtra("reason")
                if (!TextUtils.isEmpty(msg) && (msg.equals("homekey") || msg.equals("recentapp"))) {
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O && !activity.isInPictureInPictureMode) {
                        // 创建画中画模式的参数构建器
                        val builder = PictureInPictureParams.Builder()
                        val ratio = Rational(10, 5)
                        builder.setAspectRatio(ratio)
                        activity.enterPictureInPictureMode(builder.build())
                    }
                }
            }
        }
    }
}
```

## 横竖屏切换

配置变更的豁免情况

![image-20250215164023897](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215164023897.png)