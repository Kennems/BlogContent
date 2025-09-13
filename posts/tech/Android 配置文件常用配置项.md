---
title : 'Android 配置文件常用配置项'
date : 2025-02-15T10:30:13+08:00
lastmod: 2025-02-15T11:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android 配置文件常用配置项

## gradle.properties 常用配置

```properties
# 启用 AndroidX 支持
# 设置为 true，项目中将使用 AndroidX 库而不是旧版支持库（Support Libraries）。
# 如果你正在迁移到 AndroidX，确保设置此项为 true。
android.useAndroidX=true

# 启用 Jetifier 工具
# 设置为 true，Jetifier 会自动将项目中使用的所有旧版 Android 支持库（Support Library）转换为 AndroidX 库。
# 如果你的项目或依赖项中包含旧版支持库，启用此项可以确保它们与 AndroidX 兼容。
android.enableJetifier=true

# 启用数据绑定 V2 版本
# 设置为 true 时，启用数据绑定的 V2 版本，这会提供更高效的编译速度和更多的功能。
# 数据绑定 V2 相较于 V1 版本，在性能和功能上进行了优化。
android.databinding.enableV2=true

# 禁用 Android 资源 ID 的动态修改
# 设置为 false 时，资源 ID 将会被标记为 final，不允许在运行时被修改。
# 默认值为 true，可以保证资源 ID 在编译时不可变。适用于大多数应用，除非有特殊需求。
android.nonFinalResIds=false

# 启用 Gradle 守护进程
# 启用守护进程可以让 Gradle 在后台持续运行，从而加快构建速度。
# 这样 Gradle 在后续构建时不需要重新启动，减少启动时间。
org.gradle.daemon=true

# 启用并行构建
# 设置为 true 时，Gradle 会并行执行多个模块的构建任务，从而提高构建效率。
# 对于多模块项目，开启此项能显著减少构建时间。
org.gradle.parallel=true

# 启用构建缓存
# 启用构建缓存时，Gradle 会缓存之前构建的输出结果，避免重复构建相同的任务。
# 这有助于加快构建速度，尤其是在大项目中。
org.gradle.caching=true

# 设置 Kotlin 代码风格为官方标准
# 默认情况下，Kotlin 会采用官方推荐的代码风格。设置此项为 official 以确保代码风格符合 Kotlin 官方的标准。
kotlin.code.style=official

# 应用的版本代码
# 版本代码是一个整数，每次发布新版本时需要增加它，通常用来标识应用的版本。
# 版本代码用于区分应用的不同版本，Google Play 会根据版本代码进行版本更新的管理。
APP_VERSION_CODE=5663

# 应用的版本名称
# 版本名称是一个字符串，通常是用户可见的版本号，用于展示给用户。
# 版本名称通常采用类似 "1.0.0" 的格式，可以包含数字和字母。
APP_VERSION_NAME=11.7.0

# 应用的包名
# 包名是应用的唯一标识符，通常采用反向域名命名规范，如 com.example.app。
# 在发布到应用商店时，包名不能更改，因此需要为每个应用选择一个唯一的包名。
APP_PACKAGE=org.telegram.messenger

# 是否为私有应用
# 设置为 true 时，表示这是一个私有应用，不会公开发布，通常用于企业内部应用或特殊用途的应用。
IS_PRIVATE=false

# 发布密钥的密码
# 用于签名 APK 文件时使用的发布密钥的密码。为了保证应用的安全，发布密钥是非常重要的。
RELEASE_KEY_PASSWORD=android

# 发布密钥别名
# 签名应用时需要指定密钥的别名。别名通常在创建密钥时设置，并且在后续的构建过程中使用。
RELEASE_KEY_ALIAS=androidkey

# 发布密钥的存储库密码
# 这是存储签名密钥的文件（通常为 .keystore 文件）的密码。
RELEASE_STORE_PASSWORD=android

# 设置 Gradle JVM 的最大堆内存
# 通过增加 JVM 堆内存的大小，可以提高 Gradle 构建的性能，特别是在大项目中。
# 这个配置项指定了 Gradle 为 JVM 分配的最大内存。这里设置为 4096MB。
org.gradle.jvmargs=-Xmx4096M

# 启用 Gradle 守护进程
# 启用守护进程可以减少构建时的启动时间，因为 Gradle 在后台保持一个持久的进程。
# 守护进程有助于提高构建效率，特别是对大型项目。
org.gradle.daemon=true

# 启用并行构建
# 启用并行构建时，Gradle 会并行执行多个模块的构建任务。
# 这有助于加快多模块项目的构建速度。
org.gradle.parallel=true

# 启用按需配置
# 设置为 false 时，Gradle 会在构建时配置所有模块。设置为 true 时，Gradle 会只配置当前构建任务所需要的模块，减少配置时间。
# 如果你有多个模块且不需要配置全部模块时，可以考虑启用按需配置以加速构建过程。
org.gradle.configureondemand=false

# 启用 AndroidX
# 启用 AndroidX 后，所有的支持库都会被替换为 AndroidX 库。建议新项目始终使用 AndroidX。
android.useAndroidX=true

# 启用 BuildConfig 生成
# 设置为 true 时，Gradle 会生成一个名为 BuildConfig 的类，它包含与构建相关的信息（如版本号、构建类型等）。
# 这在访问这些信息时非常方便，可以用于配置和调试。
android.defaults.buildfeatures.buildconfig=true

# 禁用非传递 R 类
# 设置为 false 时，Android 编译系统会为每个模块生成一个 R 类，允许模块间访问资源。
# 如果设置为 true，则该模块的 R 类不会传递给其他模块。
android.nonTransitiveRClass=false

# 禁用资源 ID 的动态修改
# 设置为 false 时，资源 ID 会被标记为 final，确保资源 ID 在编译时不可变。
# 如果你希望在运行时修改资源 ID，则可以将其设置为 true。
android.nonFinalResIds=false

# 启用 R8 完整模式
# 设置为 true 时，启用 R8 的完整模式，R8 是 Android 构建工具链中的代码压缩器和混淆器。
# 完整模式会尝试更优化地压缩和混淆代码，通常能显著减少 APK 的大小。
android.enableR8.fullMode=true
```

