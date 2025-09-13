---
title : 'Jetpack Compose(1)'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Jetpack Compose(1)

- Jetpack Compose是用于构建原生Android界面的新工具包。它可简化并加快Android上的界面开发，帮助使用更少的代码、强大的工具和只管的Kotlin API，快速打造生动而精彩的应用。

## Compose的优势

- 更少的代码
  - 使用更少的代码实现更多的功能，并且可以避免各种bug，从而使代码简洁且易于维护
- 直观
  - 只需要描述界面，Compose会负责处理剩余的工作。应用状态变化时，界面会自动更新
- 加快应用开发
  - 兼容现有的所有代码，方便随时随地使用。借助实时预览和全面的Android Studio支持，实现快速迭代。
- 功能强大
  - 凭借对Android平台API的直接访问和对于Material Design，深色主题、动画等的内置支持，创建精美的应用。

## 可组合函数

Jetpack Compose 是围绕可组合函数构建的。这些函数可以让你以程序化定义应用的界面，只需描述应用界面的外观并提供数据项，而不必关注界面的构建过程（初始化元素，将其附加到父项等）。如需创建可组合函数，只需将`@Composable`注解添加到函数名称中即可。

## 第一个Compose

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            MessageCard(Message("Kotlin", "kkk"))
        }
    }

    @Composable
    fun MessageCard(message: Message) {
        Text(text = "Hello ${message.author}!")
    }

    @Preview
    @Composable
    fun PreviewMessageCard() {
        MessageCard(Message("Kotlin", "kkk"))
    }
}

data class Message(val author: String, val body: String)
```

![image-20250215170216112](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215170216112.png)

## Column & Row

使用Column垂直排列元素，使用Row水平排列项，使用Box堆叠元素。

![image-20250215170226532](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215170226532.png)

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            MessageCard(Message("Kotlin", "kkk"))
        }
    }

    @Composable
    fun MessageCard(message: Message) {
        Row {
            Image(painter = painterResource(id = R.drawable.prayer),
                contentDescription = null)
            Column {
                Text(text = "Hello ${message.author}!")
                Text(text = message.body)
            }
        }
    }

    @Preview
    @Composable
    fun PreviewMessageCard() {
        MessageCard(Message("Kotlin", "kkk"))
    }
    
data class Message(val author: String, val body: String)
```

## 配置布局

为了装饰或配置可组合项，Compose使用了修饰符。

![image-20250215170239209](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215170239209.png)

```kotlin
class MainActivity2 : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            MessageCard(Message("Kotlin", "kkk"))
        }
    }

    @Composable
    fun MessageCard(message: Message) {
        Row(modifier = Modifier.padding(all = 8.dp)) {
            Image(
                painter = painterResource(id = R.drawable.boat),
                contentDescription = null,
                modifier = Modifier
                    .size(40.dp)
                    .clip(CircleShape)
            )
            Column {
                Text(text = "Hello ${message.author}!")
                Text(text = message.body)
            }
        }
    }

    @Preview
    @Composable
    fun PreviewMessageCard() {
        MessageCard(Message("Kotlin", "kkk"))
    }

    data class Message(val author: String, val body: String)
}
```

## Material Design

- Compose 旨在支持Material Design原则。它的许多界面元素都原生支持Material Design. Material Design是围绕三大要素构建的：颜色、排版、形状。

## 列表和动画

Compose可以轻松创建列表并添加有趣的动画效果

- 创建消息列表
- 在展开消息时显示动画效果

