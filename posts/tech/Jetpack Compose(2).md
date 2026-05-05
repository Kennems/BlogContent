---
title : 'Jetpack Compose(2)'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Jetpack Compose(2)

## Compose中布局的目标

- 实现高性能
- 让开发者能够轻松编写自定义布局
- 在Compose中，通过避免多次测量布局子级可实现高性能。如果需要进行多次测量，Compose具有一个特殊系统，即固有特性测量。

## 标准布局组件

- 使用Column可将多个项垂直地放置在屏幕上
- 使用Row可以将多个项水平地放置在屏幕上
- 使用Box可以将一个元素放在另一个元素上

## 修饰符

修饰符的作用类似于基于视图的布局中的布局参数，借助修饰符，可以修饰或扩充可组合项。

- 可以更改组合项的大小、布局、行为和外观
- 添加信息，如无障碍标签
- 处理用户输入
- 添加高级互动，如使元素可点击，可滚动，可拖动或可缩放

由于每一个函数都会对上一个函数返回的Modifier进行更改，因此顺序会影响最终结果。

![composeLayout1](https://raw.githubusercontent.com/Kennems/blog-image/main/composeLayout1.gif)

```kotlin
@Composable
fun JetPackComposeLayouts() {
    Row(
        modifier = Modifier
            .statusBarsPadding()
            .clip(RoundedCornerShape(4.dp))
            .background(color = MaterialTheme.colors.surface)
            .clickable(onClick = {})
            .padding(16.dp)
            .padding(16.dp)
        // 添加状态栏的 padding
    ) {
        Surface(
            modifier = Modifier
                .size(50.dp)
                .fillMaxWidth(),
            shape = CircleShape,
            color = MaterialTheme.colors.onSurface.copy(alpha = 0.2f)
        ) {
            Image(
                painter = painterResource(id = R.drawable.boat),
                contentDescription = null,
                contentScale = ContentScale.Crop
            )
        }
        Column(
            modifier = Modifier
                .padding(start = 8.dp)
                .align(Alignment.CenterVertically)

        ) {
            Text(
                text = "Alfred Sisely",
                fontWeight = FontWeight.Bold
            ) 
            // 设置字体加粗
            // 前缀表达式
            // 调整透明度
            CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.medium) {
                Text(
                    text = "3 minutes ago",
                    style = MaterialTheme.typography.body2
                )
            }
        }
    }
}
```

## Slots API

Material组件大量使用**槽位AP**I，这是Compose引入的一种模式，它在可组合项之上带来一层自定义设置。这种方式使组件变得更加灵活，因为它们接受可以自行配置的子元素，而不必公开子元素的每个配置参数。**槽位会在界面中留出空白区域**，让开发者按照自己的意愿来填充。

## Scaffold

Scaffold可让我们实现具有基本Material Design布局结构的界面。Scaffold可以为最常见的顶级Material组件（如**TopAppBar**、**BottomAppBar**、**FloatingActionBottom** 和 **Drawer**）提供槽位。通过使用Scaffold，可以轻松确保这些组件得到适当放置且正确的协同工作。

<img src="https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215194351766.png" alt="image-20250215194351766" style="zoom:50%;" />

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun LayoutStudy() {
    Scaffold(
        topBar = {
            TopAppBar(
                title = {
                    Text(text = "LayoutStudy")
                },
                actions = {
                    IconButton(onClick = { }) {
                        Icon(imageVector = Icons.Filled.Favorite, contentDescription = "Favorite")
                    }
                }
            )
        }
    ) { innerPadding ->
        BodyContent(Modifier.padding(innerPadding))
    }
}

@Composable
fun BodyContent(modifier: Modifier = Modifier) {
    Column(modifier = modifier.padding(8.dp)) {
        Text(text = "Hi there!")
        Text(text = "Thanks for going through the LayoutStudy")
    }
}


```



## 使用布局修饰符



## 自定义Column



```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        // 测量所有子元素并保存测量结果
        val placeables = measurables.map { measurable ->
            measurable.measure(constraints)
        }

        // 计算总高度
        val totalHeight = placeables.sumOf { it.height }

        // 布局的尺寸可以根据需求调整，这里使用最大宽度和计算出的总高度
        layout(constraints.maxWidth, totalHeight) {
            var yPosition = 0
            placeables.forEach { placeable ->
                // 正确调用当前子元素的放置方法
                placeable.placeRelative(x = 0, y = yPosition)
                yPosition += placeable.height
            }
        }
    }
}

