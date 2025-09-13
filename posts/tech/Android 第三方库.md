---
title : 'Android 第三方库'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android 第三方库

## ARouter

ARouter 是阿里巴巴推出的 Android 路由框架，用于**简化页面跳转**、**组件通信**和**跨模块开发**。

### **1. 添加依赖**

首先，在项目中引入 ARouter 的相关依赖。

### **1.1 在项目级 `build.gradle` 中添加 ARouter Maven 仓库**

```kotlin
buildscript {
    repositories {
        mavenCentral()
        google()
        maven { url '<https://jitpack.io>' }
    }
}
```

1.2 在模块级 `build.gradle` 中添加依赖

```kotlin
android {
    defaultConfig {
        javaCompileOptions {
            annotationProcessorOptions {
                arguments = [AROUTER_MODULE_NAME: project.getName()]
            }
        }
    }
}

dependencies {
    // ARouter 核心库
    implementation 'com.alibaba:arouter-api:1.5.2'

    // ARouter 注解处理器
    kapt 'com.alibaba:arouter-compiler:1.5.2'
    annotationProcessor 'com.alibaba:arouter-compiler:1.5.2'
}
```

同步项目以确保依赖正确导入。

### **2. 初始化 ARouter**

在 `Application` 类中初始化 ARouter。

### **2.1 创建 `MyApplication` 类**

```kotlin
class MyApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        // 调试模式必须在初始化之前开启
        if (BuildConfig.DEBUG) { 
            ARouter.openLog() // 打印日志
            ARouter.openDebug() // 开启调试模式（如果在InstantRun模式下运行，必须开启）
        }

        // 初始化 ARouter
        ARouter.init(this)
    }
}
```

### **2.2 注册 `Application`**

在 `AndroidManifest.xml` 中注册自定义的 `MyApplication`：

```kotlin
<application
    android:name=".MyApplication"
    ... >
</application>
```

### **3. 页面跳转的基本用法**

### **3.1 声明路由路径**

使用 `@Route` 注解为目标 Activity 声明路由路径。

```kotlin
@Route(path = "/example/first")
class FirstActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_first)

        // 获取传递的参数
        val name = intent.getStringExtra("name")
        val age = intent.getIntExtra("age", 0)
    }
}
```

### **3.2 跳转到指定页面**

使用 ARouter 调用页面跳转。

```kotlin
ARouter.getInstance()
    .build("/example/first")
    .withString("name", "Kennem")
    .withInt("age", 25)
    .navigation()
```

### **4. 跨模块跳转**

在组件化开发中，ARouter 支持跨模块跳转。各模块只需依赖 ARouter 即可。

### **4.1 在每个模块中添加依赖**

在各模块的 `build.gradle` 中添加 ARouter 依赖。

```kotlin
implementation 'com.alibaba:arouter-api:1.5.2'
kapt 'com.alibaba:arouter-compiler:1.5.2'
annotationProcessor 'com.alibaba:arouter-compiler:1.5.2'
```

### **4.2 使用路由跳转**

路由跳转的方式与普通用法相同，不需要关心目标模块的实现细节。

### **5. 路由拦截器**

ARouter 提供了拦截器功能，用于在页面跳转前对请求进行拦截处理（如登录校验）。

### **5.1 定义拦截器**

实现 `IInterceptor` 接口并添加 `@Interceptor` 注解。

```kotlin
@Interceptor(priority = 1, name = "LoginInterceptor")
class LoginInterceptor : IInterceptor {

    override fun process(postcard: Postcard, callback: InterceptorCallback) {
        // 检查是否需要登录
        if (postcard.path == "/example/protected") {
            val isLoggedIn = checkLoginStatus()
            if (!isLoggedIn) {
                // 终止跳转并跳转到登录页面
                ARouter.getInstance().build("/example/login").navigation()
                callback.onInterrupt(null)
                return
            }
        }
        callback.onContinue(postcard) // 继续路由
    }

    override fun init(context: Context) {
        // 初始化操作
    }

    private fun checkLoginStatus(): Boolean {
        // 模拟登录检查
        return false
    }
}
```

### **5.2 注册拦截器**

拦截器无需手动注册，ARouter 会自动发现并加载。

### **6. 服务管理**

ARouter 提供了服务管理功能，用于跨模块调用服务。

### **6.1 定义服务接口**

创建一个公共接口模块并定义服务接口。

```kotlin
interface HelloService : IProvider {
    fun sayHello(name: String): String
}
```

### **6.2 实现服务**

在具体模块中实现服务。

```kotlin
@Route(path = "/service/hello")
class HelloServiceImpl : HelloService {

    override fun init(context: Context?) {
        // 初始化操作
    }

    override fun sayHello(name: String): String {
        return "Hello, $name!"
    }
}
```

### **6.3 调用服务**

在其他模块中通过 ARouter 获取服务实例并调用方法。

```kotlin
val helloService = ARouter.getInstance().navigation(HelloService::class.java)
val result = helloService.sayHello("Kennem")
Log.d("ARouter", result)
```

