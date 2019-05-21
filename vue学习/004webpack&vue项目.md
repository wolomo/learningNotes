#webpack&vue

+++

### 文件目录

```

webpack-app
|- /dist
	|- boudle.js
|- /node-modeles
|- /server
|- /src
	|- /component
		|- /HomeContainer
			|- HomeContainer.vue
		|- /MemberContainer
			|- HomeContainer.vue
		|- /SearchContainer
			|- HomeContainer.vue
		|- /ShopcarContainer
			|- HomeContainer.vue
	|- /css
		|- base.css
	|- /images
		|- xxx.png
	|- /lib
		|- /css
		|- /fonts
		|- /js
	|- app.vue
	|- index.html
	|- main.js
	|- router.js
|- .babelrc
|- .gitignore
|- package.json
|- webpack.config.js
```

### 依赖   -  package.json 

```
{
  "name": "ioo",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "jquery": "^3.4.1",
    "mint-ui": "^2.2.13",
    "art-template": "^4.13.2",
    "body-parser": "^1.18.3",
    "express": "^4.16.4",
    "express-art-template": "^1.0.1",
    "express-session": "^1.15.6"
  },
  "devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.5",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-env": "^1.7.0",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-stage-0": "^6.24.1",
    "css-loader": "^2.1.1",
    "file-loader": "^3.0.1",
    "html-webpack-plugin": "^3.2.0",
    "less": "^3.9.0",
    "less-loader": "^5.0.0",
    "node-sass": "^4.12.0",
    "sass-loader": "^7.1.0",
    "style-loader": "^0.23.1",
    "url-loader": "^1.1.2",
    "vue": "^2.6.10",
    "vue-loader": "^15.7.0",
    "vue-resource": "^1.5.1",
    "vue-router": "^3.0.6",
    "vue-template-compiler": "^2.6.10",
    "webpack": "^4.31.0",
    "webpack-cli": "^3.3.2",
    "webpack-dev-server": "^3.3.1"
  }
}

```

### 配置  - webpack.config.js

```
 // 引入path插件
const path = require('path');

// 布置热节点第二步骤
const wabpack = require('webpack');

// 内存中生成html页面的插件
// 会自动将打包好的bundle.js插入到页面中
const htmlWebpackPlugin = require('html-webpack-plugin');

// Vue-loader在15.*之后的版本都是 vue-loader的使用都是需要伴生 VueLoaderPlugin的.
const VueLoaderPlugin = require('vue-loader/lib/plugin');
module.exports = {
    // 入口配置
    entry: path.join(__dirname, './src/main.js'),
    // 出口配置
    output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist')
    },
    // 配置dev-server
    devServer: {
        open: true,//自动打开浏览器
        port: 3000,//指定端口
        contentBase: './src',//指定托管的目录
        hot: true,//启动热更新第一步
    },
    // 配置插件
    plugins: [
        //启用热更新的模块  启动热跟新的第三步
        // 自动追加内存中的js
        new wabpack.HotModuleReplacementPlugin() ,
       // 将html生成到内存中
        new htmlWebpackPlugin({
            template: path.join(__dirname,'./src/index.html'),
            filename: "index.html"
        }),
        //vue-loader插件
        new VueLoaderPlugin()
    ],
    /*
    *   配置了所有第三方模块的匹配规则
    *        test:正则表达式
    *        use: loader配置,从后往前调用
    *        exclude:不需要加载的部分
    *  */
    module: {
        rules: [
            //样式加载器
            { test:/\.css$/, use:['style-loader','css-loader']},
            { test:/\.less$/,use:['style-loader','css-loader','less-loader']},
            { test:/\.scss$/,use:['style-loader','css-loader','sass-loader']},
            //字体加载器
            { test: /\.(woff|woff2|eot|ttf|otf)$/, use: ['file-loader']},
            //图片加载器
            { test: /\.(png|jpe?g|gif|svg)(\?.*)?$/, use: 'url-loader'},
            //es6转es5加载器
            { test:/\.js$/,use:'babel-loader', exclude: /node_modules/},
            //.vue文件加载器
            { test:/\.vue$/,use:'vue-loader'},
        ]
    }
};
```

