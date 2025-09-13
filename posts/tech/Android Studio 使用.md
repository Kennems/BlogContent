---
title : 'Android Studio 使用'
date : 2025-02-15T22:30:13+08:00
lastmod: 2024-02-15T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android Studio 使用

kotlin设置带空格的函数名

https://stackoverflow.com/questions/42485164/suppress-identifier-not-allowed-in-android



### 基本快捷键

1. **`Alt + F7`**
   - **用途**: 查找当前选中的符号的所有用法（Find Usages）。可以用于查看方法、类、变量等在代码中的引用位置。
2. **`Shift + F6`**
   - **用途**: 查找并重命名当前选中的元素（Find and Rename）。适用于重命名类、方法、变量等。
3. **`Ctrl + Shift + F`**
   - **用途**: 查找文件（Find Files）。用于在项目中快速搜索文件，支持正则表达式和文件路径筛选。
4. **`Ctrl + F`**
   - **用途**: 查找当前文件中的文本内容。常用于快速定位某个词或表达式。
5. **`Ctrl + Shift + N`**
   - **用途**: 查找并打开文件（Find and Open File）。可以通过文件名或部分文件名快速找到并打开文件。
6. **`Ctrl + Shift + A`**
   - **用途**: 查找并执行命令（Find Action）。用于快速执行任何 Android Studio 中的命令或操作。
7. **`Ctrl + B`**
   - **用途**: 跳转到定义（Go to Definition）。用于快速跳转到当前选中元素（如变量、方法、类等）的定义处。
8. **`Ctrl + Shift + I`**
   - **用途**: 查看当前选中元素的定义（Quick Definition）。用于查看变量、方法或类的定义，而不必跳转到定义的文件。
9. **`Ctrl + E`**
   - **用途**: 打开最近访问的文件（Recent Files）。通过此快捷键可以快速切换到最近打开的文件。
10. **`Ctrl + P`**

- **用途**: 显示方法参数信息（Parameter Info）。在方法调用时，按下此快捷键可以查看该方法的参数类型和顺序。

### 编辑相关快捷键

1. **`Ctrl + D`**
   - **用途**: 复制当前行或选中的代码行（Duplicate Line）。可以快速复制一行代码。
2. **`Ctrl + Y`**
   - **用途**: 删除当前行（Delete Line）。删除当前光标所在的行。
3. **`Ctrl + /`**
   - **用途**: 注释/取消注释当前行（Comment/Uncomment Line）。用于快速注释或取消注释单行代码。
4. **`Ctrl + Shift + /`**
   - **用途**: 块注释/取消块注释（Block Comment/Uncomment）。可以对选中的多行代码进行块注释或取消块注释。
5. **`Ctrl + Alt + L`**
   - **用途**: 格式化代码（Reformat Code）。根据项目的代码风格自动调整代码的格式。
6. **`Ctrl + Shift + Up/Down`**
   - **用途**: 向上/向下移动当前行（Move Line Up/Down）。可以将当前代码行向上或向下移动。
7. **`Alt + Enter`**
   - **用途**: 显示意图操作（Show Context Menu）。根据光标所在位置显示相关的快速修复建议或操作。
8. **`Ctrl + Shift + V`**
   - **用途**: 粘贴最近的剪贴板内容（Paste from History）。可以选择最近的多个剪贴板内容进行粘贴。

### 调试相关快捷键

1. **`F8`**
   - **用途**: 步过当前代码行（Step Over）。在调试时，执行当前行并跳过任何方法调用。
2. **`F7`**
   - **用途**: 步入当前方法（Step Into）。在调试时，进入当前行调用的方法中。
3. **`Shift + F8`**
   - **用途**: 步出当前方法（Step Out）。在调试时，跳出当前方法并返回到调用它的地方。
4. **`Ctrl + F5`**
   - **用途**: 重新运行调试（Rerun Debugger）。在调试时，重新启动调试会话。
5. **`Alt + F9`**
   - **用途**: 运行到光标位置（Run to Cursor）。将程序执行跳到光标所在行。

### 项目管理快捷键

1. **`Alt + 1`**
   - **用途**: 打开项目视图（Project View）。快速打开项目结构视图，查看和管理项目文件。
2. **`Alt + 4`**
   - **用途**: 打开运行窗口（Run Window）。用于查看构建、运行过程中的日志和输出。
3. **`Ctrl + Shift + F12`**
   - **用途**: 切换最大化/恢复编辑窗口（Maximize/Restore Editor）。在多个窗口之间切换编辑区域的显示状态。
