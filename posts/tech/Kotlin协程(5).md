---
title : 'Kotlin协程(5)'
date : 2024-08-06T22:30:13+08:00
lastmod: 2024-08-13T22:20:13+08:00
description : "Kotlin协程(4)" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Kotlin"]
---

# Kotlin协程(5)

![carHome](https://raw.githubusercontent.com/Kennems/blog-image/main/carHome.gif)

## Binding

视图绑定

- View Binding: 通过View Binding， Android 会为每个XML布局文件自动生成一个绑定类。在这个类中，每个View都有一个直接对应的属性，可以避免`findViewById`的使用，从而减少了手动查找视图和类型转换的工作。提高代码的安全性和可读性。

```kotlin
val binding = ActivityMainBinding.inflate(layoutInflater)
setContentView(binding.root)
binding.textView.text = "Hello World"
```

- Data Binding: 功能比View Binding 更为强大，允许在布局文件中直接绑定UI组件到数据源（如ViewModel），这样，数据的变化会自动更新UI

```xml
<data>
    <variable
        name="user"
        type="com.example.User"/>
</data>

<TextView
    android:text="@{user.name}"/>
```

Activity中：

```kotlin
val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)
binding.user = userObject
```

##  Holder

`Holder`通常指的是`ViewHolder`，用于在`RecyclerView`中复用视图的一种设计模式。ViewHolder主要用于存储视图引用，从而避免频繁调用`findViewById`，提高RecyclerView的性能。

- ViewHolder的作用
  - 缓存视图：在 `ViewHolder`中缓存了每个子项的视图引用，减少不必要的视图查找。
  - 服用视图：通过复用 `ViewHolder`对象，避免在滚动时重复创建和销毁视图，提高性能。

```kotlin
class MyViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
    val textView: TextView = itemView.findViewById(R.id.text_view)
}

override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
    holder.textView.text = data[position]
}
```

在RecyclerView中，当一个新的子项需要显示时，系统会使用已经创建好的`ViewHolder`，并通过`onBindViewHolder`方法绑定数据到视图上，从而避免重复创建视图。

## Hilt

负责托管对象与对象之间的注入关系

### 注解

- **@HiltAndroidApp**：触发 Hilt 的代码生成。
- **@AndroidEntryPoint**：创建一个依赖容器，该容器遵循 Android 类的生命周期。
- **@Module**：告诉 Hilt 如何提供不同类型的实例。
- **@InstallIn**：Install 用来告诉 Hilt 这个模块会被装配到哪个组件上。
- **@Provides**：告诉 Hilt 如何获得具体实例。
- **@Singleton**：单例。
- **@ViewModelInject**：通过构造函数，给 ViewModel 注入实例。

## Data Mapper

使用Data Mapper 分离数据源的Model和页面显示的Model， 不要因为数据源的增加，修改或者删除，导致上层页面也要跟着一起修改。

## Coil

Coil 是一个用于 Android 的图像加载库，具有以下特点：

1. **性能优越**：在性能上表现出色。
2. **体积较小**：与 Picasso 相比，体积小，且低于 Glide 和 Fresco。
3. **简单易用**：与 Kotlin 兼容，API 简单直观。
4. **技术先进**：基于 Coroutine、OkHttp、Okio 等现代技术，确保技术的先进性。
5. **丰富功能**：支持缓存管理（内存和磁盘缓存）、动态图像采样等，提升图像加载效率。

Coil 旨在提供高效的图像加载解决方案，特别适合 Kotlin 开发者。

## LoadType



## PagingState

- `pages`: List<Page<Key, Value>> 返回的上一页的数据，主要用于获取上一页最后一条数据作为下一页的开始位置。
- `config`：PagingConfig返回的初始化位置的PagingConfig包含了pageSize，prefetchDistance， initialLoadSize等等。



## MediatorResult

- 请求出现错误，返回MediatorResult.Error(e)
- 请求成功且有数据，返回MediatorResult.Success(endOfPaginationReached = true)。
- 请求成功但是没有数据，返回MediatorResult.Success(endOfPaginationReached = false)。

### CarBrandRemoteMediator.kt

```kotlin
@OptIn(ExperimentalPagingApi::class)
class CarBrandRemoteMediator(
    private val api: CarBrandService, // 依赖注入的API服务，用于请求网络数据
    private val database: AppDatabase // 依赖注入的数据库实例，用于数据存储
) : RemoteMediator<Int, CarBrandEntity>() {

    // 负责加载分页数据的核心方法
    override suspend fun load(
        loadType: LoadType, // 加载类型，可能是REFRESH、PREPEND或APPEND
        state: PagingState<Int, CarBrandEntity> // 用于获取当前分页状态
    ): MediatorResult {
        try {
            Log.d("Kennem", "loadType = ${loadType}")

            // 确定当前请求的页码
            val pageKey = when (loadType) {
                // 首次加载或手动刷新时，不需要页码，设为null
                LoadType.REFRESH -> null
                // 向前加载数据时，直接返回，不继续请求，标记为分页结束
                LoadType.PREPEND -> return MediatorResult.Success(endOfPaginationReached = true)
                // 向后加载更多数据时，获取最后一项的数据页码作为新的页码
                LoadType.APPEND -> {
                    val lastItem: CarBrandEntity = state.lastItemOrNull()
                        ?: return MediatorResult.Success(endOfPaginationReached = true)
                    lastItem.page
                }
            }

            // 无网络，加载本地数据
            if (!AppHelper.mContext.isConnectedNetWork()){
                MediatorResult.Success(endOfPaginationReached = true)
            }

            // 请求网络分页数据，pageKey为null时默认请求第一页数据
            val page = pageKey ?: 0
            val result = api.fetchCarBrandList(
                page * state.config.pageSize, // 根据页码和页面大小计算请求的偏移量
                state.config.pageSize // 页面大小
            )

            // 将网络数据映射为数据库实体
            val item = result.map {
                CarBrandEntity(
                    id = it.id,
                    name = it.name,
                    icon = it.icon,
                    page = page + 1 // 将页码加一，标记为下一次请求的页码
                )
            }

            // 判断是否到达分页末端
            val endOfPaginationReached = result.isEmpty()

            // 获取DAO操作对象
            val carBrandDao = database.carBrandDao()

            // 使用事务插入数据，保证数据一致性
            database.withTransaction {
                if (loadType == LoadType.REFRESH) {
                    // 如果是刷新操作，清空已有的数据
                    carBrandDao.clearCarBrand()
                }
                // 插入新数据
                carBrandDao.insertCarBrand(item)
            }

            // 返回加载结果，标记是否到达分页末端
            return MediatorResult.Success(endOfPaginationReached = endOfPaginationReached)
        } catch (e: Exception) {
            e.printStackTrace()
            // 捕获异常，返回错误结果
            return MediatorResult.Error(e)
        }
    }
}

```

## App Startup

- App Startup 是 Android Jetpack 最新成员，提供了在App启动时初始化组件简单、高效的方法，无论是library开发人员还是App开发人员都可以使用App Startup**显式的设置初始化顺序**。

![image-20240829130314698](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240829130314698.png)

```kotlin
/**
 * AppInitializer 是一个自定义的应用初始化类，用于在应用启动时进行必要的初始化操作。
 * 它实现了 androidx.startup.Initializer 接口。
 */
class AppInitializer : Initializer<Unit> {

    /**
     * create 方法在应用启动时被调用，用于执行初始化逻辑。
     *
     * @param context 应用程序的上下文，用于初始化操作
     */
    override fun create(context: Context) {
        // 调用 AppHelper 的 init 方法进行初始化操作
        AppHelper.init(context)
    }

    /**
     * dependencies 方法用于声明当前 Initializer 所依赖的其他 Initializer。
     * 这里返回一个空的列表，表示没有依赖项。
     *
     * @return MutableList<Class<out Initializer<*>>> 当前初始化器的依赖项列表
     */
    override fun dependencies(): MutableList<Class<out Initializer<*>>> = mutableListOf()
}
```

### AndroidManifest.xml

添加provider部分

```xml
        <provider
            android:name="androidx.startup.InitializationProvider"
            android:authorities="${applicationId}.androidx-startup"
            android:exported="false"
            tools:node="merge">
            <meta-data
                android:name="com.show.carhome.init.AppInitializer"
                android:value="androidx.startup" />
        </provider>
```

