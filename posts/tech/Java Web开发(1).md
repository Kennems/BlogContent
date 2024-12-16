---
title : 'Java Web开发(1)'
date : 2024-04-22T22:30:13+08:00
lastmod: 2024-04-22T22:20:13+08:00
description : "JavaWeb学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["JavaWeb学习笔记"]
tags : ["Java", "Web"]
# password : leetcode
---

# Java Web开发(1)

### 什么是Web？

- Web：全球广域网，也成为万维网（`World Wide Web`)，能够通过浏览器访问的网站

**CRM OA ERP**

不谋全局者不足以谋一域

-  前后端分离开发

- 混合开发

Web标准也成为网页标准，有一系列的标准组成，大部分由W3C（万维网联盟）负责制定

- 三个部分
  - HTML：负责网页的结果（页面元素和内容）
  - CSS：负责网页的表现（页面元素的外观，位置等页面样式，如：颜色，大小等）
  - JavaScript：负责网页的行为（交互效果）

#### 什么是HTML，CSS？

- **HTML**（HyperTextMarkupLanguage):超文本标记语言。
  - 超文本：超越了文本的限制，比普通文本更强大。
  - 标记语言：由标签构成的语言
    - HTML标签都是预定义号的。例如：使用`<a>`展示超链接，使用`<img>`展示图片，`<video>`展示视频。
    - HTML代码直接在浏览器中允许，HTML标签由浏览器解析。

标签不区分大小，单双引号都可以，语法结构比较松散

## CSS

- **CSS**（`Cascading Style Sheet`）：层叠样式表，用于控制页面的样式（表现）
  - CSS引入方式：
    - 行内样式：写在标签的`style`属性中（不推荐）
    - 内嵌样式：写在`style`标签中（可以写在页面任何位置，但通常约定写在`head`标签中）
    - 外联样式，写在一个单独的`.css`文件中（需要通过`link`标签在网页中引入）

### 颜色标识法：

- 关键字：预定义的颜色名`（red, green, blue)`
- rgb表示法：红绿蓝三原色，每项取值范围：`0~255`
- 十六进制表示法：#开头，将数字转换成十六进制表示，`#000000`， `#ff0000`, `#cccccc`， 简写为`#000`， `#ccc`

`<span>`标签

- 大量使用没有语义的布局标签
- 特点：一行可以显示多个（组合行内元素），宽度和高度默认由内容撑开

### CSS选择器

- 元素选择器：标签名{...}
- id选择器：#id属性值{...}
- 类选择器：.class属性值{...}
- 优先级：id选择器 > 类选择器 > 元素选择器

### CSS属性

- `color`， `font-size`(单位px)

超链接

- 标签：`<a>`
- 属性：`href`：制定资源访问url
- `target`:指定在何处打开资源链接
  - `_self`：默认值，在当前页面打开
  - `_blank`：在空白页面打开

CSS属性

- `text-decoration`:规定添加到文本的修饰，`none`表示定义标准的文本
- `color`：定义文本的颜色

音频，视频标签

- `<audio>` `<video>`

换行，段落标签

- `<br>`, `<p>`

文本加粗标签

- `<b>`, `<strong>`

### CSS样式

- `line-height`: 设置行高

- `text-indent`: 定义第一个行内容的缩进
- `text-align`:规定元素中的文本的水平对齐方式

html中的空格占位符` &nbsp;`

## 页面布局

- 盒子：页面中的所有元素（标签），都可以看成一个盒子， 由盒子将页面中的元素包含在一个矩形区域内，通过盒子的视角更方便的进行页面布局
- 盒子模型组成：内容区域（content）， 内边距区域（padding），边框区域（border），外边距区域(margin)

- 布局标签：实际开发网页中，会大量频繁的使用div和span这两个没有语义的布局标签
- 标签：`<div>``<span>`
- 特点：
  - `div`标签：
    - 一行只显示一个（独占一行）
    - 宽度默认是父元素的宽度，高度默认由内容撑开
    - 可以设置宽高（`width`, `height`)
  - `span`标签
    - 一行可以显示多个
    - 宽度和高度默认由内容撑开
    - 不可设置宽高（`width`, `height`)

注意：如果只需要设置某一个方位的边框，内边距，外边距，可以在属性后米娜加上`-位置`， 例如：`padding-top`, `padding-left`, `padding-right`。