@Composable
fun MyOwnColumnSample() {
    JetpackComposeLayoutsTheme {
        MyOwnColumn(Modifier.padding(16.dp)) {
            Text("MyOwnColumn")
            Text(text = "places items")
            Text(text = "vertically")
            Text(text = "We've done it by hand!")
        }
    }
}
```

## StaggeredGrid

<img src="https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250517151100526.png" alt="image-20250517151100526" style="zoom:50%;" />



```kotlin
val topics = listOf(
    "Arts * Crafts",
    "Health * Wellness",
    "Food * Drinks",
    "Travel * Adventure",
    "Technology * Gadgets",
    "Fashion * Beauty",
    "Home * Garden",
    "Sports * Fitness",
    "Music * Entertainment",
    "Education * Learning",
    "Business * Finance",
    "Science * Nature",
    "Photography * Film",
    "History * Culture",
    "Parenting * Family",
    "Self * Improvement",
    "Books * Literature",
    "Pets * Animals",
    "Gaming * Esports",
    "Social * Community"
)

@Composable
fun StaggeredGrid(
    modifier: Modifier = Modifier,
    rows: Int = 3,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        val rowWidths = IntArray(rows) { 0 }
        val rowHeights = IntArray(rows) { 0 }

        val placeables = measurables.mapIndexed { index, measurable ->
            val placeable = measurable.measure(constraints)

            val row = index % rows
            rowWidths[row] += placeable.width
            rowHeights[row] = max(rowHeights[row], placeable.height)

            placeable
        }

        val width = rowWidths.maxOrNull() ?: constraints.maxWidth
        val height = rowHeights.sum()

        val rowY = IntArray(rows) { 0 }
        for (i in 1 until rows) {
            rowY[i] = rowY[i - 1] + rowHeights[i - 1]
        }

        layout(width, height) {
            val rowX = IntArray(rows) { 0 }
            // 设置每一个元素的坐标
            placeables.forEachIndexed { index, placeable ->
                // index: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
                // rows: 3
                // row: 0, 1, 2
                val row = index % rows
                placeable.placeRelative(
                    x = rowX[row],
                    y = rowY[row]
                )
                rowX[row] += placeable.width
            }
        }
    }
}

