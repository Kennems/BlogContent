---
title : 'Android 网络请求解析数据'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# 网络请求解析数据

## Pull方式解析XML数据

```kotlin
/**
 * pull 解析 xml
 */
class MainActivity5 : AppCompatActivity() {
    private val mBinding by lazy {
        ActivityMain5Binding.inflate(layoutInflater)
    }

    private fun sendRequestWithOkHttp() {
        thread{
            try {
                val client = OkHttpClient()
                val request = Request.Builder()
                    .url("<http://10.0.2.2/get_data.xml>")
                    .build()
                val response = client.newCall(request).execute()
                val responseData = response.body?.string()
                if (responseData != null){
                    parseXMLWithPull(responseData)
                }
            }catch (e: Exception){
                e.printStackTrace()
            }
        }
    }

    private fun parseXMLWithPull(xmlData: String) {
        try {
            val factory = XmlPullParserFactory.newInstance()
            val xmlPullParser = factory.newPullParser()
            xmlPullParser.setInput(StringReader(xmlData))
            var eventType = xmlPullParser.eventType
            var id = ""
            var name = ""
            var version = ""
            while (eventType != XmlPullParser.END_DOCUMENT) {
                val nodeName = xmlPullParser.name
                when (eventType) {
                    // 开始解析某个节点
                    XmlPullParser.START_TAG -> {
                        when (nodeName) {
                            "id" -> id = xmlPullParser.nextText()
                            "name" -> name = xmlPullParser.nextText()
                            "version" -> version = xmlPullParser.nextText()
                        }
                    }
                    // 完成解析某个节点
                    XmlPullParser.END_TAG -> {
                        if ("app" == nodeName) {
                            Log.d("MainActivity", "id is $id")
                            Log.d("MainActivity", "name is $name")
                            Log.d("MainActivity", "version is $version")
                        }
                    }
                }
                eventType = xmlPullParser.next()
            }
        } catch (e: Exception) {
            e.printStackTrace()
        }
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

        mBinding.sendRequestBtn.setOnClickListener {
            Log.d("MainActivity", "发送请求 ")
            sendRequestWithOkHttp()
        }
    }
}
```

![image-20250215150919137](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215150919137.png)

## SAX方式解析XML数据

### ContentHandler

```kotlin
class ContentHandler : DefaultHandler() {
    companion object {
        private const val TAG = "ContentHandler"
    }

    private var nodeName: String = ""
    private lateinit var id: StringBuilder
    private lateinit var name: StringBuilder
    private lateinit var version: StringBuilder

    override fun startElement(
        uri: String,
        localName: String,
        qName: String,
        attributes: Attributes
    ) {
        super.startElement(uri, localName, qName, attributes)
        nodeName = localName
//        Log.d(TAG, "uri: $uri")
//        Log.d(TAG, "localName is $localName")
//        Log.d(TAG, "qName is $qName")
//        Log.d(TAG, "attributes is $attributes")
    }

    override fun endElement(uri: String?, localName: String?, qName: String?) {
        super.endElement(uri, localName, qName)
        if ("app" == localName) {
            Log.d("ContentHandler", "id is ${id.toString().trim()}")
            Log.d("ContentHandler", "name is ${name.toString().trim()}")
            Log.d("ContentHandler", "version is ${version.toString().trim()}")
            // 最后要将StringBuilder清空
            id.setLength(0)
            name.setLength(0)
            version.setLength(0)
        }
    }

    override fun characters(ch: CharArray?, start: Int, length: Int) {
        super.characters(ch, start, length)
        when (nodeName) {
            "id" -> id.append(ch, start, length)
            "name" -> name.append(ch, start, length)
            "version" -> version.append(ch, start, length)
        }
    }

    override fun startDocument() {
        super.startDocument()
        id = StringBuilder()
        name = StringBuilder()
        version = StringBuilder()
    }

    override fun endDocument() {
        super.endDocument()
    }
}
```

### MainActivity6

