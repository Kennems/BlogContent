---
title : '苍穹外卖前端开发(Day1)'
date : 2024-10-26T22:30:13+08:00
lastmod: 2024-10-26T22:20:13+08:00
description : "苍穹外卖前端开发(Day1)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖前端开发(Day1)

## [node.js](https://nodejs.org/en)安装

### 配置全局下载包地址和缓存地址

```shell
npm config set prefix "E:x\nodejs\node_global"
```

```shell
npm config set cache "E:\x\nodejs\node_cache"
```

### 最新的配置淘宝镜像的淘宝官方提供的方法

```shell
npm config set registry https://registry.npmmirror.com
```

### 安装Vue

```shell
npm i @vue/cli -g
```

### 创建Vue项目

命令行创建（只能在cmd， 不能用powershell）

```shell
vue create vue-demo-1
```

用网页的形式创建

```shell
vue ui
```

## Vue目录结构

- node_modules：当前项目依赖的js包
- assets：静态资源存放目录
- components：公共组件存放目录
- App.vue：项目的主组件，页面的入口文件
- main.js：整个项目的入口文件
- package.json：项目的配置信息、依赖包管理
- vue.config.js：vue-cli配置文件

## 启动Vue项目工程

在Vue项目文件夹中：

```shell
npm run serve
```

## Vue的基本使用方式

### Vue组件

Vue的组件文件以`.vue`结尾，每个组件由三部分组成

- 结构 `<template>`
  - 只有一个根元素
  - 由它生成HTML代码
- 样式 `<style>`
  - 编写CSS，控制页面展示效果
  - 全局样式：影响所有组件
  - 局部样式：只作用于当前组件
- 逻辑 `<script>`
  - 编写JS代码
  - 控制模板的数据来源和行为

## Vue基本使用方式

### 文本插值

- 作用：用来绑定data方法返回的对象属性
- 用法：`{{}}`

先定义`data()`

```vue
<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      name: "张三",
      age: 30,
    };
  },
};
</script>
```

之后在`template`中使用

```vue
  <div class="hello">
    {{ name }}
    {{ age > 60 ? "老年" : "青年" }}
  </div>
```

### 属性绑定

- 作用：为标签的属性绑定data方法中返回的属性
- 用法：`v-bind:xxx` 简写为`:xxx`

```vue
    <input type="text" v-bind:value="name"/>
    <input type="text" :value="age"/>
    <img :src="src"/>
```

### 事件绑定

- 作用：为元素绑定对应的事件
- 用法：`v-on:xxx`，简写为`@xxx`

```vue
    <input type="button" value="保存" v-on:click="handleSave" />
    <input type="button" value="保存" @click="handleSave" />
```

```vue
<script>
export default {
  methods: {
    handleSave() {
      alert("你点击了保存按钮！")
    },
  },
};
</script>
```

### 双向绑定

- 作用：表单输入项和data方法中的属性进行绑定，任意一方改变都会同步给另一方
- 用法：`v-model`

```vue
    <input type="text" v-model="name"/>
```

### 条件渲染

- 作用：根据表达式的值来动态渲染页面元素
- 用法：v-if、v-else、v-else-if

```vue
    <div v-if="sex == 1">男</div>
    <div v-else-if="sex == 2">女</div>
    <div v-else>未知</div>
```

## Vue的基本使用方式 - axios

`Axios`是一个基于promise的网络请求库，作用于浏览器和node.js中

安装命令

```shell
npm install axios
```

导入命令：

```shell
import axios from 'axios'
```

### axios的API列表

| 请求                                 | 备注 |
| ------------------------------------ | ---- |
| `axios.get(url[, config])`           | ⭐    |
| `axios.delete(url[, config])`        |      |
| `axios.head(url[, config])`          |      |
| `axios.options(url[, config])`       |      |
| `axios.post(url[, data[, config]])`  | ⭐    |
| `axios.put(url[, data[, config]])`   |      |
| `axios.patch(url[, data[, config]])` |      |

- url：请求路径
- data：请求体数据，最常见的是JSON格式数据
- config：配置对象，可以设置查询参数，请求头信息