### 路由的书写 - router.js
```
//导入vue
import Vue from 'vue'
//导入vue-router
import VueRouter from 'vue-router'

// 将VueRouter注册到Vue
Vue.use(VueRouter);


//导入自定义组件
import HomeContainer from './component/HomeContainer/HomeContainer.vue'
import MemberContainer from './component/MemberContainer/Member.vue'
import SearchContainer from './component/SearchContainer/SearchContainer.vue'
import ShopcarContainer from './component/ShopcarContainer/ShopcarContainer.vue'


//创建路由
var router = new VueRouter({
    routes: [
        /*
        * path:页面路由路径
        * component:使用的组件
        * */
        {path: '/', redirect: '/home'},
        {path: '/home', component: HomeContainer},
        {path: '/member', component: MemberContainer},
        {path: '/shopcar', component: ShopcarContainer},
        {path: '/search', component: SearchContainer},
    ],
    //配合点击切换组件添加的自定义类
    linkActiveClass:'mui-active' //覆盖默认高亮类
})

//导出路由
export  default  router

```

### 项目入口配置 - main.js

```
// 导入vue
import  Vue  from 'vue'
// 导入app组件
import App from './app.vue';



// 导入使用mint-ui包
import MintUI from 'mint-ui'
Vue.use(MintUI)

//导入mint-ui&mui样式
import 'mint-ui/lib/style.css'
import '../src/lib/css/mui.min.css'
import '../src/lib/css/icons-extra.css'
import '../src/css/base.css'

//使用特定的组件
import { Header,Swipe, SwipeItem  } from 'mint-ui';
//使用头部
Vue.component(Header.name, Header);
//使用轮播
Vue.component(Swipe.name, Swipe);
Vue.component(SwipeItem.name, SwipeItem);


//导入自定义的router
import router from './router.js'


// 导入使用网络请求包  vue-resource
import Resource from 'vue-resource'
Vue.use(Resource)


//注册app项目
var vm = new Vue({
    el:"#app",
    //将app.vue插入到index.html
    render:c=>c(App),
    //挂载路由
    router
})
```

### 单富页面 - index.html

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
<body style="background-color:#fff;">
    <div id="app" >
    </div>
</body>
</html>
```

###主组件书写 - app.vue

```
<template>
    <div class="app-container">
        //头部
        <mt-header title="wolomo">
            <router-link to="/" slot="left">
                <mt-button icon="back">返回</mt-button>
            </router-link>
            <mt-button icon="more" slot="right"></mt-button>
        </mt-header>

        //路由部分
        <transition>
            <router-view></router-view>
        </transition>

        //tab栏部分
        <nav class="mui-bar mui-bar-tab">
            <router-link class="mui-tab-item " to="/home">
                <span class="mui-icon mui-icon-home"></span>
                <span class="mui-tab-label">首页</span>
            </router-link>
            <router-link class="mui-tab-item" to="/member">
                <span class="mui-icon mui-icon-contact"></span>
                <span class="mui-tab-label">会员</span>
            </router-link>
            <router-link class="mui-tab-item" to="/shopcar">
                <span class="mui-icon mui-icon-extra mui-icon-extra-cart"><span class="mui-badge">9</span></span>
                <span class="mui-tab-label">购物车</span>
            </router-link>
            <router-link class="mui-tab-item" to="/search">
                <span class="mui-icon mui-icon-search"></span>
                <span class="mui-tab-label">搜索</span>
            </router-link>
        </nav>
    </div>
</template>

<script>

    export default {
        name: "app"
    }
</script>

<style scoped lang="scss">
    .app-container{
        overflow-x: hidden;
    }
    .v-enter{
        opacity: 0;
        transform: translate(100%);
    }
    .v-leave-to{
        opacity: 0;
        transform: translate(-100%);
        position: absolute;
    }

    .v-enter-active,
    .v-leave-active{
        transition: all 0.5s ease
    }
</style>
```




