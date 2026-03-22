---
title : 'Wsl'
date : 2025-10-23T15:40:01+08:00
lastmod: 2025-10-23T15:40:01+08:00
description : "Wsl初始化" 
image : img/cat.jpg
draft : false    
categories : ["Linux"]
tags : ["Wsl"]
---

# Wsl

## 初始化 su 用户（WSL）

WSL 默认未启用 root 密码，可直接使用 `sudo` 获得管理员权限。

### 方法 1：直接切到 root（推荐）

```bash
sudo su
# 或
sudo -i
```

------

### 方法 2：设置 root 密码（启用 su）

```bash
sudo su
passwd   # 设置 root 密码
exit
su       # 输入新密码登录 root
```

------

### 方法 3：设置默认登录用户为 root

查看发行版名：

```bash
wsl -l -v
```

设置：

```bash
ubuntu config --default-user root
```

恢复：

```bash
ubuntu config --default-user kennem
```

## 更新系统基础软件源（Ubuntu官方→国内源）

### 1. 备份原有软件源

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

### 2. 修改为国内镜像源（以阿里云为例）

编辑：

```bash
sudo vim /etc/apt/sources.list
```

替换内容为（Ubuntu 22.04 示例）：

```bash
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
```

保存并更新：

```bash
sudo apt update && sudo apt upgrade -y
```