### 表格标签

- 场景：在网页中以表格（行、列）形式整齐展示数据
- 标签
  - `<table>` 定义表格整体，可以包裹多个`<tr>`
    - `border`:规定表格边框的宽度
    - `width`：规定表格的宽度
    - `cellspacing`：规定单元之间的空间
  - `<tr>`表格的行，可以包裹多个`<td>`
  - `<td>`表格单元格（普通）， 可以包裹内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML-表格</title>
    <style>
        /* 样式美化 */
        td {
            text-align: center; /* 让单元格内容居中展示 */
        }
    </style>
</head>
<body>
    
    <table border="1px" cellspacing="0"  width="600px">
        <tr> 
            <!-- 表头 -->
            <th>序号</th>
            <th>品牌Logo</th>
            <th>品牌名称</th>
            <th>企业名称</th>
        </tr>
        <tr>
            <!-- 第一行数据 -->
            <td>1</td>
            <td> <img src="img/huawei.jpg" width="100px"> </td> <!-- 插入图片 -->
            <td>华为</td>
            <td>华为技术有限公司</td>
        </tr>
        <tr>
            <!-- 第二行数据 -->
            <td>2</td>
            <td> <img src="img/alibaba.jpg"  width="100px"> </td> <!-- 插入图片 -->
            <td>阿里</td>
            <td>阿里巴巴集团控股有限公司</td>
        </tr>
    </table>

</body>
</html>

```

### 表单标签

- 场景：在网页中主要负责数据采集功能，如 注册，登录等数据采集。

- 标签：`<form>`

- 表单项：不同类型的`input`元素，下俩列表，文本域等

  - `<input>`: 定义表单项，通过`type`属性控制输入形式

  - `<select>`：定义下拉列表

  - `<textarea>`：定义文本域

- 属性：
- `action`：规定当提交表单时向何处发送表单数据，URL
- `method`：规定用于发送表单数据的方式。
  - `GET`表单数据拼接在url后面， 大小有限制
  - `POST`表单数据在请求体中携带，大小没有限制

- 表单必须由`name`属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML-表单</title>
</head>
<body>
    <!-- 
    form表单属性: 
        action: 表单提交的URL，数据将会被提交到指定的URL地址。如果未指定，将默认提交到当前页面。
        method: 表单的提交方式。
            get: 将表单数据附加在URL后面，例如：?username=Tom&age=12。由于URL长度有限制，适合传递少量数据。默认值。
            post: 将表单数据包含在消息体（请求体）中传递，没有参数大小限制，适合传递大量数据。
    -->   

    <form action="" method="post">
        <!-- 输入框 -->
        用户名: <input type="text" name="username">
        年龄: <input type="text" name="age">
        性别: <input type="text" name="sex">

        <!-- 提交按钮 -->
        <input type="submit" value="提交">
    </form>

</body>
</html>

```

### 表单项

- `<input>` ：表单项：通过type属性控制输入形式。
- `<select>`：定义下拉列表，`<option>`定义列表项
- `<textarea>`：文本域

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>标题</title>
    <!-- 内嵌样式 -->
    <style>
        /* CSS样式 */
        h1 {
            color: #4d4f53; /* 设置标题颜色 */
        }
    </style>
</head>
<body>
    <div id="center">
        <!-- 页面内容 -->
        <h1>标题</h1> <!-- 标题 -->
        <hr> <!-- 水平分割线 -->
        <span id="time">2024年04月24日 16:32</span> <!-- 时间 -->
        <hr> <!-- 水平分割线 -->
        <!-- 视频和音频播放器 -->
        <video src="video\1.mp4" controls width="950px"></video>
        <audio src="audio\1.mp3" controls></audio>
        <!-- 文章内容 -->
        <p>
            标题
            <!-- 更多内容省略... -->
        </p>
        <!-- 图片 -->
        <img src="https://n.sinaimg.cn/sinakd20240424s/66/w554h312/20240424/6365-b8bea506131b4261f579f58304a45baa.png">
        <!-- 更多内容省略... -->
        <!-- 责任编辑 -->
        <p id="plast">
            责任编辑：xxx
        </p>
    </div>
</body>
</html>

