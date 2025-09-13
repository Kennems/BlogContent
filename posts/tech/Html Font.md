---
title : 'HTML 字体'
date : 2025-03-16T10:30:13+08:00
lastmod: 2025-03-16T10:40:13+08:00
description : "HTML 字体" 
categories : ["HTML 字体"]
tags : ["Web"]
---

# HTML 字体

本文件中，每个示例只显示描述的字体效果，而不是完整的 HTML 文档结构。这样你就可以直观地看到对应字体的显示效果。

## 1. 通用字体家族

### 1.1 Serif（衬线字体）
衬线字体的特点是在字母笔画的末端带有小装饰线，给人以传统和正式的感觉。  
**常用示例**：`Times New Roman`、`Georgia`、`Garamond`

#### 示例效果
<div style="font-family: 'Times New Roman', Georgia, serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用衬线字体（Serif）的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Times New Roman', Georgia, serif;">
  这是一段使用衬线字体（Serif）的示例文本。
</div>
```

---

### 1.2 Sans-serif（无衬线字体）
无衬线字体没有装饰线条，线条简洁，常用于屏幕显示，现代感强。  
**常用示例**：`Arial`、`Helvetica`、`Verdana`、`Trebuchet MS`

#### 示例效果
<div style="font-family: Arial, Helvetica, sans-serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用无衬线字体（Sans-serif）的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: Arial, Helvetica, sans-serif;">
  这是一段使用无衬线字体（Sans-serif）的示例文本。
</div>
```

---

### 1.3 Monospace（等宽字体）
等宽字体中每个字符占用相同宽度，常用于代码展示和排版。  
**常用示例**：`Courier New`、`Consolas`、`Lucida Console`

#### 示例效果
<div style="font-family: 'Courier New', Consolas, monospace; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用等宽字体（Monospace）的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Courier New', Consolas, monospace;">
  这是一段使用等宽字体（Monospace）的示例文本。
</div>
```

---

### 1.4 Cursive（手写体）
手写体模拟自然手写风格，通常具有较强的装饰性。  
**常用示例**：`Comic Sans MS`、`Brush Script MT`

#### 示例效果
<div style="font-family: 'Comic Sans MS', 'Brush Script MT', cursive; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用手写体（Cursive）的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Comic Sans MS', 'Brush Script MT', cursive;">
  这是一段使用手写体（Cursive）的示例文本。
</div>
```

---

### 1.5 Fantasy（装饰字体）
装饰字体设计独特、个性鲜明，多用于标题或特殊场合。  
**常用示例**：`Impact`、`Papyrus`

#### 示例效果
<div style="font-family: Impact, Papyrus, fantasy; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用装饰字体（Fantasy）的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: Impact, Papyrus, fantasy;">
  这是一段使用装饰字体（Fantasy）的示例文本。
</div>
```

---

## 2. 常见中文字体

### 2.1 Windows 系统常用中文字体
**常见字体**：  

- **微软雅黑**（Microsoft YaHei）  
- **宋体**（SimSun）  
- **黑体**（SimHei）  
- **楷体**（KaiTi）  
- **仿宋**（FangSong）

#### 示例效果（Windows）
<div style="font-family: 'Microsoft YaHei', '微软雅黑', SimSun, serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用 Windows 系统常用中文字体的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Microsoft YaHei', '微软雅黑', SimSun, serif;">
  这是一段使用 Windows 系统常用中文字体的示例文本。
</div>
```

---

### 2.2 Mac 系统常用中文字体
**常见字体**：  
- **华文细黑**（STXihei）  
- **华文楷体**（STKaiti）  
- **华文宋体**（STSong）

#### 示例效果（Mac）
<div style="font-family: 'STXihei', '华文细黑', 'STKaiti', '华文楷体', 'STSong', '华文宋体', serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用 Mac 系统常用中文字体的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'STXihei', '华文细黑', 'STKaiti', '华文楷体', 'STSong', '华文宋体', serif;">
  这是一段使用 Mac 系统常用中文字体的示例文本。
</div>
```

---

## 3. 使用 Web 字体

通过第三方服务加载 Web 字体，这里以 Google Fonts 的 Roboto 字体为例。

#### 示例效果（Web 字体）
<div style="font-family: 'Roboto', Arial, sans-serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用 Google Fonts 加载的 Roboto 字体的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Roboto', Arial, sans-serif;">
  这是一段使用 Google Fonts 加载的 Roboto 字体的示例文本。
</div>
```

---

## 4. 更多 Web 字体

### 4.1 Open Sans（Google Fonts）
一种极受欢迎的无衬线字体，具有良好的可读性。

#### 示例效果（Open Sans）
<div style="font-family: 'Open Sans', Arial, sans-serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用 Google Fonts 加载的 Open Sans 字体的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Open Sans', Arial, sans-serif;">
  这是一段使用 Google Fonts 加载的 Open Sans 字体的示例文本。
</div>
```

---

### 4.2 Lato（Google Fonts）
Lato 字体线条简洁，现代感强，同样适用于网页正文和标题。

#### 示例效果（Lato）
<div style="font-family: 'Lato', Arial, sans-serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用 Google Fonts 加载的 Lato 字体的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Lato', Arial, sans-serif;">
  这是一段使用 Google Fonts 加载的 Lato 字体的示例文本。
</div>
```

---

### 4.3 Montserrat（Google Fonts）
Montserrat 具有较强的几何感和现代感，非常适合标题展示。

#### 示例效果（Montserrat）
<div style="font-family: 'Montserrat', Arial, sans-serif; border:1px solid #ccc; padding:10px; margin-bottom:10px;">
  这是一段使用 Google Fonts 加载的 Montserrat 字体的示例文本。
</div>

#### 示例代码
```html
<div style="font-family: 'Montserrat', Arial, sans-serif;">
  这是一段使用 Google Fonts 加载的 Montserrat 字体的示例文本。
</div>
```

---

## 总结

- **HTML 字体选择**：通过 CSS 的 `font-family` 属性指定字体，采用字体堆栈策略确保在不同系统上有良好显示。
- **通用字体家族**：包括 Serif、Sans-serif、Monospace、Cursive、Fantasy，每种字体具有独特风格。
- **中英文混排**：针对中文网页建议设置多个中文字体备选；英文网页则使用 Web Safe Fonts 或 Web 字体服务。
- **Web 字体加载**：使用 `@font-face` 或第三方服务（如 Google Fonts）可以加载不依赖于系统预装的字体，保证设计风格统一。

