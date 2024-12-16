---
title : 'Java Web开发(2)'
date : 2024-04-22T22:30:13+08:00
lastmod: 2024-04-22T22:20:13+08:00
description : "JavaWeb学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["JavaWeb学习笔记"]
tags : ["Java", "Web"]
# password : leetcode
---

# Java Web开发(2)

## Vue

- Vue是一套前端框架，免除原生的`JavaScript`中的`DOM`操作，简化书写。
- 基于MVVM（`Model-View-View-Model`）思想，实现数据的双向绑定，将编程的关注点放在数据上。

```vue
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue</title>
    <!-- 引入Vue.js库 -->
    <script src="../js/vue.js"></script>
</head>

<body>

    <div id="app">
        <!-- 输入框，使用v-model指令实现双向数据绑定 -->
        <input type="text" v-model="message">
        <!-- 使用插值表达式显示message变量的值 -->
        {{ message }}
    </div>
</body>

<script>
    // 创建一个Vue实例
    new Vue({
        // 将Vue实例挂载到id为"app"的元素上
        el: "#app",
        // 定义数据对象
        data: {
            message: "Hello Vue" // 初始值为"Hello Vue"
        }
    })
</script>

</html>
```

### Vue常用指令

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue</title>
    <!-- 引入 Vue.js 文件 -->
    <script src="../js/vue.js"></script>
</head>

<body>

    <!-- Vue 实例挂载的根节点 -->
    <div id="app">
        <table border="1" cellspacing="0" width="60%">
            <tr>
                <th>编号</th>
                <th>姓名</th>
                <th>年龄</th>
                <th>性别</th>
                <th>成绩</th>
                <th>等级</th>
            </tr>
            <!-- 使用 v-for 循环渲染列表 -->
            <tr align="center" v-for="(user, index) in users">
                <td>{{index + 1}}</td>
                <td>{{user.name}}</td>
                <td>{{user.age}}</td>
                <td>
                    <!-- 使用 v-if 条件渲染 -->
                    <span v-if="user.gender == 1">男</span>
                    <span v-if="user.gender == 2">女</span>
                </td>

                <td>{{user.score}}</td>

                <td>
                    <!-- 使用 v-if、v-else-if、v-else 实现多条件渲染 -->
                    <span v-if="user.score >= 60 && user.score < 85">优秀</span>
                    <span v-else-if="user.score >= 85">及格</span>
                    <span style="color:red" v-else>不及格</span>
                </td>

            </tr>
    </div>
</body>

<script>
    // 创建 Vue 实例
    new Vue({
        el: "#app", // 指定实例挂载的根节点
        data: {
            // 用于存储数据的对象
            users: [{
                name: "Tom",
                age: 20,
                gender: 1,
                score: 30
            }, {
                name: "Rose",
                age: 18,
                gender: 2,
                score: 99
            }, {
                name: "Jerry",
                age: 16,
                gender: 1,
                score: 98
            }, {
                name: "Kennem",
                age: 21,
                gender: 1,
                score: 100
            }]
        },

    })
</script>

</html>
~~~

### Vue生命周期

```js
    new new Vue({
        el: "#app", //接管区域
        data: {

        },
        methods: {

        },
        mounted() {
            alert("挂载完成!");
        },
    })
```

## Ajax

- `Asynchronous JavaScript And XML`, 异步的`JavaScript`和`XML`
- 作用：
  - **数据交换**：通过`Ajax`可以给服务器发送请求，并获取服务器响应的数据。
  - **异步交互**：可以在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术，如：搜索联想，用户名是否可用的校验等。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>原生Ajax</title>
</head>

<body>

    <input type="button" value="获取数据" onclick="getData()">

    <div id="div1"></div>

</body>
<script>
    function getData() {
        // 创建XMLHttpRequest对象
        var xmlHttpRequest = new XMLHttpRequest();

        // 发送异步请求
        xmlHttpRequest.open('GET', 'https://xxx/json_demo.json');
        xmlHttpRequest.send(); // 发送请求

        // 获取服务响应数据
        xmlHttpRequest.onreadystatechange = function () {
            if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                document.getElementById('div1').innerHTML = xmlHttpRequest.responseText;
            }
        }
    }
