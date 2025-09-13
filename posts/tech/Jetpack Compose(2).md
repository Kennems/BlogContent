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