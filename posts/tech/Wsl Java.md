---
title : 'Wsl Java'
date : 2025-10-23T10:00:01+08:00
lastmod: 2025-11-09T10:00:01+08:00
description : "Wsl初始化" 
image : img/cat.jpg
draft : false    
categories : ["Linux"]
tags : ["Wsl"]
---

# Wsl Java

## 安装多个 Java 版本

### 使用 apt/yum 官方仓库（推荐）

以 Ubuntu 为例：

```shell
sudo apt update
sudo apt install openjdk-8-jdk openjdk-11-jdk openjdk-17-jdk -y
```

安装后，你的系统会有多个版本，例如：

```shell
/usr/lib/jvm/java-8-openjdk-amd64
/usr/lib/jvm/java-11-openjdk-amd64
/usr/lib/jvm/java-17-openjdk-amd64
```

## 1、使用 update-alternatives（官方推荐切换方案）

`update-alternatives` 是 Ubuntu/Debian 下管理多版本工具的标准方式。

#### 添加各版本：

```shell
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-8-openjdk-amd64/bin/java 1080
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-11-openjdk-amd64/bin/java 1110
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-17-openjdk-amd64/bin/java 1170
```

#### 交互式切换：

```shell
sudo update-alternatives --config java
```

输出示例：

```
There are 3 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                             Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-17-openjdk-amd64/bin/java      1170      auto mode
  1            /usr/lib/jvm/java-8-openjdk-amd64/bin/java       1080      manual mode
  2            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1110      manual mode
  3            /usr/lib/jvm/java-17-openjdk-amd64/bin/java      1170      manual mode
```

输入编号即可切换。

同样操作也可以用于 `javac`：

```shell
sudo update-alternatives --config javac
```

------

## 2、为项目设置独立 Java 版本

有时你希望**不同项目使用不同 Java**：

```shell
# 在项目 A 下使用 Java 8
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
mvn clean install

# 在项目 B 下使用 Java 17
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
gradle build
```

或写成脚本 `run_with_java8.sh`：

```shell
#!/bin/bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
java -version
mvn clean install
```

------

## 验证当前配置

```shell
echo $JAVA_HOME
java -version
javac -version
```