</script>

</html>
```

## Axios

- Axios对原生的Ajax进行了封装，简化书写，快速开发。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax-Axios-案例</title>
    <!-- 引入axios库 -->
    <script src="js/axios-0.18.0.js"></script>
    <!-- 引入Vue.js -->
    <script src="js/vue.js"></script>
</head>
<body>
    <div id="app">
        <!-- 数据展示表格 -->
        <table border="1" cellspacing="0" width="60%">
            <!-- 表头 -->
            <tr>
                <th>编号</th>
                <th>姓名</th>
                <th>图像</th>
                <th>性别</th>
                <th>职位</th>
                <th>入职日期</th>
                <th>最后操作时间</th>
            </tr>

            <!-- 数据行，使用Vue.js的v-for指令循环渲染 -->
            <tr align="center" v-for="(emp,index) in emps">
                <td>{{index + 1}}</td> <!-- 显示编号 -->
                <td>{{emp.name}}</td> <!-- 显示姓名 -->
                <td>
                    <img :src="emp.image" width="70px" height="50px"> <!-- 显示图像 -->
                </td>
                <td>
                    <span v-if="emp.gender == 1">男</span> <!-- 根据性别显示男或女 -->
                    <span v-if="emp.gender == 2">女</span>
                </td>
                <td>{{emp.job}}</td> <!-- 显示职位 -->
                <td>{{emp.entrydate}}</td> <!-- 显示入职日期 -->
                <td>{{emp.updatetime}}</td> <!-- 显示最后操作时间 -->
            </tr>
        </table>
    </div>
</body>
<script>
    new Vue({
       el: "#app",
       data: {
         emps: [] // 存储员工数据的数组
       },
       mounted () {
          // 在Vue实例mounted生命周期钩子中发送异步请求加载数据
          axios.get("http://yapi.smart-xwork.cn/mock/169327/emp/list").then(result => {
            // 异步请求成功后将数据赋值给emps数组
            this.emps = result.data.data;
          })
       }
    });
</script>
</html>
```

## 接口管理平台