@Composable
fun Chip(
    modifier: Modifier = Modifier,
    text: String
) {
    Card(
        modifier = modifier,
        border = BorderStroke(color = Color.Black, width = Dp.Hairline),
        shape = RoundedCornerShape(8.dp)
    ) {
        Row(
            modifier = Modifier.padding(start = 8.dp, top = 4.dp, end = 8.dp, bottom = 4.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Box(
                modifier = Modifier
                    .size(16.dp, 16.dp)
                    .background(color = MaterialTheme.colorScheme.secondary)
            )
            Spacer(modifier = Modifier.width(4.dp))
            Text(text = text)
        }
    }
}

@Composable
fun StaggeredGridBodyContent(modifier: Modifier = Modifier) {
    Row(
        modifier = modifier
            .background(Color.LightGray)
            .padding(16.dp)
            .horizontalScroll(rememberScrollState()),
        content = {
            StaggeredGrid(modifier = Modifier) {
                for (topic in topics) {
                    Chip(modifier = Modifier.padding(8.dp), text = topic)
                }
            }
        }
    )
}
```



## ConstraintLayoutContent

<img src="https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250517153719992.png" alt="image-20250517153719992" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250517153736343.png" alt="image-20250517153736343" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250517153644141.png" alt="image-20250517153644141" style="zoom:50%;" />

- **引用**时使用`createRefs()`或`createRefFor()`创建的，ConstraintLayout中的每个可组合项都需要有与之关联的引用
- **约束条件**是使用`constrainAs()`修饰符提供的，该修饰符将引用作为参数，可让在主体lambda中指定其约束条件
- 约束条件是使用`linkTo()`或其他有用的方法指定的
- `parent`是一个现有的引用，可用于指定对`ConstraintLayout`可组合想本身的约束条件

```kotlin
@Composable
fun ConstraintsLayoutContent() {
    ConstraintLayout {
        val (button, text) = createRefs()
        Button(
            onClick = { /* 按钮点击事件 */ },
            modifier = Modifier.constrainAs(button) {
                top.linkTo(parent.top, margin = 16.dp)
                start.linkTo(parent.start, margin = 16.dp)
            }
        ) {
            Text(text = "Button")

        }
        Text(
            text = "Text",
            modifier = Modifier.constrainAs(text) {
                top.linkTo(button.bottom, margin = 16.dp)
                centerHorizontallyTo(parent)
            }
        )
    }
}


@Composable
fun ConstraintsLayoutContent2() {
    ConstraintLayout {
        val (button1, button2, text) = createRefs()

        Button(
            onClick = {},
            modifier = Modifier.constrainAs(button1) {
                top.linkTo(parent.top, margin = 16.dp)
            }
        ) {
            Text(text = "Button 1")
        }


        Text(
            text = "Text",
            modifier = Modifier.constrainAs(text) {
                top.linkTo(button1.bottom, margin = 16.dp)
                centerAround(button1.end)
            }
        )

        val barrier = createEndBarrier(button1, text)

        Button(
            onClick = {},
            modifier = Modifier.constrainAs(button2) {
                top.linkTo(parent.top, margin = 16.dp)
                start.linkTo(barrier)
            }
        ) {
            Text(text = "Button 2")
        }


    }
}

@Composable
fun LargeLayoutContent(){
    ConstraintLayout {
        val text = createRef()
        val guideline = createGuidelineFromStart(0.5f)
        Text(
            text = "This is long long long long long long long long long long long long long long long long long long long long long long long long  text",
            modifier = Modifier.constrainAs(text) {
                linkTo(start = guideline, end = parent.end)
                // 自动换行
                width = Dimension.preferredWrapContent
            }
        )
    }
}
```

## 解耦API

将约束布局在外部指定，适配横竖屏

![image-20250527205000712](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250527205000712.png)

![image-20250527205014402](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250527205014402.png)

```kotlin

@SuppressLint("UnusedBoxWithConstraintsScope")
@Composable
fun DecoupledConstraintLayout2(modifier: Modifier) {
    BoxWithConstraints(modifier = modifier) {
        val constraints = if (maxWidth < maxHeight) {
            decoupledConstraints(margin = 16.dp) // 竖屏
        } else {
            decoupledConstraints(margin = 160.dp) // 横屏
        }

        ConstraintLayout(constraintSet = constraints) {
            Button(
                onClick = {},
                modifier = Modifier.layoutId("button")
            ) {
                Text(text = "Button")
            }

            Text(
                text = "Text",
                modifier = Modifier.layoutId("text")
            )
        }
    }
}

private fun decoupledConstraints(margin: Dp): ConstraintSet {
    return ConstraintSet {
        val button = createRefFor("button")
        val text = createRefFor("text")
        constrain(button) {
            top.linkTo(parent.top, margin = margin)
        }
        constrain(text) {
            top.linkTo(button.bottom, margin = margin)
        }
    }
}
```

## Intrinsics

Intrinsics 允许在实际测量之前查询子项

- （min|max）IntrinsicWidth：可以正确绘制内容的最小/最大宽度是多少
- （min|max）IntrinsicHeight：可以正确绘制内容的最小/最大高度是多少

![image-20250527204010973](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250527204010973.png)



```kotlin
@Composable
fun TwoTexts(
    modifier: Modifier = Modifier
) {
    Row(modifier = modifier.height(IntrinsicSize.Min)) {
        Text(
            text = "Hi",
            modifier = Modifier
                .weight(1f)
                .padding(start = 4.dp)
                .wrapContentWidth(Alignment.Start)
        )
        Divider(
            color = Color.Black, modifier = Modifier
                .fillMaxHeight()
                .width(1.dp)
        )
        Text(
            text = "There",
            modifier = Modifier
                .weight(1f)
                .padding(start = 4.dp)
                .wrapContentWidth(Alignment.End)
        )
    }
}
```

