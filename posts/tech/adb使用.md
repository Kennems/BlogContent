---
title : 'adb使用'
date : 2025-02-15T10:30:13+08:00
lastmod: 2025-02-15T10:40:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# adb使用

### **Android Debug Bridge (ADB) 最常用命令**

以下是开发中使用频率最高的 ADB 命令及其用途：

------

### **1. 检查设备连接**

- **`adb devices`** 列出已连接的设备及其状态。

------

### **2. 安装与卸载应用**

- **安装 APK：**

  ```shell
  adb install <apk文件路径>
  ```

  将指定 APK 文件安装到设备中。

- **卸载应用：**

  ```shell
  adb uninstall <包名>
  ```

  从设备中卸载指定包名的应用程序。

------

### **3. 文件管理**

- **从设备中拉取文件：**

  ```shell
  adb pull <设备文件路径> <本地保存路径>
  ```

  将设备中的文件传输到本地。

- **推送文件到设备：**

  ```shell
  adb push <本地文件路径> <设备保存路径>
  ```

  将本地文件传输到设备。

------

### **4. 访问设备 Shell**

- **进入设备 Shell：**

  ```shell
  adb shell
  ```

  进入设备的命令行界面。

- **运行单个命令：**

  ```shell
  adb shell <命令>
  ```

  执行指定的设备命令。例如：`adb shell ls /sdcard` 列出设备 SD 卡的文件。

------

### **5. 查看系统日志**

- **获取 Logcat：**

  ```shell
  adb logcat
  ```

  实时显示设备的日志信息，用于调试。

- **保存 Logcat 到文件：**

  ```shell
  adb logcat -d > logcat.txt
  ```

  将当前日志保存到本地文件。

------

### **6. 重启设备**

- **正常重启：**

  ```shell
  adb reboot
  ```

- **进入恢复模式：**

  ```shell
  adb reboot recovery
  ```

- **进入 Fastboot 模式：**

  ```shell
  adb reboot bootloader
  ```

------

### **7. 网络调试**

- **启用网络调试：**

  ```shell
  adb tcpip 5555
  ```

  在指定端口（如 5555）上启动设备的网络调试。

- **连接设备：**

  ```shell
  adb connect <设备IP>:<端口>
  ```

  通过 IP 地址连接设备。

------

### **8. 清除应用数据**

- **清除应用数据和缓存：**

  ```shell
  adb shell pm clear <包名>
  ```

------

### **9. 截图和录屏**

- **截图：**

  ```shell
  adb shell screencap /sdcard/screenshot.png
  ```

  截取屏幕并保存在设备中。

- **录屏：**

  ```shell
  adb shell screenrecord /sdcard/screenrecord.mp4
  ```

  录制屏幕并保存为视频。

  ```shell
  adb shell screenrecord --time-limit 120 /sdcard/screenrecord.mp4
  ```