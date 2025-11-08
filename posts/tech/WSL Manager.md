---
title : 'WSL Manager'
date : 2025-11-01T15:40:01+08:00
lastmod: 2025-11-01T15:40:01+08:00
description : "WSL Manager" 
image : img/cat.jpg
draft : false    
categories : ["Linux"]
tags : ["Wsl"]
---

# WSL Manager

## wsl初始化

### **创建新用户**

使用以下命令创建名为 `zg` 的新用户：

```shell
sudo adduser zg
```

- 系统会提示你输入 **新密码** 和 **确认密码**。
- 接下来，你还会被要求填写一些额外信息（如全名、房间号等），这些可以按需填写，也可以直接按 Enter 跳过。

### **将用户添加到 sudo 组**（可选）

如果你希望 `zg` 用户具有管理员权限，可以将其添加到 `sudo` 组：

```shell
sudo usermod -aG sudo zg
```

### **切换到新用户**

完成创建后，你可以使用以下命令切换到 `zg` 用户：

```shell
su - zg
```

或者使用：

```shell
sudo -i -u zg
```

### **设置密码**（如果你需要更改密码）

如果你需要为用户设置或更改密码，可以使用以下命令：

```shell
sudo passwd zg
```

然后输入新密码。

### **查看用户列表**（验证）

你可以通过以下命令查看当前系统中的所有用户：

```shell
cat /etc/passwd
```

### **退出当前用户会话**

完成后，如果你要退出当前用户会话，可以使用：

```shell
exit
```