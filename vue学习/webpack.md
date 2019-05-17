# webpack

### 网页中的静态资源

JS：

- .js .jsx .coffee .ts

CSS: 

- css  less  scss(.sass)

Image

- jpg png gif bmp

字体文件

- svg ttf eot woff

模板文件

- ejs jade vue（推荐）

### webpack解决的问题

+ 加快网页加载速度


+ 处理错综复杂的依赖关系



> 官网 <https://webpack.js.org/>

+++

### 安装的插件列表

+ npm install webpack --save-dev    //webpack
+ npm install  webpack-cli --save-dev //webpack脚手架（webpack4.0以后需要安装的）
+ npm install  webpack-dev-server --save-dev   //将HTML加载到内存中的插件
+ npm i html-webpack-plugin --save-dev  //webpack服务器插件
+ npm i style-loader css-loader  --save-dev //加载.css样式的插件
+ npm i less-loader less  --save-dev  //加载.less样式的插件
+ npm i node-sass  sass-loader  --save-dev //加载scss(sass)样式的插件
+ ​

+++







###一.安装和使用

#### 1.安装

> npm安装（4.0版本以上需要安装webpack-cli）
>
> npm install webpack --save-dev
> npm install  webpack-cli --save-dev



#### 2.创建文档结构

```
webpack-demo

|- /dist

	|- boudle.js

|- /node-modeles

|- /src

	|- /css

	|- /js

	|- index.html

	|-main.js

|- package.json

|- webpack.config.js

```

####3.基本demo

##### webpack-config.js的配置

>npm install  webpack-dev-server --save-dev   //将HTML加载到内存中的插件
>
>npm i html-webpack-plugin --save-dev  //webpack服务器插件
>
>npm i style-loader css-loader  --save-dev //加载.css样式的插件
>
>npm i less-loader less  --save-dev  //加载.less样式的插件
>
>npm i node-sass  sass-loader  --save-dev //加载scss(sass)样式的插件

```
// 引入path插件
const path = require('path');
// 布置热节点第二步骤
const wabpack = require('webpack');

// 内存中生成html页面的插件
//自动将打包好的bundle.js插入到页面中
const htmlWebpackPlugin = require('html-webpack-plugin');

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
        //启用热更新的模块  启动热更新的第三步
        new wabpack.HotModuleReplacementPlugin() ,
       // 将html生成到内存中
        // 自动追加内存中的js
        new htmlWebpackPlugin({
            template: path.join(__dirname,'./src/index.html'),
            filename: "index.html"
        })
    ],
    //配置了所有第三方模块的匹配规则
    module: {
        rules: [
            {   //正则表达式
                test:/\.css$/,
                // 从后往前调用
                // 先使用css-loader处理 .css文件类型，再调用style-loader，最后交给webpack
                use:['style-loader','css-loader']
            },
            {est:/\.less$/,use:['style-loader','css-loader','less-loader']},
            { test:/\.scss$/,use:['style-loader','css-loader','sass-loader']}
        ]
    }
};
```

##### package.json的配置

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server"
  },
```

##### mian.js的配置

```
//调用jquery
import  $ from "jquery"

// 默认只能处理js文件，需要手动安装第三方加载器加载css
import  './css/index.css'
import  './css/index.less'
import  './css/index.scss'


$(function () {
    $('li:odd').css('backgroundColor','yellow')
})
```

#####index.html配置

```
<!doctype html>
<html lang="ch">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Title</title>
<!--    <script src="bundle.js"></script>-->
<!--    <link rel="stylesheet" href="css/index.css">-->
</head>
<body>
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
    <li>7</li>
    <li>8</li>
    <li>9</li>
    <li>10</li>
</ul>
</body>
</html>
```

### 二.管理资源

#### 1.加载css

```
npm install style-loader css-loader --save-dev
```

**webpack.config.js**

```
module: {
   rules: [
   	{
    	test: /\.css$/,
         use: [
           'style-loader',
           'css-loader'
         ]
       }
     ]
   }
  };
```

#### 2.加载图片

```
npm install file-loader --save-dev
或者
npm install url-loader --save-dev
```

**webpack.config.js**

```
module: {
	rules:[
			{test: /\.(png|svg|jpg|gif)$/,use: ['url-loader']}
		]
	}
};

```

```
module: {
	rules:[
	//limit  大小  
	//更改name值改变在内存中的名字，避免重复
			{test: /\.(png|svg|jpg|gif)$/,use: ['url-loader?limit=7631&name=[hash:8]-[name].[ext]']}
		]
	}
};
```

#### 3.加载字体

```
module: {
	rules:[
			{test: /\.(woff|woff2|eot|ttf|otf)$/,use: ['file-loader']}
		]
	}
};
```

#### 4....

#### 5.ES6 转ES5

```
//转换器
npm i babel-core babel-loader babel-plugin-transform-runtime --save-dev
//语法对应关系
npm i babel-preset-env babel-preset-stage-0 --save-dev
```

**webpack.config.js**

```
module: {
	rules:[
			//不编译/node_modules/下的js文件
			{ test:/\.js$/,use:'babel-loader', exclude: /node_modules/}
		]
	}
};
```

**根目录下创建.babelrc并配置**

```
{
  "presets": ["env","stage-0"],
  "plugins": ["transform-runtime"]
}
```