```

## JavaScript

- JS是一门跨平台，面向对象的脚本语言。用来控制网页行为的，它能使网页可以交互

### JavaScript引入方式

- 内部脚本：将JS代码定义在HTMl页面中
  - JavaScript代码必须位于`<script>``</script>`标签之间
  - 在HTML文档中，可以在任意地方，放置任意数量的`<script>`
  - 一般会把脚本置于`<body>`元素的底部，可改善显示速度

- 外部脚本：将JS代码定义在外部JS文件中，然后引入到HTML页面中
  - 外部JS文件中，只包含JS代码，不包含`<script>`标签
  - `<script>`标签不能自闭和

### 书写语法：

- 区分大小写
- 每行结尾的分号可有可无
- 注释
  - 单行注释：`//注释内容`
  - 多行注释：`/* 注释内容 */`
- 大括号表示代码块

### JS输出

```js
<script>
//    alert("H")
//    /* 注释*/
    // 
    window.alert("hello js")

    document.write("hi")

    console.log("hello")

</script>
```

### 变量

- JS中使用`var`关键字来声明变量
- JS是一门弱类型语言，变量可以存放不同类型的值
- 变量名需要遵循如下规则：
  - 组成字符可以是任何字母，数字，下划线(_)或者美元符号($)
  - 数字不能开头
  - 建议使用驼峰命名

原始数据类型：

- `number`：数字（整数，小数`NaN`)
- `string` : 字符串， 单双引号皆可
- `boolean`：布尔， `true`, `false`
- `null` : 对象为空
- `undefined`：为初始化时，默认值时`undefined`

```js
<script>
// 注释示例
// 注释可以在代码中加入说明性文字，不会被执行

// 弹出对话框，显示"hello js"
window.alert("hello js");

// 在文档中写入"hi"
document.write("hi");

// 在控制台输出"hello"
console.log("hello");
</script>

<script>
// 变量声明和赋值
var a = 10;
a = "zs"; // 变量类型可以动态改变
alert(a); // 弹出"zs"

// 使用let声明的变量具有块级作用域
{
    let x = 1;
    alert(x); // 弹出"1"
}

// 使用const声明常量，常量值不可修改
const pi = 3.14;
pi = 3.15; // 会报错，常量值不可修改
alert(pi); // 弹出"3.14"
</script>

<script>
// typeof操作符用于检测变量或表达式的数据类型
alert(typeof 3); // 弹出"number"
alert(typeof 3.14); // 弹出"number"
alert(typeof "A"); // 弹出"string"
alert(typeof true); // 弹出"boolean"
alert(typeof null); // 弹出"object"，null被误判为object

var a;
alert(typeof a); // 弹出"undefined"
</script>

<script>
// 逻辑运算符 && 和 || 的使用
if (0 && NaN && 1) {
    alert("ok1"); // 不会执行，因为NaN为假
}
if (0 || NaN || 1) {
    alert("ok2"); // 执行，因为除了0和NaN外，其他都为真
}

// 空字符串和空格字符串为真，null和undefined为假
if ("") {
    alert("字符串为真");
}
if (" ") {
    alert("字符串为真");
}
if (null) {
    alert("null为真");
}
if (undefined) {
    alert("undefined为真");
}
</script>

<script>
// 函数声明和函数表达式
// function add(a, b) {
//     return a + b;
// }

var add = function (a, b) {
    return a + b;
}

var res = add(1, 2, 5, 6); // 多余的参数会被忽略
alert(res); // 弹出"3"，因为只计算了前两个参数的和
</script>
```

### JS对象

#### Array

```js
<script>
// 创建数组的两种方式：使用Array构造函数和直接使用数组字面量
// var arr = new Array(1, 2, 3, 4);
// console.log(arr); // 输出数组[1, 2, 3, 4]
// console.log(arr[1]); // 输出数组索引为1的元素值，即2

// 使用数组字面量创建数组，并添加元素
var arr = [1, 2, 3, 4];
arr[10] = 10; // 添加元素到索引为10的位置
// console.log(arr[10]); // 输出数组索引为10的元素值，即10
// console.log(arr[9]); // 输出数组索引为9的元素值，由于未定义，默认为undefined

arr[9] = "A"; // 修改索引为9的元素值为"A"
arr[8] = "a;dlsfjakldjf"; // 修改索引为8的元素值
// console.log(arr[8]); // 输出数组索引为8的元素值
// console.log(arr[9]); // 输出数组索引为9的元素值

// 遍历数组的方式：for循环、forEach方法
// for (let i = 0; i < arr.length; i++) {
//     console.log(arr[i]); // 使用for循环遍历数组元素并输出
// }

// arr.forEach(function (e) {
//     console.log(e); // 使用forEach方法遍历数组元素并输出
// });

// arr.forEach(e => {
//     console.log(e); // 使用箭头函数和forEach方法遍历数组元素并输出
// });

// 向数组末尾添加元素：push方法
arr.push(7, 8, 9);
arr.forEach(e => {
    console.log(e); // 输出添加后的数组元素
});
console.log("-----------");

// 从数组中删除元素：splice方法
arr.splice(1, 3); // 从索引为1的位置开始删除3个元素
arr.forEach(e => {
    console.log(e); // 输出删除后的数组元素
});
</script>
```