### **7. 注解说明**

ARouter 提供的核心注解包括：

1. @Route

   ：用于标记路由目标（如 Activity、Fragment 或 Service）。

   - `path`：必填，表示路由路径。
   - `group`：可选，路由分组。

- `path`：必填，表示路由路径。
- `group`：可选，路由分组。

1. **@Autowired**：用于自动注入字段。

```kotlin
@Route(path = "/example/second")
class SecondActivity : AppCompatActivity() {

    @Autowired
    lateinit var name: String

    @Autowired
    var age: Int = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_second)

        ARouter.getInstance().inject(this) // 自动注入
        Log.d("ARouter", "Name: $name, Age: $age")
    }
}
```

### **8. 混淆配置**

为了避免代码被混淆，需在 `proguard-rules.pro` 文件中添加以下规则：

```kotlin
-keep class com.alibaba.android.arouter.** { *; }
-keep @com.alibaba.android.arouter.facade.annotation.* class * { *; }
-keepclassmembers class * {
    @com.alibaba.android.arouter.facade.annotation.Autowired <fields>;
}
-keep interface * extends com.alibaba.android.arouter.facade.template.IProvider
```

### **9. 常见问题**

1. **未初始化 ARouter**

   确保在 Application 中调用了 `ARouter.init(this)`。

确保在 Application 中调用了 `ARouter.init(this)`。

1. **路由找不到**

   检查 `@Route` 的路径是否正确，是否在模块中执行了编译。

检查 `@Route` 的路径是否正确，是否在模块中执行了编译。

1. **跨模块无法跳转**

   确保各模块都正确依赖了 ARouter，并在编译期生成了相应的代码。

确保各模块都正确依赖了 ARouter，并在编译期生成了相应的代码。

```kotlin
        ARouter.getInstance()
            .build(path)
            .withTransition(R.anim.now_playing_activity_in, R.anim.anim_nothing)
            .withString("url", song.getInsightSong()?.webUrl)
            .withBoolean("resume", true)
            .withFlags(Intent.FLAG_ACTIVITY_REORDER_TO_FRONT)
            .navigation(context)
```

## Hilt

Hilt 是 Android 的依赖项注入库，可减少在项目中执行手动依赖项注入的样板代码。执行[手动依赖项注入](https://developer.android.google.cn/training/dependency-injection/manual?hl=zh-cn)要求您手动构造每个类及其依赖项，并借助容器重复使用和管理依赖项。

### **1. 添加依赖**

在项目的 `build.gradle` 文件中添加 Hilt 所需的依赖。

```
// 在项目级 build.gradle 中添加
classpath 'com.google.dagger:hilt-android-gradle-plugin:2.50'

// 在应用级 build.gradle 中添加
plugins {
    id 'com.android.application'
    id 'kotlin-kapt'
    id 'dagger.hilt.android.plugin'
}

dependencies {
    implementation 'com.google.dagger:hilt-android:2.50'
    kapt 'com.google.dagger:hilt-android-compiler:2.50'

    // 如果需要使用 ViewModel
    implementation 'androidx.hilt:hilt-lifecycle-viewmodel:1.0.0-alpha03'
    kapt 'androidx.hilt:hilt-compiler:1.0.0'
}
```

同步项目以确保依赖已正确添加。

### **2. 配置应用程序类**

在应用程序类上添加 `@HiltAndroidApp` 注解，使 Hilt 初始化依赖注入。

```kotlin
@HiltAndroidApp
class MyApplication : Application() {
    // 可以在这里初始化全局依赖
}
```

### **3. 使用 Hilt 提供依赖**

### **a. 创建模块提供依赖**

使用 `@Module` 和 `@InstallIn` 注解来提供依赖。

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object AppModule {

    @Provides
    @Singleton
    fun provideRetrofit(): Retrofit {
        return Retrofit.Builder()
            .baseUrl("<https://api.example.com/>")
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }

    @Provides
    @Singleton
    fun provideApiService(retrofit: Retrofit): ApiService {
        return retrofit.create(ApiService::class.java)
    }
}
```

### **b. 在 Activity 或 Fragment 中注入依赖**

使用 `@AndroidEntryPoint` 注解标记 Activity 或 Fragment，Hilt 会自动注入依赖。

```kotlin
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {

    @Inject
    lateinit var apiService: ApiService

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 使用注入的依赖
        apiService.someMethod()
    }
}
```

### **c. 在 ViewModel 中使用 Hilt**

使用 Hilt 提供依赖给 ViewModel。

```kotlin
@HiltViewModel
class MyViewModel @Inject constructor(
    private val apiService: ApiService
) : ViewModel() {

    fun fetchData() {
        // 使用 apiService
    }
}
```

在 Activity 或 Fragment 中使用 ViewModel：

```kotlin
@AndroidEntryPoint
class MyFragment : Fragment() {

