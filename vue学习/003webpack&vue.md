# webpack&vue

+++

### 安装的插件列表

+ npm install vue --save-dev    //vue
+ npm install vue-loader  vue-template-compiler --save-dev    //vue loader
+ npm install vue-router --save-dev    //vue-router路由包

+++



### 一.webpack&vue

#### 1.安装

> - npm install vue --save-dev    //vue
> - npm install vue-loader  vue-template-compiler --save-dev     //vue loader



#### 2.文档结构

```
webpack-demo

|- /dist

	|- boudle.js

|- /node-modeles

|- /src

	|- /components
		|- /login
			|- login.vue

	|- index.html

	|-main.js

|- package.json

|- webpack.config.js

```

#### 3.基本demo

##### webpack-config.js的配置

```
// Vue-loader在15.*之后的版本都是 vue-loader的使用都是需要伴生 VueLoaderPlugin的,
const VueLoaderPlugin = require('vue-loader/lib/plugin')

// 配置插件
    plugins: [
        new VueLoaderPlugin()
    ],
    //配置了所有第三方模块的匹配规则
    module: {
        rules: [
            { test:/\.vue$/,use:'vue-loader'}
        ]
    },
    
```

##### mian.js的配置

```
import  Vue from 'vue'

import login from './login/login.vue'

var vm = new Vue({
    el:"#app",
    data:{
        msg:"123"
    },
     //使用 **import  Vue from 'vue'** 导入的是  "main": "dist/vue.runtime.common.js"版本，不能使用传统方式方式导入到index.html中
    // render:function(createElement){
    //     return createElement(login)
    // }
    render:createElement=> createElement(login)

})
```

##### index.html配置

```
<body>
    <div id="app" v-cloak>
    </div>
</body>
```

##### login.vue

```
<template>
    <div class="login">
        <p>大声道 {{msg}}</p>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                msg:"123"
            }
        },
        methods:{
            show(){
                console.log("213123")
            }
        },
    };
</script>
```

#### 复习

##### 包的查找规则

1. 查找项目有没有node_modules文件夹
2. 在node_modules文件夹中根据包名，找到对应的vue文件
3. 在vue文件夹中，找到package.json的包配置文件
4. 在package.json中，查找一个main属性（指定了包在被加载的时候的入口文件）

==》解释了为什么使用`import  Vue from 'vue'`导入的是`vue.runtime.common.js`版本

##### 向外暴露成员变量的方法

+ Node

  ```
  //导出
  module.exports ={}
  //导入
  var xx = require('模块标识符')
  ```

+ ES6

  ```
  //导出对象(只能导出一次)
  export default{
      
  }
  //导入对象
  import xxx from '路径'

    //导出成员(多次)

    export var title = "hello"

    export var view =  "world"

    //导入成员(使用{}来进行导出)

    import {title,view} from '路径'

    console.log(title)

    console.log(view)


  ```


### 二.webpack&vue-router

#### 1.安装

  ```
npm install vue-router --save-dev    //vue-router包
  ```
#### 2.结构目录

```
webpack-demo

|- /dist

    |- boudle.js

|- /node-modeles

|- /src

    |- /components
    	|- /login
    		|- login.vue
    	|- /
    		|- register.vue
    |- index.html
    
    |-main.js
    
    |-app.vue

|- package.json

|- webpack.config.js
```
#### 3.配置

**app.vue**

```
<template>
    <div>
        <p>App</p>
        <router-link to="/login">login</router-link>
        <router-link to="/register">register</router-link>
        <router-view></router-view>
    </div>
</template>
<script>
    export default {
        name: "app"
    }
</script>
<style scoped>
</style>
```
**app.html**

```
<!doctype html>
<html lang="ch">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Title</title>
</head>
<body>
    <div id="app">
    </div>
</body>
</html>


```
**main.js**
```


// 导包

import Vue from 'vue'

import VueRouter from 'vue-router'

Vue.use(VueRouter);

// 导入组件

import app from './app.vue'

import login from './login/login.vue'

import register from './register/register.vue'

// 设置路由

var router = new VueRouter({

    routes: [

        {path: '/login', component: login},

        {path: '/register', component: register},

    ]

})

var vm = new Vue({

    el: "#app",

    // 将app组件导入到html

    render: createElement => createElement(app),

    // 将路由挂载到vm下

    router

})

```
#### 4.路由嵌套

##### 目录结构

```
webpack-demo

|- /dist

	|- boudle.js

|- /node-modeles

|- /src

	|- /components

		|- /login

			|- /name

				|- name.vue

			|- /age

				|- age.vue

			|- login.vue

		|- /

			|- register.vue

	|- index.html

	|-main.js

	|-app.vue

|- package.json

|- webpack.config.js

```
##### login.vue

```
<template>

    <div class="login">

        <h1>login {{msg}}</h1>

        <router-link to="/login/name">name</router-link>

        <router-link to="/login/age">age</router-link>

        <router-view></router-view>

    </div>

</template>

<script>

    export default {

        data(){

            return{

                msg:"123"

            }

        },

    };

</script>


```
##### main.js

```
import Vue from 'vue'

import VueRouter from 'vue-router'

Vue.use(VueRouter);

import app from './app.vue'

import login from './login/login.vue'

import register from './register/register.vue'

import email from './login/age/age.vue'

import name from './login/name/name.vue'

var router = new VueRouter({

    routes: [

        {

            path: '/login',

            component: login,

            children:[

                {path: 'age', component: age},

                {path: 'name', component: name},

            ]

        },

        {path: '/register', component: register},

    ]

})

var vm = new Vue({
    el: "#app",
    render: createElement => createElement(app),
    router
})
```
#### 5.抽离路由模块

##### 在src下面创建router.js

```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter);

import login from './login/login.vue'
import register from './register/register.vue'
import email from './login/email/email.vue'
import name from './login/name/name.vue'

var router = new VueRouter({
    routes: [
        {
            path: '/login',
            component: login,
            children:[
                {path: 'email', component: email},
                {path: 'name', component: name},
            ]
        },
        {path: '/register', component: register},
    ]
})

export  default  router
```

##### 重新写main.js

```
import Vue from 'vue'


import router from './router.js'
import app from './app.vue'


var vm = new Vue({
    el: "#app",
    render: createElement => createElement(app),
    router
})
```

### 三.样式

#### 1.在.vue的内部使用

给style添加 scoped属性(比如name.vue)

scoped  是给template 下面的div添加自定义的样式

```
<template>
    <div class="name">
        <p>name子组件</p>
    </div>
</template>

<script>
    export default {
        props: {
        },
    };
</script>
<style scoped>
    p{
        color: red;
    }
</style>
```