#### String

```js
// 创建一个 String 对象并赋值为 "abc"
var str1 = new String("abc");

// 直接创建一个字符串变量并赋值为 "   abccba   "
var str2 = "   abccba   ";

// 打印字符串 str1 和 str2
console.log(str1);
console.log(str2);

// 输出 str1 的长度，即字符串中字符的个数
console.log(str1.length);

// 返回 str1 中索引为 1 的字符，索引从 0 开始
console.log(str1.charAt(1));

// 返回指定字符在 str1 中第一次出现的位置，如果找不到则返回 -1
console.log(str1.indexOf("c"));

// 去除字符串 str2 开头和结尾的空白字符，并赋值给变量 s
var s = str2.trim();
console.log(s);

// 返回 str1 中索引从 0 到 4（不包括 4）的子字符串
console.log(str1.substring(0, 4));

```

### JSON

- Java Script Object Notation, JavaScript对象标记法
- JSON是通过JavaScript对象标记法书写的文本
- 由于其语法简单，层次结构鲜明，现在多用于数据载体，在网络中进行数据传输

```js
// 创建一个名为 user 的对象，包含 name、age、gender 和 intro 方法
var user = {
    name: "Tom",
    age: 10,
    gender: "male",
    intro: function () {
        alert("I am " + this.name + " and I am " + this.age + " years old.");
    }
    // 另一种定义方法的方式
    // intro() {
    //     alert("I am " + this.name + " and I am " + this.age + " years old.");
    // }
}

// 弹出 user 对象的 name 属性值
alert(user.name);

// 调用 user 对象的 intro 方法，弹出一段介绍信息
user.intro();

// 创建一个 JSON 字符串
var jsonstr = '{"name":"Tom", "age":10, "addr":["青岛", "南京"]}';

// 使用 JSON.parse() 方法解析 JSON 字符串，并将其转换为 JavaScript 对象
var obj = JSON.parse(jsonstr);

// 弹出解析后对象的 name 属性值
alert(obj.name);

// 使用 JSON.stringify() 方法将 JavaScript 对象转换为 JSON 字符串，并弹出
alert(JSON.stringify(obj));

```

### BOM

- 概念：`Browser Object Model`， 浏览器对象模型， 允许JavaScript与浏览器对话，JavaScript将浏览器的各个组成部分封装成对象。

#### Window

- 浏览器窗口对象
- 获取：直接使用`window`，其中`window.`可以成略

#### Location

- 地址栏对象
- 获取：使用window.location， 其中`window.`可以省略

```js
// 弹出一个警告框显示 "Hello BOMWINDOWS"
window.alert("Hello BOMWINDOWS");

// 弹出一个警告框显示 "Hello BOM"
alert("Hello BOM");

// 弹出一个确认框，并根据用户的选择返回一个布尔值，true 表示确认，false 表示取消
var flag = confirm("你确认删除吗？");
alert(flag);

// 设置一个间隔定时器，每隔 2000 毫秒（2 秒）执行一次匿名函数，并在控制台打印执行次数
var i = 0;
setInterval(function () {
    i += 1;
    console.log("定时器执行了:" + i + "次");
}, 2000);

// 设置一个定时器，在 2000 毫秒后弹出一个警告框显示 "Hello"
setTimeout(function () {
    alert("Hello");
}, 2000);

// 弹出一个警告框显示当前页面的 URL
alert(location.href);

// 重定向当前页面到指定 URL
location.href = "https://www.itcast.cn";

```

### DOM