```vue
<script>
import axios from "axios";

export default {
  methods: {
    handleSendPOST() {
      // 通过axios发送HTTP请求
      axios
        .post("/api/admin/employee/login", {
          username: "admin",
          password: "123456",
        })
        .then((res) => {
          console.log(res.data);
        })
        .catch((error) => {
          console.log(error.response);
        });
    },
    handleSendGET() {
      // 通过 axios 发送get请求
      axios
        .get("/api/admin/shop/status", {
          headers: {
            token:
              "eyJhbGciOiJIUzI1NiJ9.eyJlbXBJZCI6MSwiZXhwIjoxNzI5OTMzMjU4fQ.JV9g9K6u4UPzdSIMzcX5crMM3VXImspm6PbvolDpkSg",
          },
        })
        .then((res) => {
          console.log(res.data);
        });
    },
  },
};
</script>
```

### Vue的基本使用方式 - axios

axios 统一使用方式 axios(config)

```vue
<script>
import axios from "axios";

export default {
    handleSend() {
      // 通过 axios 发送get请求
      axios({
        url: "/api/admin/employee/login",
        method: "post",
        data: {
          //data表示通过请求体传参
          username: "admin",
          password: "123456",
        },
      }).then((res) => {
        console.log(res.data.data.token)
        axios({
          url:'api/admin/shop/status',
          method:'get',
          headers:{
            token: res.data.data.token
          }
        }
        )
      });
    },
  },
};
</script>
```

## Vue-Router

vue属于单页面应用，所谓的路由，就是根据浏览器路径的不同，用不同的视图组件替换这个页面内容

创建Vue项目时勾选`router`选项

老项目中使用

```shell
npm install vue-router
```

### 路由配置

路由组成

- `VueRouter`：路由器，根据路由请求在路由视图中动态渲染对应的视图组件
- `<router-link>`：路由链接组件，浏览器会解析成`<a>`
- `<router-view>`：路由视图组件，用来展示与路由路径匹配的视图组件

![image-20241026154037692](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241026154037692.png)



#### `router/index.js`中指定路由表

![image-20241026160311413](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241026160311413.png)

#### App.vue中指定路由的位置

![image-20241026160352655](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241026160352655.png)

### 嵌套路由

- 嵌套路由：组件内要切换内容，就需要用到嵌套路由（子路由）

![image-20241026173705114](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241026173705114.png)

```js
// 维护路由表，某个路由路径对应哪个视图组件
const routes = [
  {
    path: '/',
    name: 'home',
    component: HomeView
  },
  {
    path: '/about',
    name: 'about',
    component: () => import('../views/AboutView.vue')
  },
  {
    path: '/404',
    component: () => import('../views/404View.vue')
  },
  {
    path: '/c',
    component: () => import('../views/container/ContainerView.vue'),
    redirect: '/c/p1',
    // 嵌套路由（子路由）
    children:[
      {
        path:'/c/p1',
        component: () => import('../views/container/P1View.vue'),
      },
      {
        path: '/c/p2',
        component: () => import('../views/container/P2View.vue'),
      },
      {
        path: '/c/p3',
        component: () => import('../views/container/P3View.vue'),
      }
    ]
  },
  {
    //重定向到404
    path: '/*',
    redirect: '/404'
  },
]
```



## 状态管理`vuex`

- vuex是一个专门为Vue.js应用程序开发的状态管理库
- vuex可以在多个组件之间共享数据，并且共享的数据是响应式的，即数据的变更能及时渲染到模板
- vuex采用集中式存储管理所有组件的状态

### vuex介绍

- state：状态对象，集中定义各个组件共享的数据
- mutations：类似于一个事件，用于修改共享数据，要求必须是同步函数
- actions：类似于mutation，可以包含异步操作，通过调用mutation来改变共享数据

### 定义和展示共享数据

```js
// 集中管理多个组件共享的数据
export default new Vuex.Store({
  // 集中定义共享数据
  state: {
    name: '未登录游客'
  },
  getters: {
  },
  mutations: {
  },
  actions: {
  },
  modules: {
  }
})
```

使用共享数据：

```vue
  <div id="app">
    欢迎你，{{$store.state.name}}
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
```

### 在mutations中定义函数，修改共享数据

```js
export default new Vuex.Store({
  state: {
  },
  getters: {
  },
  // 通过当前属性中定义的函数修改共享数据，必须都是同步操作
  mutations: {
    setName(state, newName){
      state.name = newName
    }
  },
  actions: {
  },
  modules: {
  }
})
```

#### 在vue中调用mutations中的函数

```vue
<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  methods: {
    handleUpdate(){
      // mutations中定义的函数不能直接调用，必须通过这种方式来调用
      this.$store.commit("setName", "Kennem")
    }
  },
}
</script>
```