![composeList](https://raw.githubusercontent.com/Kennems/blog-image/main/composeList.gif)

```kotlin
class MainActivity4 : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()

        setContent {
            ComPoseLearnTheme() {
                val sampleMessages = listOf(
                    Message("Alice", "Hey there! Just finished an amazing project at work and feeling really excited about the potential impact. Can't wait to share more details with you soon!"),
                    Message("Bob", "I've been exploring some new hiking trails in the mountain region. The landscapes are absolutely breathtaking, and the natural beauty is just incredible. Thinking of planning a longer trek next month."),
                    Message("Charlie", "Programming is such an interesting field! I've been diving deep into Kotlin and Jetpack Compose, and I'm constantly amazed by how elegant and powerful the language and framework are."),
                    Message("David", "Tried out a new recipe for homemade pizza last night. Spent hours perfecting the dough and experimenting with different toppings. It turned out better than any restaurant pizza I've had!"),
                    Message("Emma", "Just got back from an incredible conference about artificial intelligence and machine learning. The keynote speakers were mind-blowing, and I learned so many cutting-edge techniques."),
                    Message("Frank", "Thinking about starting a side project that combines my passion for photography and mobile app development. Any suggestions on interesting app ideas that could showcase visual creativity?"),
                    Message("Grace", "Reading an fascinating book about the history of technology innovation. It's fascinating how small ideas can transform entire industries and change the way we live and work."),
                    Message("Henry", "Spent the weekend learning advanced Kotlin coroutines and concurrent programming. The complexity is challenging, but also incredibly rewarding when you start understanding the concepts."),
                    Message("Isabella", "Planning a cross-country road trip and researching the best routes, attractions, and hidden gems along the way. Travel is such an amazing way to broaden one's perspective."),
                    Message("Jack", "Just completed a marathon coding session developing a complex Android application using Jetpack Compose. The declarative UI approach is a game-changer!")
                )

                ComPoseLearnTheme {
                    Conversation(messages = sampleMessages)
                }
            }
        }
    }

    @Composable
    fun Conversation(messages: List<Message>) {
        LazyColumn {
            items(messages.size) { messageIndex ->
                MessageCard(messages.get(messageIndex))
            }
        }
    }

    @Composable
    fun MessageCard(message: Message) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(all = 8.dp)
                .background(MaterialTheme.colorScheme.background),
            verticalAlignment = Alignment.CenterVertically, // Row 内部垂直居中
//            horizontalArrangement = Arrangement.Center // Row 内部水平居中
        ) {
            Image(
                painter = painterResource(id = R.drawable.boat),
                contentDescription = "boat",
                modifier = Modifier
                    .size(60.dp)
                    .clip(CircleShape),
                contentScale = ContentScale.Crop // 确保图片填充整个圆形区域
            )

            Spacer(modifier = Modifier.width(8.dp))
            
            var isExpanded by remember { mutableStateOf(false) }
            
            Column(
                modifier = Modifier.clickable { isExpanded = !isExpanded }, // 图片和文字间添加间距
                verticalArrangement = Arrangement.Center, // Column 内部垂直居中
                horizontalAlignment = Alignment.Start // Column 内文字水平对齐方式
            ) {
                Text(
                    text = "Hello ${message.author}!",
                    color = MaterialTheme.colorScheme.onBackground
                )// 使用主题提供的颜色
                Text(
                    text = message.body,
                    color = MaterialTheme.colorScheme.onBackground,
                    style = MaterialTheme.typography.bodySmall,
                    maxLines = if (isExpanded) Int.MAX_VALUE else 1,
                )// 使用主题提供的颜色
            }
        }
    }

    @Preview(showBackground = true)
    @Composable
    fun PreviewConversation() {
        val sampleMessages = listOf(
            Message("Alice", "Hey there! Just finished an amazing project at work and feeling really excited about the potential impact. Can't wait to share more details with you soon!"),
            Message("Bob", "I've been exploring some new hiking trails in the mountain region. The landscapes are absolutely breathtaking, and the natural beauty is just incredible. Thinking of planning a longer trek next month."),
            Message("Charlie", "Programming is such an interesting field! I've been diving deep into Kotlin and Jetpack Compose, and I'm constantly amazed by how elegant and powerful the language and framework are."),
            Message("David", "Tried out a new recipe for homemade pizza last night. Spent hours perfecting the dough and experimenting with different toppings. It turned out better than any restaurant pizza I've had!"),
            Message("Emma", "Just got back from an incredible conference about artificial intelligence and machine learning. The keynote speakers were mind-blowing, and I learned so many cutting-edge techniques."),
            Message("Frank", "Thinking about starting a side project that combines my passion for photography and mobile app development. Any suggestions on interesting app ideas that could showcase visual creativity?"),
            Message("Grace", "Reading an fascinating book about the history of technology innovation. It's fascinating how small ideas can transform entire industries and change the way we live and work."),
            Message("Henry", "Spent the weekend learning advanced Kotlin coroutines and concurrent programming. The complexity is challenging, but also incredibly rewarding when you start understanding the concepts."),
            Message("Isabella", "Planning a cross-country road trip and researching the best routes, attractions, and hidden gems along the way. Travel is such an amazing way to broaden one's perspective."),
            Message("Jack", "Just completed a marathon coding session developing a complex Android application using Jetpack Compose. The declarative UI approach is a game-changer!")
        )

        ComPoseLearnTheme {
            Conversation(messages = sampleMessages)
        }
    }

    data class Message(val author: String, val body: String)
}
```

![image-20250215170137213](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215170137213.png)

## Compose需要解决的问题

- 在编写可维护的软件时，目标是最大程度地**减少耦合增加内聚**
- 尽可能的将相关的代码组合在一起，以便我们可以轻松地维护它们，并方便随着应用规模的增长而扩展我们的代码，这个称之为关注点分离
- 视图模型会使用像Kotlin这样的编程语言进行定义，而布局模型则使用XML。由于这两种语言的差异，使得它们之间存在一条强制的分割线。然而即使存在这种情况，视图模型和布局XML还是可以关联得十分紧密。

由于可以使用相同的语言，一些以往隐式的依赖关系可能会变得更加明显。我们也可以重构代码并将其移动至那些可以使它们减少耦合和增加内聚的位置。

## Composable 函数剖析

AppData可以接受数据作为参数。

可以使用任何Kotlin代码来获取这一数据，并利用它来描述的我们的层级结构，例如Header()与Body()调用。

```kotlin
    @Composable
    fun App(appData : AppData){
        val deliverData = compute(appData)
        Header()
        if(appData.isOwner){
            EditButton()
        }
        Body{
            for (item in deliverData.items){
                Item(item)
            }
        }
    }
```

## 声明式UI

编写代码来按照我们的想法描述UI，而不是如何转换到对应的状态。

## 组合 vs 继承

Composition（组合）指的是多个简单的代码单元如何结合到一起，从而构成更为复杂的代码单元。

```kotlin
// 定义一个接口表示点击行为
interface Clickable {
    fun onClick()
}

// 定义一个接口表示聚焦行为
interface Focusable {
    fun onFocus()
}

// 一个组件通过组合来实现这些行为
class ButtonComponent(
    private val clickable: Clickable,
    private val focusable: Focusable
) {
    @Composable
    fun Render(modifier: Modifier = Modifier) {
        Box(
            modifier = modifier
                .clip(RoundedCornerShape(10.dp))
                .background(Color.Blue)
                .clickable { clickable.onClick() }
                .focusable(enabled = true)
                .pointerInput(Unit) {
                    focusable.onFocus()
                }
        ) {
            BasicText(
                text = "Click Me",
                style = TextStyle(color = Color.White, fontSize = 16.sp)
            )
        }
    }
}

// 实现接口行为
class MyClickable : Clickable {
    override fun onClick() {
        println("Button clicked!")
    }
}

class MyFocusable : Focusable {
    override fun onFocus() {
        println("Button focused!")
    }
}

// 在Composable函数中使用
@Composable
fun AppScreen() {
    val myClickable = MyClickable()
    val myFocusable = MyFocusable()
    val button = ButtonComponent(myClickable, myFocusable)

    button.Render(
        modifier = Modifier
            .background(Color.Gray)
            .clip(RoundedCornerShape(8.dp))
    )
}
```

## 装饰类型的抽象

FancyBox是一个用于装饰其他视图的视图，本例中将用来装饰Story和EditFrom。

1. FancyBox 介绍:
   - FancyBox 是一个用于装饰其他视觉元素的组件。
   - 主要用在 FancyStory 和 EditForm 中。
2. 需求:
   - 希望结合 FancyStory 和 FancyEditForm，但不确定如何实现。
   - 讨论是否继续使用 FancyBox 或者选择 Story。
3. Composable 结构:
   - Composable 可以处理子组件。
   - 通过 Composable lambda，FancyStory 和 FancyEditForm 可以灵活组合。
   - 示例代码展示了如何在 Composable 中使用 FancyBox 以及其与其他组件的交互。

```kotlin
@Composable
fun FancyBox(children: @Composable () -> Unit) {
    // FancyBox 的实现
    Box(modifier = Modifier.padding(16.dp).background(Color.Gray)) {
        children()
    }
}

@Composable
fun Story() {
    // Story 的实现
    Text(text = "This is a story", style = MaterialTheme.typography.h6)
}

@Composable
fun FancyStory() {
    FancyBox {
        Story()
    }
}

@Composable
fun EditForm() {
    // EditForm 的实现
    TextField(value = "", onValueChange = {}, label = { Text("Edit here") })
}

@Composable
fun FancyEditForm() {
    FancyBox {
        EditForm()
    }
}

@Composable
fun MainScreen() {
    Column {
        FancyStory()
        FancyEditForm()
    }
}
```

## 重组

重组指任何Composable函数在任何时候都可以被重新调用。

1. 使用 Compose:
   - `@Composable` 注解用于标记可以被 Compose 框架管理的函数。
2. LiveData 与 State:
   - `LiveData` 是 Android 的一种数据持有者，可用于观察数据变化。
   - `observeAsState` 方法用于将 `LiveData` 转换为 Compose 的状态，方便在 UI 中使用。
3. **示例代码**:

```kotlin
fun bind(liveMsgs: LiveData<MessageData>) {
	liveMsgs.beserve(this) { msgs -> 
		updateBody(msgs)
	}
}
@Composable
fun Messages(liveMsgs: LiveData<MessageState>) {
    val msgs = liveMsgs.observeAsState()
    for (msg in msgs.value) {
        Message(msg)
    }
}
```

- 该示例展示了如何在 `Messages` 函数中使用 `LiveData`，并通过 `observeAsState` 获取状态。

1. LifecycleAware:
   - `LiveData` 会根据生命周期自动处理数据的观察，避免内存泄漏。

### 关键点

- **Compose UI 更新**: 通过观察 `LiveData`，可以自动更新 Compose UI。
- **状态管理**: `State` 在 Compose 中用于管理和响应数据变化，简化了 UI 更新的过程。

## 使用列表

![composeList (1)](https://raw.githubusercontent.com/Kennems/blog-image/main/composeList%20(1).gif)

## 自定义布局

在Compose中，界面元素由可组合函数表示，此类函数在被调用之后会发出一部分界面，这部分界面随后会被添加到呈现在屏幕上的界面树中。每个界面元素都有一个父元素，还可能有多个子元素。此外，每个元素在其父元素中都有一个位置，指定为（x, y）位置，也都有一个尺寸，指定为width和height。

### 使用布局修饰符

可以使用layout修饰符来修改元素的测量和布局方式。Layout是一个lambda；它的参数包括可以测量的元素（以measureable的形式传递）以及该可组合项的传入约束条件（以constraints的形式传递）