```kotlin
/**
 * Sax 解析 xml
 */
class MainActivity6 : AppCompatActivity() {
    private val mBinding by lazy {
        ActivityMain6Binding.inflate(layoutInflater)
    }

    private fun sendRequestWithOkHttp() {
        thread {
            try {
                val client = OkHttpClient()
                val request = Request.Builder()
                    // 指定访问的服务器地址是计算机本机
                    .url("<http://10.0.2.2/get_data.xml>")
                    .build()
                val response = client.newCall(request).execute()
                val responseData = response.body?.string()
                if (responseData != null) {
                    parseXMLWithSAX(responseData)
                }

            } catch (e: Exception) {
                e.printStackTrace()
            }
        }
    }

    private fun parseXMLWithSAX(xmlData: String) {
        try {
            val factory = SAXParserFactory.newInstance()
            val xmlReader = factory.newSAXParser().xmlReader
            val handler = ContentHandler()
            // 将ContentHandler的实例设置到XMLReader中
            xmlReader.contentHandler = handler
            // 开始执行解析
            xmlReader.parse(InputSource(StringReader(xmlData)))

        } catch (e: Exception) {
            e.printStackTrace()
        }
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

        mBinding.sendRequestBtn.setOnClickListener {
            sendRequestWithOkHttp()
        }

    }
}
```

![image-20250215150905588](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215150905588.png)

## 解析JSON数据

```kotlin

```

![image-20250215150850925](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215150850925.png)

## 将网络请求封装成工具类

封装具体的 `HttpRequest` 和 `OkHttpRequest`

### HttpUtil

```kotlin
object HttpUtil {
    fun sendHttpRequest(address: String, httpCallbackListener: HttpCallbackListener): String {
        var connection: HttpURLConnection? = null
        try {
            val response = StringBuilder()
            val url = URL(address)
            connection = url.openConnection() as HttpURLConnection
            connection.connectTimeout = 8000
            connection.readTimeout = 8000
            val input = connection.inputStream
            val reader = BufferedReader(InputStreamReader(input))
            reader.use {
                reader.forEachLine {
                    response.append(it)
                }
            }
            // 调用 onFinish 回调
            httpCallbackListener.onFinish(response.toString())
            return response.toString()
        } catch (e: Exception) {
            e.printStackTrace()
            // 调用 onError 回调
            httpCallbackListener.onError(e)
            return ""
        } finally {
            connection?.disconnect()
        }
    }

    fun sendOkHttpRequest(address: String, callback: okhttp3.Callback) {
        val client = OkHttpClient()
        val request = Request.Builder()
            .url(address)
            .build()
        client.newCall(request).enqueue(callback)
    }
}
```

### HttpCallbackListener

```kotlin
/**
 * Callback 接口
 */
interface HttpCallbackListener {
    fun onFinish(response: String)

    fun onError(e: Exception)
}
```

### HttpRequest工具类请求

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/a6e4c3cd-aabf-4e0f-8862-7cf74795f576/d3ef1abc-9276-49b6-9182-fb1221435344/image.png)

### OkHttpRequest工具类请求

![image-20250215150833087](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215150833087.png)

## Retrofit

### App

```kotlin
data class App(
    val id: String,
    val name: String,
    val version: String
)
```

### AppService

```kotlin
interface AppService {
    @GET("get_data.json")
    fun getAppData(): Call<List<App>>
}
```

### MainActivity11

```kotlin
class MainActivity11 : AppCompatActivity() {
    private val mBinding by lazy { ActivityMain11Binding.inflate(layoutInflater) }
    companion object {
        private const val TAG = "MainActivity11"

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
        mBinding.getAppDataBtn.setOnClickListener {
            val retrofit = Retrofit
                .Builder()
                .baseUrl("<http://10.0.2.2/>")
                .addConverterFactory(GsonConverterFactory.create())
                .build()

            val appService = retrofit.create(AppService::class.java)
            appService.getAppData().enqueue(object : Callback<List<App>> {
                override fun onResponse(
                    call: Call<List<App>?>,
                    response: Response<List<App>?>
                ) {
                    val list = response.body()
                    if (list != null) {
                        for (app in list) {
                            Log.d(TAG, "id is ${app.id}")
                            Log.d(TAG, "name is ${app.name}")
                            Log.d(TAG, "version is ${app.version}")
                        }
                    }
                }

                override fun onFailure(
                    call: Call<List<App>?>,
                    t: Throwable
                ) {
                    t.printStackTrace()
                }
            })
        }
    }
}
```

![image-20250215150809849](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215150809849.png)