[Yapi](https://yapi.pro/) [apifox](https://app.apifox.com/main/teams/2527014?tab=project)

作为测试接口

## Vue开发

### Vue安装

```bash
npm -g install vue/cli
```

### vue 项目创建

```bash
vue create project-name
```

- Vue的组件文件以.vue结尾， 每个组件由三个部分组成`<template>`, `<script>`, `<style>`。

### Element UI

```bash
npm install element-ui@2.15.3
```

```js
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
```

```js
Vue.use(ElementUI);
```

### Vue路由

- **前端路由**：URL中的hash(#)与组件之间的对应关系。

```
https://mock.apifox.cn/m1/3128855-0-default/emp/list
```

## Nginx

处理被占用的端口号：

```bash
netstat -ano | findStr 80
```

将测试好的前端网页复制到`html`静态资源中

![image-20240427140541969](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240427140541969.png)

然后直接启动nginx即可。

访问：

```url
http://localhost:80
```

## 员工管理项目

### main.js

```js
// 引入Vue框架
import Vue from 'vue'

// 引入ElementUI组件库
import ElementUI from 'element-ui';

// 引入Vue Router，用于管理页面路由
import router from './router'

// 引入ElementUI的样式文件
import 'element-ui/lib/theme-chalk/index.css';

// 引入根组件App.vue
import App from './App.vue'

// 在Vue中使用ElementUI
Vue.use(ElementUI);

// 关闭生产模式下的提示
Vue.config.productionTip = false

// 创建Vue实例
new Vue({
  // 注入路由
  router,
  // 渲染根组件
  render: h => h(App),
}).$mount('#app')
```

### app.vue

```vue
<template>
  <div id="app">
    <!-- <emp-view>
    </emp-view> -->
    <!-- 这是 Vue Router 的视图渲染出口，用于展示当前匹配的视图组件 -->
    <router-view>
    </router-view>
  </div>
</template>

<script>
// 导入组件
// import EmpView from './views/tlias/EmpView.vue'

export default {
  // 注册组件
  components: { /*EmpView*/ },
  data() {
    // 组件的数据属性，可以在模板中使用
    return {
      message: ""
    }
  },
  methods: {
    // 组件的方法，用于定义各种功能
  },
}
</script>

<style>
/* 组件的样式 */
#app {
  font-family: Consolas, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

### EmpView.vue

```vue
<template>
    <div>
        <!-- Element UI 布局容器 -->
        <el-container style="height: 700px; border: 2px solid #eee">
            <!-- 头部区域 -->
            <el-header
                style="font-size:  48px; background-color: rgb(238, 241, 246); text-align: left;">Tlias智能学习辅助系统</el-header>
            <el-container>
                <!-- 侧边栏区域 -->
                <el-aside width="230px" style=" border: 2px solid #eee">
                    <!-- 侧边栏导航菜单 -->
                    <el-menu :default-openeds="['1', '3']">
                        <el-submenu index="1">
                            <template slot="title"><i class="el-icon-message"></i>系统信息管理</template>
                            <el-menu-item-group>
                                <!-- 导航链接 -->
                                <el-menu-item index="1-1">
                                    <router-link to="/dept">部门管理</router-link>
                                </el-menu-item>
                                <el-menu-item index="1-2">
                                    <router-link to="/emp">员工管理</router-link>
                                </el-menu-item>
                            </el-menu-item-group>
                        </el-submenu>
                    </el-menu>
                </el-aside>

                <!-- 主体区域 -->
                <el-main>
                    <!-- 查询表单 -->
                    <el-form :inline="true" :model="searchForm" class="demo-form-inline">
                        <el-form-item label="姓名">
                            <el-input v-model="searchForm.name" placeholder="输入姓名"></el-input>
                        </el-form-item>
                        <el-form-item label="性别">
                            <el-select v-model="searchForm.gender" placeholder="输入性别">
                                <el-option label="男" value="1"></el-option>
                                <el-option label="女" value="2"></el-option>
                            </el-select>
                        </el-form-item>
                        <el-form-item label="入职时间">
                            <el-date-picker v-model="searchForm.entrydate" type="daterange" range-separator="至"
                                start-placeholder="开始日期" end-placeholder="结束日期">
                            </el-date-picker>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="onSubmit">查询</el-button>
                        </el-form-item>
                    </el-form>

                    <!-- 表格 -->
                    <el-table :data="tableData" border>
                        <el-table-column prop="name" label="姓名" width="180">
                        </el-table-column>
                        <el-table-column prop="image" label="图像" width="180">
                            <!-- 作用域插槽 -->
                            <template slot-scope="scope">
                                <img :src="scope.row.image" width="100px" height="70px">
                            </template>
                        </el-table-column>
                        <el-table-column prop="gender" label="性别" width="140">
                            <!-- 作用域插槽 -->
                            <template slot-scope="scope">
                                {{ scope.row.gender == 1 ? "男" : "女" }}
                            </template>
                        </el-table-column>
                        <el-table-column prop="job" label="职位" width="140">
                        </el-table-column>
                        <el-table-column prop="entrydate" label="入职日期" width="180">
                        </el-table-column>
                        <el-table-column prop="updatetime" label="最后操作时间" width="230">
                        </el-table-column>
                        <el-table-column label="操作">
                            <el-button type="primary" size="mini">编辑</el-button>
                            <el-button type="danger" size="mini">删除</el-button>
                        </el-table-column>
                    </el-table>

                    <!-- 分页条 -->
                    <el-pagination background layout="sizes, prev, pager, next, jumper, total"
                        @size-change="handleSizeChange" @current-change="handleCurrentChange"
                        :total="1000"></el-pagination>
                </el-main>
            </el-container>
        </el-container>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            // 表格数据
            tableData: [],
            // 查询表单数据
            searchForm: {
                name: "",
                gender: ""
            },
            // 入职日期范围
            entrydate: []
        }
    },
    methods: {
        // 查询按钮点击事件
        onsubmit() {
            alert("查询数据！");
        },
        // 分页当前页改变事件
        handleCurrentChange() {
            alert("CurrentChange");
        },
        // 分页每页条数改变事件
        handleSizeChange() {
            alert("SizeChange");
        }
    },
    mounted() {
        // 组件初始化时从接口获取数据
        axios.get("https://mock.apifox.cn/m1/3128855-0-default/emp/list").then((result) => {
            this.tableData = result.data.data;
        });
    },
}
</script>

