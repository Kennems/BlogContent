---
title : 'Firebase使用'
date : 2024-12-17T22:30:13+08:00
lastmod: 2024-12-17T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Firebase

[Firebase](https://firebase.google.com/) 

**教程**：https://firebase.google.com/codelabs/firebase-android?hl=zh-cn#1

## 依赖项

### build.gradle.kts

```kotlin
plugins {
    id("com.android.application") version "8.7.3" apply false
    id("com.android.library") version "8.7.3" apply false
    id("org.jetbrains.kotlin.android") version "2.1.0" apply false
    id("com.google.gms.google-services") version "4.4.2" apply false
}
```

### **app/build.gradle.kts**

```kotlin
dependencies{
    // Google
    implementation("com.google.android.gms:play-services-auth:20.7.0")

    // Firebase
    implementation(platform("com.google.firebase:firebase-bom:33.6.0"))
    implementation("com.google.firebase:firebase-database")
    implementation("com.google.firebase:firebase-storage")
    implementation("com.google.firebase:firebase-auth")
    // Firebase UI
    implementation("com.firebaseui:firebase-ui-auth:8.0.2")
    implementation("com.firebaseui:firebase-ui-database:8.0.2")
}
```

## 安装Firebase CLI

```kotlin
npm install -g firebase-tools
firebase login
```

## 本地启动Firebase模拟器

```kotlin
firebase emulators:start --project=demo-friendlychat-android
```

## Firebase本地调试

```kotlin
    Firebase.database.useEmulator("10.0.2.2",gg 9000)
    Firebase.auth.useEmulator("10.0.2.2", 9099)
    Firebase.storage.useEmulator("10.0.2.2", 9199)
```

在app模块下放置 `services.json`

![image-20250215150249992](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20250215150249992.png)