- Decument Object Model, 文档对象模型
- 将标记语言的各个部分封装为对应的对象

```js
// 获取 id 为 'h1' 的元素
var img = document.getElementById('h1');
alert(img);

// 获取所有标签名为 'div' 的元素并弹出
var divs = document.getElementsByTagName('div');
for (let i = 0; i < divs.length; i++) {
    alert(divs[i]);
}

// 获取所有 name 属性为 'hobby' 的元素并弹出
var ins = document.getElementsByName("hobby");
for (let i = 0; i < ins.length; i++) {
    alert(ins[i]);
}

// 获取所有类名为 'cls' 的元素并弹出
var divs = document.getElementsByClassName('cls');
for (let i = 0; i < divs.length; i++) {
    alert(divs[i]);
}

// 重新获取类名为 'cls' 的元素集合，并取第一个元素赋值给 div1，并弹出
var divs = document.getElementsByClassName('cls');
var div1 = divs[0];
alert(div1);

// 修改 div1 的 innerHTML 属性为 "666666"
div1.innerHTML = "666666";

// 获取 id 为 'h1' 的元素并修改其 src 属性为 "img/on.gif"
var img = document.getElementById('h1');
img.src = "img/on.gif";

// 给所有标签名为 'div' 的元素追加内容 "<font color='red'>Very Good</font>"
var divs = document.getElementsByTagName('div');
for (let i = 0; i < divs.length; i++) {
    const element = divs[i];
    element.innerHTML += "<font color='red'>Very Good</font>";
}

// 获取所有 name 属性为 'hobby' 的元素，并设置它们的 checked 属性为 true
var ins = document.getElementsByName("hobby");
for (let i = 0; i < ins.length; i++) {
    const check = ins[i];
    check.checked = true;
}

```

### 小案例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JS-事件-事件绑定</title>
    <style>
        /* 样式美化 */
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }

        #light {
            width: 100px;
        }

        .button {
            padding: 8px 16px;
            margin: 5px;
            font-size: 16px;
            cursor: pointer;
        }

        .checkbox-label {
            margin-right: 10px;
        }
    </style>
</head>

<body>
    <!-- 图片标签，用于显示灯的状态 -->
    <img id="light" src="img/off.gif" alt="灯"> <br>
    <!-- 两个按钮，分别用于点亮和熄灭灯 -->
    <button class="button" id="btnOn" onclick="on()">点亮</button>
    <button class="button" id="btnOff" onclick="off()">熄灭</button>
    <!-- 文字标签 -->
    <div class="cls">黑马</div> <br>
    <br>
    <!-- 输入框，输入内容转换为大写或小写 -->
    <input type="text" id="name" value="itcast" onfocus="lower()" onblur="upper()"> <br>
    <!-- 多个复选框，用于选择爱好 -->
    <label class="checkbox-label"><input type="checkbox" name="hobby"> 电影</label>
    <label class="checkbox-label"><input type="checkbox" name="hobby"> 旅游</label>
    <label class="checkbox-label"><input type="checkbox" name="hobby"> 游戏</label>
    <br>
    <!-- 全选和反选按钮 -->
    <button class="button" id="btnCheckAll" onclick="checkAll()">全选</button>
    <button class="button" id="btnReverse" onclick="reverse()">反选</button>
</body>

<script>
    // 点亮灯函数
    function on() {
        var img = document.getElementById("light");
        img.src = "img/on.gif";
    }

    // 熄灭灯函数
    function off() {
        var img = document.getElementById("light");
        img.src = "img/off.gif";
    }

    // 将输入内容转换为大写
    function upper() {
        var input = document.getElementById("name");
        input.value = input.value.toUpperCase();
    }

    // 将输入内容转换为小写
    function lower() {
        var input = document.getElementById("name");
        input.value = input.value.toLowerCase();
    }

    // 全选复选框函数
    function checkAll() {
        var hobbys = document.getElementsByName("hobby");
        for (let i = 0; i < hobbys.length; i++) {
            const element = hobbys[i];
            element.checked = true;
        }
    }

    // 反选复选框函数
    function reverse() {
        var hobbys = document.getElementsByName("hobby");
        for (let i = 0; i < hobbys.length; i++) {
            const element = hobbys[i];
            element.checked = !element.checked; // 切换选中状态
        }
    }
</script>

</html>

```



## 
