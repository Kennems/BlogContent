---
title : 'Kotlin协程(4)'
date : 2024-08-25T22:30:13+08:00
lastmod: 2024-08-27T22:20:13+08:00
description : "Kotlin协程(4)" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Kotlin"]
---

# Kotlin协程(4)

Kotlin实践部分

## Flow与文件下载应用

![flow_download](https://raw.githubusercontent.com/Kennems/blog-image/main/flow_download.gif)

### DownloadFragment.kt

```kotlin
// DownloadFragment 是一个 Fragment 类，用于处理文件下载任务
class DownloadFragment : Fragment() {

    // 定义下载文件的 URL 地址，这是一个静态的常量
    val URL = "https://ts1.cn.mm.bing.net/th/id/R-C.56ab5704680b6574c1b3c0a52643d8b5?rik=P8OAzrJEZS%2biuw&riu=http%3a%2f%2fjourneyz.co%2fwp-content%2fuploads%2f2019%2f09%2fGolden-Gate-Bridge.jpg&ehk=WX9eb2rUUjWBLLrsG2MQZLOMk2wtreV%2bT1Qq1tARk4s%3d&risl=&pid=ImgRaw&r=0"

    // 延迟初始化 mBinding，这是一个 FragmentDownloadBinding 对象，用于绑定 XML 布局文件
    private val mBinding: FragmentDownloadBinding by lazy {
        FragmentDownloadBinding.inflate(layoutInflater)
    }

    // 重写 onCreateView 方法，在 Fragment 创建视图时调用
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        // 返回绑定的根视图作为 Fragment 的界面
        return mBinding.root
    }

    // 重写 onActivityCreated 方法，当与 Fragment 相关的活动的 onCreate 方法完成时调用
    @RequiresApi(Build.VERSION_CODES.UPSIDE_DOWN_CAKE)
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)

        // 使用 lifecycleScope 启动协程，当 Fragment 的生命周期处于 Created 状态时执行
        lifecycleScope.launchWhenCreated {
            // 获取上下文并执行下载操作
            context?.apply {
                // 指定下载文件的存储位置，保存在应用的外部文件目录下
                val file = File(getExternalFilesDir(null)?.path, "pic.jpg")

                // 开始下载文件，并收集下载状态
                DownloadManager.download(URL, file).collect { status ->
                    when (status) {
                        // 如果下载过程中有进度更新
                        is DownloadStatus.Progress -> {
                            mBinding.apply {
                                // 更新进度条和进度文本
                                progressBar.progress = status.value
                                tvProgress.text = "${status.value}%"
                            }
                        }

                        // 如果下载过程中出现错误
                        is DownloadStatus.Error -> {
                            Toast.makeText(context, "下载错误", Toast.LENGTH_SHORT).show()
                        }

                        // 如果下载完成
                        is DownloadStatus.Done -> {
                            mBinding.apply {
                                // 设置进度条到 100%，并更新文本为 100%
                                progressBar.progress = 100
                                tvProgress.text = "100%"
                            }
                            Toast.makeText(context, "下载完成", Toast.LENGTH_SHORT).show()
                        }

                        // 处理其他可能的下载状态
                        else -> {
                            Log.d("Kennem", "下载失败")
                        }
                    }
                }
            }
        }
    }
}
```


### DownloadManager.kt

```kotlin
object DownloadManager {

    /**
     * 下载指定的文件并返回下载状态的流（Flow）。
     *
     * @param url 文件下载的 URL 地址。
     * @param file 下载后保存的本地文件。
     * @return 下载状态的 Flow，包含下载进度、完成状态或错误信息。
     */
    @RequiresApi(Build.VERSION_CODES.UPSIDE_DOWN_CAKE)
    fun download(url: String, file: File): Flow<DownloadStatus> {
        return flow {
            // 构建 HTTP 请求对象
            val request = Request.Builder().url(url).get().build()
            // 执行 HTTP 请求，获取响应
            val response = OkHttpClient.Builder().build().newCall(request).execute()

            // 检查响应是否成功
            if (response.isSuccessful) {
                response.body()!!.let { body ->
                    // 获取内容的总长度（字节数）
                    val total = body.contentLength()
                    // 使用输出流将下载的数据写入本地文件
                    file.outputStream().use { output ->
                        val input = body.byteStream()  // 获取输入流
                        var emittedProgress = 0L  // 记录已发出的进度

                        // 将输入流中的数据复制到输出流，同时跟踪复制的字节数
                        input.copyTo(output) { bytesCopied ->
                            // 计算当前进度百分比
                            val progress = bytesCopied * 100 / total
                            // 每当进度比上次更新多5%以上时，发出进度更新
                            if (progress - emittedProgress > 5) {
                                delay(100)  // 模拟网络延迟（可选）
                                emit(DownloadStatus.Progress(progress.toInt()))  // 发出进度状态
                                emittedProgress = progress  // 更新已发出的进度
                            }
                        }
                    }
                }
                // 下载完成，发出完成状态
                emit(DownloadStatus.Done(file))
            } else {
                // 如果响应失败，抛出异常
                throw IOException(response.toString())
            }
        }.catch {
            // 如果发生错误，删除部分下载的文件并发出错误状态
            file.delete()
            emit(DownloadStatus.Error(it))
        }.flowOn(Dispatchers.IO)  // 在 IO 线程上运行下载流程
    }
}
```

### DownloadStatus.kt

```kotlin
/**
 * 封闭类，用于表示下载过程中的不同状态。
 * 封闭类允许定义一个有限的类型集合，在使用 `when` 表达式时提供全面的类型检查。
 */
sealed class DownloadStatus {

    /**
     * 表示初始状态，即尚未开始下载。
     * 可以用于初始化或重置状态。
     */
    object None : DownloadStatus()

    /**
     * 表示正在进行的下载的进度。
     * @param value 一个整数，表示当前的进度百分比（0-100）。
     */
    data class Progress(val value: Int) : DownloadStatus()

    /**
     * 表示下载过程中发生的错误。
     * @param throwable 引发失败的异常或错误。
     */
    data class Error(val throwable: Throwable) : DownloadStatus()

    /**
     * 表示下载成功完成。
     * @param file 下载完成的文件。
     */
    data class Done(val file: File) : DownloadStatus()
}
```

## Flow与Room的应用

![flow_room](https://raw.githubusercontent.com/Kennems/blog-image/main/flow_room.gif)

### UserFragment.kt

```kotlin
/**
 * `UserViewModel` 继承自 `AndroidViewModel`，用于管理用户数据的视图模型。
 * `AndroidViewModel` 提供了对应用程序上下文的访问。
 *
 * @param app 应用程序上下文。
 */
class UserViewModel(app: Application) : AndroidViewModel(app) {

    /**
     * 插入一个新的用户到数据库中。
     * 这个方法会在 `viewModelScope` 中异步执行。
     * `viewModelScope` 是 `ViewModel` 提供的协程作用域，可以自动取消任务。
     *
     * @param uid 用户 ID。
     * @param firstName 用户的名字。
     * @param lastName 用户的姓氏。
     */
    fun insert(uid: String, firstName: String, lastName: String) {
        viewModelScope.launch {
            // 获取应用数据库实例并执行插入操作
            AppDatabase.getInstance(getApplication())
                .userDao()
                .insert(User(uid.toInt(), firstName, lastName))
            // 打印日志，便于调试
            Log.d("Kennem", "insert user: $uid")
        }
    }

    /**
     * 获取所有用户的流。
     * 返回一个 `Flow` 对象，它允许异步地获取数据并观察数据变化。
     *
     * @return 返回一个 `Flow` 对象，其中包含用户列表。
     */
    fun getAll(): Flow<List<User>> {
        return AppDatabase.getInstance(getApplication())
            .userDao()
            .getAll()
            .catch { e ->
                // 捕获并打印流中的异常
                e.printStackTrace()
            }
            .flowOn(Dispatchers.IO) // 在 IO 线程中执行数据库操作
    }
}
```

### User.kt

```kotlin
@Entity(tableName = "user")
data class User(
    @PrimaryKey(autoGenerate = true) val uid: Int = 0,
    @ColumnInfo(name = "first_name") val firstName: String,
    @ColumnInfo(name = "last_name") val lastName: String
)
```

### UserDao.kt

```kotlin
@Dao
interface UserDao{
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(user: User)

    @Query("SELECT * FROM user")
    fun getAll(): Flow<List<User>>
}
```

### AppDatabase.kt

```kotlin
/**
 * Room 数据库类，用于定义数据库及其版本。
 * Room 是 Android 的持久性库，它提供了一个抽象层，方便使用 SQLite 数据库。
 *
 * @param entities 数据库中的实体类。这里定义了 `User` 实体类。
 * @param version 数据库的版本号。当实体类结构发生变化时，版本号需要递增。
 * @param exportSchema 是否导出数据库模式到一个 JSON 文件。这里设置为 false，表示不导出。
 */
@Database(entities = [User::class], version = 1, exportSchema = false)
abstract class AppDatabase : RoomDatabase() {

    /**
     * 获取 UserDao 的实例。
     * DAO（数据访问对象）用于定义对数据库的操作。
     *
     * @return UserDao 实例，用于执行数据库操作。
     */
    abstract fun userDao(): UserDao

    companion object {
        @Volatile
        private var instance: AppDatabase? = null

        /**
         * 获取数据库实例的方法。
         * 如果实例不存在，则同步创建一个新实例，并进行初始化。
         *
         * @param context 应用程序上下文，用于创建数据库。
         * @return AppDatabase 的单例实例。
         */
        fun getInstance(context: Context): AppDatabase {
            // 检查现有的实例
            return instance ?: synchronized(this) {
                // 双重检查锁定（Double-Checked Locking）
                val newInstance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "app_database"
                ).build()
                instance = newInstance
                newInstance
            }
        }
    }
}
```

### UserAdapter.kt

```kotlin
// UserAdapter 是一个继承自 RecyclerView.Adapter 的适配器类，用于在 RecyclerView 中显示 User 数据。
class UserAdapter(private val context: Context) : RecyclerView.Adapter<BindingViewHolder>() {

    // data 用于存储要显示的 User 数据的 ArrayList。
    private val data = ArrayList<User>()

    /**
     * 用于更新适配器的数据。
     * @param data 要设置的新数据列表。
     */
    @SuppressLint("NotifyDataSetChanged")
    fun setData(data: List<User>) {
        // 打印日志以查看 setData 方法调用时传递的数据。
        Log.d("Kennem", "setData called with data: $data")
        // 清空当前数据并添加新的数据。
        this.data.clear()
        this.data.addAll(data)
        // 通知适配器数据已更改，刷新视图。
        notifyDataSetChanged()
    }

    /**
     * 创建新的 ViewHolder 对象。
     * @param parent ViewGroup 父视图，用于在其中添加新视图。
     * @param viewType 视图类型。
     * @return 返回一个新的 BindingViewHolder 对象。
     */
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BindingViewHolder {
        // 使用指定的上下文对象通过布局解析器加载布局并绑定到 ViewHolder。
        val binding = ItemUserBinding.inflate(LayoutInflater.from(context), parent, false)
        return BindingViewHolder(binding = binding)
    }

    /**
     * 返回 RecyclerView 中数据项的总数。
     * @return 数据项的总数。
     */
    override fun getItemCount() = data.size

    /**
     * 绑定 ViewHolder 中的视图数据。
     * @param holder BindingViewHolder 当前 ViewHolder。
     * @param position 当前数据项的位置。
     */
    @SuppressLint("SetTextI18n")
    override fun onBindViewHolder(holder: BindingViewHolder, position: Int) {
        // 根据当前的 position 获取对应的 User 对象。
        val item = data[position]
        // 将 ViewHolder 的绑定对象转换为 ItemUserBinding 类型。
        val binding = holder.binding as ItemUserBinding
        // 设置文本视图，显示用户的 id、名字和姓氏。
        binding.text.text = "id: ${item.uid}, Name: ${item.firstName}, ${item.lastName}"
    }
}
```

## Flow与Retrofit的应用

![flow_retrofit](https://raw.githubusercontent.com/Kennems/blog-image/main/flow_retrofit.gif)

### ArticleFragment.kt

```kotlin
class ArticleFragment : Fragment() {

    // 使用 viewModels 委托属性获取 ArticleViewModel 实例
    private val viewModel by viewModels<ArticleViewModel>()

    // 延迟初始化 FragmentArticleBinding 实例，用于绑定布局
    private val mBinding: FragmentArticleBinding by lazy {
        FragmentArticleBinding.inflate(layoutInflater)
    }

    // 创建视图时返回绑定的根视图
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View? {
        return mBinding.root
    }

    // 扩展函数：为 TextView 创建一个 Flow 以响应文本变化
    private fun TextView.textWatcherFlow(): Flow<String> = callbackFlow {
        // 创建一个 TextWatcher 对象以监听文本变化
        val textWatcher = object : TextWatcher {
            override fun beforeTextChanged(
                s: CharSequence?, start: Int, count: Int, after: Int
            ) {
                // 在文本变化之前的回调，这里无需处理
            }

            override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
                // 在文本变化时的回调，这里无需处理
            }

            override fun afterTextChanged(s: Editable?) {
                // 在文本变化后发送新文本到 Flow
                this@callbackFlow.trySend(s.toString()).isSuccess
            }
        }

        // 将 TextWatcher 添加到 TextView
        addTextChangedListener(textWatcher)

        // 当 Flow 关闭时，移除 TextWatcher
        awaitClose {
            removeTextChangedListener(textWatcher)
        }
    }

    // 在 Activity 创建时执行的操作
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)

        // 启动一个协程来收集文本变化并搜索文章
        lifecycleScope.launchWhenCreated {
            mBinding.etSearch.textWatcherFlow().collect {
                Log.d("Kennem", "onActivityCreated: 收到文本变化：$it")
                viewModel.searchArticles(it)
            }
        }

        // 配置 RecyclerView 适配器和布局管理器
        context?.let {
            val adapter = ArticleAdapter(it)
            mBinding.recyclerView.adapter = adapter
            mBinding.recyclerView.layoutManager = LinearLayoutManager(context)
            // 观察文章列表变化并更新适配器的数据
            viewModel.articles.observe(viewLifecycleOwner) { articles ->
                adapter.setData(articles)
            }
        }
    }
}
```

### ArticleViewModel.kt

```kotlin
/**
 * `ArticleViewModel` 继承自 `AndroidViewModel`，用于管理与文章相关的视图模型。
 * `AndroidViewModel` 提供了对应用程序上下文的访问。
 *
 * @param app 应用程序上下文。
 */
class ArticleViewModel(app: Application) : AndroidViewModel(app) {

    /**
     * 使用 `MutableLiveData` 来保存和观察文章数据。
     * `MutableLiveData` 允许对数据进行修改，并通知观察者数据变化。
     */
    val articles = MutableLiveData<ResponseBody>()

    /**
     * 根据给定的关键字搜索文章。
     * 使用协程和流来进行异步网络请求并处理结果。
     *
     * @param key 搜索关键字。
     */
    fun searchArticles(key: String) {
        viewModelScope.launch {
            flow {
                // 发起网络请求获取文章数据
                val list = RetrofitClient.articleApi.searchArticles(key)
                // 将获取的结果发射到流中
                emit(list)
            }
                .flowOn(Dispatchers.IO) // 在 IO 线程中执行网络请求
                .catch { e ->
                    // 捕获并打印流中的异常
                    e.printStackTrace()
                }
                .collect {
                    // 更新 LiveData 的值，以便 UI 可以观察到变化
                    articles.value = it
                }
        }
    }
}
```

### RetrofitClient.kt

```kotlin
object RetrofitClient {
    private val instance: Retrofit by lazy {
        Retrofit
            .Builder()
            .client(OkHttpClient.Builder().build())
            .baseUrl("https://www.bing.com/")
            .build()
    }

    val articleApi: ArticleApi by lazy {
        instance.create(ArticleApi::class.java)
    }
}
```

### ArticleApi.kt

```kotlin
interface ArticleApi {
    @GET("search")
    suspend fun searchArticles(
        @Query("q") key : String
    ): ResponseBody
}
```

### ArticleAdapter.kt

```kotlin
class ArticleAdapter(private val context: Context) : RecyclerView.Adapter<BindingViewHolder>() {

    // 用于存储 ResponseBody 对象列表
    private val data = ArrayList<String>()

    // 设置数据时，将 ResponseBody 转换为字符串
    @SuppressLint("NotifyDataSetChanged")
    fun setData(responseBody: ResponseBody) {
        Log.d("Kennem", "setData called with data: $responseBody")
        this.data.clear()
        // 将 ResponseBody 转换为字符串并添加到数据列表中
        this.data.add(responseBody.string())
        notifyDataSetChanged()
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BindingViewHolder {
        val binding = ItemUserBinding.inflate(LayoutInflater.from(context), parent, false)
        return BindingViewHolder(binding = binding)
    }

    override fun getItemCount() = data.size

    @SuppressLint("SetTextI18n")
    override fun onBindViewHolder(holder: BindingViewHolder, position: Int) {
        val item = data[position]
        val binding = holder.binding as ItemUserBinding
        // 将字符串形式的原始响应内容显示在 TextView 中
        binding.text.text = item
    }
}
```

## StateFlow

![stateFlow](https://raw.githubusercontent.com/Kennems/blog-image/main/stateFlow.gif)

类似LiveData, 具有LiveDat的属性又有Flow的支持

```kotlin
class NumberFragment : Fragment() {
    // 使用 viewModels 委托属性获取 ArticleViewModel 实例
    private val viewModel by viewModels<NumberViewModel>()

    // 延迟初始化 FragmentArticleBinding 实例，用于绑定布局
    private val mBinding: FragmentNumberBinding by lazy {
        FragmentNumberBinding.inflate(layoutInflater)
    }

    // Fragment的布局生成和视图创建
    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        // 返回绑定的根视图
        return mBinding.root
    }

    // 在Activity创建后调用，用于初始化视图和数据
    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)

        // 使用apply作用域函数来简化代码
        mBinding.apply {
            // 为增加按钮设置点击事件监听器
            btnAdd.setOnClickListener{
                // 调用ViewModel的increment函数，增加计数值
                viewModel.increment()
            }
            // 为减少按钮设置点击事件监听器
            btnMinus.setOnClickListener{
                // 调用ViewModel的decrement函数，减少计数值
                viewModel.decrement()
            }
        }

        // 使用lifecycleScope来确保在视图创建时收集数据流
        lifecycleScope.launchWhenCreated {
            // 收集ViewModel中number的状态流，并在UI中显示
            viewModel.number.collect { value ->
                // 将当前的数值更新到TextView中
                mBinding.tvNumber.text = "$value"
            }
        }
    }
}
```

## ShareFlow

ShareFlow为热流，一直发送，可以在多个位置接受到发送的流 

![shareFlow](https://raw.githubusercontent.com/Kennems/blog-image/main/shareFlow.gif)

### LocalEventBus.kt

```kotlin
object LocalEventBus {
    val events = MutableSharedFlow<Event>()
    suspend fun postEvent(event: Event){
        events.emit(event)
    }
}

data class Event(val timestamp: Long)
```

### SharedFlowViewModel.kt

```kotlin
class SharedFlowViewModel : ViewModel() {
    private var job: Job? = null

    fun startRefresh() {
        // 检查是否已有正在运行的 Job
        if (job?.isActive == true) {
            return // 如果已存在，直接返回
        }

        // 启动新的 Job
        job = viewModelScope.launch(Dispatchers.IO) {
            while (isActive) {
                LocalEventBus.postEvent(Event(System.currentTimeMillis()))
            }
        }
    }

    fun stopRefresh() {
        job?.cancel() // 取消当前的 Job
        job = null    // 将 job 置为空，表示没有运行的 Job
    }
}
```

### SharedFlowFragment.kt

```kotlin
class SharedFlowFragment : Fragment() {

    private val viewModel by viewModels<SharedFlowViewModel>()

    private val mBinding: FragmentSharedFlowBinding by lazy {
        FragmentSharedFlowBinding.inflate(layoutInflater)
    }

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return mBinding.root
    }

    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        mBinding.apply {
            btnStart.setOnClickListener {
                viewModel.startRefresh()
            }
            btnEnd.setOnClickListener {
                viewModel.stopRefresh()
            }
        }
    }

}
```

## Flow与Jetpack Paging3

### Paging3

#### 加载数据流程

![image-20240826190455634](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240826190455634.png)

![paging3](https://raw.githubusercontent.com/Kennems/blog-image/main/paging3.gif)

### PageConfig

- pageSize: 每页显示的数据的大小
- prefetchDistance，预刷新的距离，距离最后一个item多远时加载数据，默认为pageSize。
- initialLoadSize，初始化加载数量，默认位pageSize * 3

### Paging3部分

#### MoviePagingSource.kt

```kotlin
// MoviePagingSource 继承自 PagingSource，用于定义如何从数据源加载分页数据
class MoviePagingSource : PagingSource<Int, Movie>() {

    // 用于获取刷新数据时的键值
    override fun getRefreshKey(state: PagingState<Int, Movie>): Int? {
        // 获取当前锚定位置的索引，如果不存在则返回 null
        val anchorPosition = state.anchorPosition ?: return null

        // 获取与锚定位置最近的一页
        val anchorPage = state.closestPageToPosition(anchorPosition)

        // 如果锚定页存在且前一页的 key 存在，则返回前一页的 key 加 1
        // 否则，如果下一页的 key 存在，则返回下一页的 key 减 1
        return anchorPage?.prevKey?.plus(1) ?: anchorPage?.nextKey?.minus(1)
    }

    // 定义如何加载数据
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, Movie> {
        // 当前要加载的页码，如果为 null 则加载第一页
        val currentPage = params.key ?: 1

        // 需要加载的项目数
        val pageSize = params.loadSize

        return try {
            // 使用 Retrofit 发送网络请求获取电影数据，传入当前页码和每页加载的数量
            val response = RetrofitClient.createApi(MovieApi::class.java)
                .getMovies(currentPage, pageSize)

            // 打印日志，显示加载的页码和获取的电影数量
            Log.d("PagingSource", "Loaded page $currentPage with ${response.movieList.size} items")

            // 获取电影列表，如果为空则返回空列表
            val movies = response.movieList ?: emptyList()

            // 如果当前页是第一页，则前一页的 key 为 null，否则为当前页码减 1
            val prevKey = if (currentPage == 1) null else currentPage - 1

            // 如果还有更多数据，则下一页的 key 为当前页码加 1，否则为 null
            val nextKey = if (response.hasMore) currentPage + 1 else null

            // 返回加载的结果，包括数据、前一页的 key 和下一页的 key
            LoadResult.Page(
                data = movies,
                prevKey = prevKey,
                nextKey = nextKey
            )
        } catch (e: Exception) {
            // 捕获异常并打印错误日志
            Log.e("PagingSource", "Error loading page $currentPage", e)

            // 返回错误结果，传递异常信息
            return LoadResult.Error(e)
        }
    }
}
```

#### MovieViewModel.kt

负责分页的配置以及分页的加载

```kotlin
// MovieViewModel 继承自 ViewModel，负责管理 UI 相关的数据
class MovieViewModel : ViewModel() {

    // 定义 movies 变量，使用 lazy 初始化，即在首次访问时才会初始化
    // Pager 是 Paging 库中用于分页加载数据的核心组件
    private val movies by lazy {
        Pager(
            config = PagingConfig(
                pageSize = 8,            // 每页加载的数据量大小
                prefetchDistance = 1,    // 提前预加载下一页数据的触发距离
                initialLoadSize = 8      // 首次加载时加载的数据量大小
            ),
            // 定义数据源工厂，创建 MoviePagingSource 实例，用于从数据源加载数据
            pagingSourceFactory = { MoviePagingSource() }
        )
            // flow 是一种异步数据流，cachedIn(viewModelScope) 将流数据缓存到 ViewModel 的作用域中
            // 这样即使屏幕旋转，数据也能保留，不需要重新加载
            .flow.cachedIn(viewModelScope)
    }

    // 暴露一个公共方法用于加载电影数据，并返回 Flow<PagingData<Movie>> 类型的流
    fun loadMovie(): Flow<PagingData<Movie>> = movies
}
```

### NetWrok部分

#### RetrofitClient.kt

```kotlin
object RetrofitClient {
    private val instance: Retrofit by lazy {
        val interceptor = HttpLoggingInterceptor {
            Log.d("Kennem", "$it")
        }
        interceptor.level = HttpLoggingInterceptor.Level.BODY
        Retrofit.Builder()
            .client(OkHttpClient.Builder().addInterceptor(interceptor).build())
            .baseUrl("https://apifoxmock.com/m1/4944163-4601795-default/")
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }

    fun <T> createApi(clazz: Class<T>): T {
        // 调用实例的 create 方法，传入 clazz 参数以创建接口的实例
        return instance.create(clazz) as T
    }
}
```