### 在actions中定义函数，用于调用mutation

```vue
<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  methods: {
    handleUpdate() {
      // mutations中定义的函数不能直接调用，必须通过这种方式来调用
      this.$store.commit("setName", "Kennem");
    },
    handleCallAction(){
      //调用actions中定义的函数，setNameByAxios为函数名称
      this.$store.dispatch('setNameByAxios')
    }
  },
};
</script>
```

```js
// 集中管理多个组件共享的数据
export default new Vuex.Store({
  // 集中定义共享数据
  state: {
    name: '未登录游客'
  },
  getters: {
  },
  // 通过当前属性中定义的函数修改共享数据，必须都是同步操作
  mutations: {
    setName(state, newName) {
      state.name = newName
    }
  },
  actions: {
    setNameByAxios(context) {
      axios({
        url: '/api/admin/employee/login',
        method: 'post',
        data: {
          username: 'admin',
          password: '123456'
        }
      }).then(res=>{
        if(res.data.code == 1){
          // 异步请求后，需要修改共享数据
          // 在actions中调用mutations中定义的setName函数
          context.commit('setName', res.data.data.name)
        }
      })
    }
  },
  modules: {
  }
})
```

## TypeScript

```ts
function hello(msg:string){
	console.log(msg)
}
hello("123")
```

### TS 为什么要增加类型支持？

- TS属于静态类型编程语言，JS属于动态类型编程语言
- 静态类型在编译期做类型检查，动态类型在执行期做类型检查
- 对于JS来说，需要等到代码执行的时候才能发现错误（晚）
- 对于TS来说，在代码编译的时候就可以发现错误（早）
- 配合VSCode开发工具，TS可以体检到在编写代码的同时就发现代码错误，减少Bug，改Bug的时间。

### 如何理解TS？

- 是JS的超集，兼容JavaScript
- 扩展了JavaScript的语法，文件扩展名为ts
- 可以编译成标准的JavaScript，并且可以在编译时进行类型检查
- 全局安装`npm install -g typescript`
- 使用`tsc`命令将`ts`文件编译成`js`文件

| 类型       | 例                                     | 备注                         |
| ---------- | -------------------------------------- | ---------------------------- |
| 字符串类型 | string                                 |                              |
| 数字类型   | number                                 |                              |
| 布尔类型   | boolean                                |                              |
| 数组类型   | number[], string[], boolean[] 依此类推 |                              |
| 任意类型   | any                                    | 相当于又回到了没有类型的时代 |
| 复杂类型   | type 与 interface                      |                              |
| 函数类型   | () => void                             | 对函数的参数和返回值进行说明 |
| 字面量类型 | "a" \| "b" \| "c"                      | 限制变量或参数的取值         |
| class 类   | class Animal                           |                              |

### TypeScript常用类型

- 类型标注的位置
  - 标注变量
  - 标注参数
  - 标注返回值

```ts
// 字符串类型
let username: string = 'Kennem'

// 数字类型
let age: number = 20

// 布尔类型
let isTrue: boolean = true
```

- 字面量类型
  - 类似Java中的枚举，限制变量的取值

```ts
// 字面量类型
function printText(s, alignment) {
    console.log(s, alignment);
}
printText('hello', 'center');
```

- interface类型

```ts
interface Cat {
    name: string, 
    age?: number // 当前属性为可选
}

const c1: Cat = {name: 'Kennem', age:1}
const c2: Cat = {name: 'Kennem'}
```

- class类-基本使用

使用class关键字来定义类，类中可以包含属性、构造方法、普通方法

```ts
//定义一个类
class User{
    name: string
    constructor(name: string){
        this.name = name
    }

    study(){
        console.log(this.name + ' is studying');
    }
}

const user = new User('Kennem')

console.log(user.name);

user.study();
```

- class类-实现接口

```ts
interface Animal {
    name: string
    eat(): void
}

class Bird implements Animal {
    name: string

    constructor(name: string) {
        this.name = name
    }

    eat() {
        console.log(this.name + ' is eating');
    }
}

// 创建类型为bird的对象
const b1: Bird = new Bird('燕子')
console.log(b1.name);
b1.eat()
```

- class类-类的继承

```ts
class Parrot extends Bird {
    fly() {
        console.log(this.name + ' is flying');
    }
}

const myParrot: Parrot = new Parrot('鹦鹉一号')
myParrot.fly()
myParrot.eat()
```