<style></style>
```

### DeptView.vue

```vue
<template>
    <div>
        <!-- Element UI 布局容器 -->
        <el-container style="height: 700px; border: 1px solid #eee">
            <!-- 头部区域 -->
            <el-header style="font-size:40px; background-color: rgb(238, 241, 246)">tlias 智能学习辅助系统</el-header>
            <el-container>
                <!-- 侧边栏区域 -->
                <el-aside width="230px" style="border: 1px solid #eee">
                    <!-- 侧边栏导航菜单 -->
                    <el-menu :default-openeds="['1', '3']">
                        <el-submenu index="1">
                            <template slot="title"><i class="el-icon-message"></i>系统信息管理</template>
                            <!-- 导航链接 -->
                            <el-menu-item index="1-1">
                                <router-link to="/dept">部门管理</router-link>
                            </el-menu-item>
                            <el-menu-item index="1-2">
                                <router-link to="/emp">员工管理</router-link>
                            </el-menu-item>
                        </el-submenu>
                    </el-menu>
                </el-aside>

                <!-- 主体区域 -->
                <el-main>
                    <!-- 表格 -->
                    <el-table :data="tableData" border>
                        <el-table-column prop="name" label="名称" width="250"></el-table-column>
                        <el-table-column prop="updatetime" label="最后操作时间" width="250"></el-table-column>
                        <el-table-column label="操作">
                            <el-button type="primary" size="mini">编辑</el-button>
                            <el-button type="danger" size="mini">删除</el-button>
                        </el-table-column>
                    </el-table>
                </el-main>
            </el-container>
        </el-container>
    </div>
</template>

<script>
export default {
    data() {
        return {
            // 表格数据
            tableData: [{
                id: 1,
                name: "学工部",
                updatetime: "2010-01-01 12:00:00"
            }, {
                id: 2,
                name: "教研部",
                updatetime: "2010-01-01 12:00:00"
            }, {
                id: 3,
                name: "就业部",
                updatetime: "2010-01-01 12:00:00"
            }, {
                id: 4,
                name: "人事部",
                updatetime: "2010-01-01 12:00:00"
            }, {
                id: 5,
                name: "行政部",
                updatetime: "2010-01-01 12:00:00"
            }]
        }
    },
    methods: {
        // 可以在这里定义组件的方法
    }
}
</script>

<style></style>
```

### index.js

```js
import Vue from 'vue'
// 导入 Vue Router
import VueRouter from 'vue-router'

// 使用 Vue Router 插件
Vue.use(VueRouter)

// 定义路由规则
const routes = [{
        // 路由路径为 /emp
        path: '/emp',
        // 路由名称为 emp
        name: 'emp',
        // 使用动态导入延迟加载组件
        component: () => import('../views/tlias/EmpView.vue')
    },
    {
        // 路由路径为 /dept
        path: '/dept',
        // 路由名称为 dept
        name: 'dept',
        // 使用动态导入延迟加载组件
        component: () => import('../views/tlias/DeptView.vue')
    },
    {
        // 路由路径为 /
        path: '/',
        // 重定向到 /dept
        redirect: '/dept' //默认访问dept 
    }
]

// 创建路由实例
const router = new VueRouter({
    routes
})

// 导出路由实例
export default router
```