    private val viewModel: MyViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        viewModel.fetchData()
    }
}
```

### **4. 常见作用域**

Hilt 提供多种作用域，常见的有：

- **SingletonComponent**：应用程序生命周期内单例。
- **ActivityComponent**：Activity 生命周期内单例。
- **FragmentComponent**：Fragment 生命周期内单例。
- **ViewModelComponent**：ViewModel 生命周期内单例。

根据实际需求选择合适的作用域。

### **5. 测试支持**

Hilt 提供专门的测试支持，通过使用 `@HiltAndroidTest` 和 `@UninstallModules` 进行测试。

```kotlin
@HiltAndroidTest
class MyTest {

    @get:Rule
    var hiltRule = HiltAndroidRule(this)

    @Inject
    lateinit var apiService: ApiService

    @Before
    fun setUp() {
        hiltRule.inject()
    }

    @Test
    fun testSomething() {
        // 测试逻辑
    }
}
```

### **6. Hilt 注解概览**

- **@HiltAndroidApp**：初始化 Hilt。
- **@AndroidEntryPoint**：标记注入目标，如 Activity、Fragment。
- **@Inject**：用于注入字段或构造函数。
- **@Provides**：用于提供依赖实例。
- **@Module**：用于创建提供依赖的模块。
- **@InstallIn**：指定模块的作用域。
- **@Singleton**：标记单例依赖。

# Room

### Room使用

### 导入依赖

```kotlin
plugins {
    id("kotlin-kapt")
}

dependencies {
    implementation("androidx.room:room-runtime:2.1.0")
    kapt("androidx.room:room-compiler:2.1.0")
}
```

### 定义Entity

```kotlin
@Entity
data class User(var firstName: String, var lastName: String, var age: Int) {
    @PrimaryKey(autoGenerate = true)
    var id: Long = 0
}
```

### 定义Dao

```kotlin
@Dao
interface UserDao {
    @Insert
    fun insertUser(user: User): Long

    @Update
    fun updateUser(newUser: User)

    @Query("select * from User")
    fun loadAllUsers(): List<User>

    @Query("select * from User where age > :age")
    fun loadUsersOlderThan(age: Int): List<User>

    @Delete
    fun deleteUser(user: User)

    @Query("delete from User where lastName = :lastName")
    fun deleteUserByLastName(lastName: String): Int
}
```

### 定义Database

```kotlin
@Database(version = 1, entities = [User::class])
abstract classAppDatabase: RoomDatabase() {
    abstract fun userDao(): UserDao

    companion object {
        private var instance:AppDatabase? = null

        @Synchronized
        fun getDatabase(context:Context):AppDatabase{
instance?.let {
                return it
            }

            return Room.databaseBuilder(
                context.applicationContext,
AppDatabase::class.java,
                "app_database"
            )
                .build()
                .apply {
instance= this
                }
        }
    }
}
```

在需要的地方用userDao调用相应的增删改查方法。

```kotlin
val userDao = AppDatabase.getDatabase(this).userDao()
val user1 = User("Tom", "Brady", 40)
val user2 = User("Tom", "Hanks", 63)

addDataBtn.setOnClickListener {
    CoroutineScope(Dispatchers.IO).launch {
        user1.id= userDao.insertUser(user1)
        user2.id= userDao.insertUser(user2)
    }
}

updateDataBtn.setOnClickListener {
    CoroutineScope(Dispatchers.IO).launch {
        user1.age= 42
        userDao.updateUser(user1)
    }
}

deleteDataBtn.setOnClickListener {
    CoroutineScope(Dispatchers.IO).launch {
        userDao.deleteUserByLastName("Hanks")
    }
}

queryDataBtn.setOnClickListener {
    CoroutineScope(Dispatchers.IO).launch {
        val users = userDao.loadAllUsers()
        users.forEach { user ->
            Log.d("MainActivity", user.toString())
        }
    }
}
```

### 升级后的逻辑

```kotlin
@Database(version = 3, entities = [User::class, Book::class])
abstract classAppDatabase: RoomDatabase() {
    abstract fun userDao(): UserDao
    abstract fun bookDao(): BookDao

    companion object {
        // 升级数据库修改表结构
        val MIGRATION_2_3 = object : Migration(2, 3) {
            override fun migrate(database: SupportSQLiteDatabase) {
                database.execSQL("alter table Book add column author text not null default 'unknown'")
            }
        }

        // 升级添加数据库表
        val MIGRATION_1_2 = object : Migration(1, 2) {
            override fun migrate(database: SupportSQLiteDatabase) {
                database.execSQL(
                    "create table Book (id integer primary key autoincrement not null, name text not null, pages integer not null)"
                )
            }
        }

        private var instance:AppDatabase? = null

        @Synchronized
        fun getDatabase(context:Context):AppDatabase{
instance?.let {
                return it
            }

            return Room.databaseBuilder(
                context.applicationContext,
AppDatabase::class.java,
                "app_database"
            )
                .addMigrations(MIGRATION_1_2, MIGRATION_2_3)
                .build()
                .apply {
instance= this
                }
        }
    }
}
```

## Glide