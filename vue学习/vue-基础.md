# VUE基础

安装

+ cdn  `<script src="https://cdn.jsdelivr.net/npm/vue"></script>`
+ NPM `$ npm install vue`

### vue是什么

一套用于构建用户界面的**渐进式框架(能够部分使用,也能够全局使用)**



### 一.模板语法

#### 1.插值

+ 文本  <span>Message: {{ msg }}</span>
+ 原始HTML <span v-html="rawHtml"></span></p>
+ 特性(属性)  <div v-bind:id="dynamicId"></div>
+ js表达式(只能绑定单个表达式)
  - {{ number + 1 }}
  - {{ ok ? 'YES' : 'NO' }}
  - {{ message.split('').reverse().join('') }}

#### 2.指令

指令 (Directives) 是带有 `v-` 前缀的特殊特性。指令特性的值预期是**单个 JavaScript 表达式** (`v-for` 是例外情况，稍后我们再讨论)。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

+ 参数:一些指令能够接收一个“参数”，在指令名称之后以冒号表示。例如，`v-bind` 指令可以用于响应式地更新 HTML 特性：

  + ```
    <a v-bind:href="url">...</a>
    <a v-on:click="doSomething">...</a>
    ```

+ 动态参数:从 2.6.0 开始，可以用方括号括起来的 JavaScript 表达式作为一个指令的参数

  + ```vue
    <a v-on:[eventName]="doSomething"> ... </a>
    当eventName为click时
    等价于
    <a v-on:click="doSomething"> ... </a>
     
    ```

+ 修饰符:修饰符 (modifier) 是以半角句号 `.` 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。

  + ```vue
    <form v-on:submit.prevent="onSubmit">...</form>
    例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：
    ```

    ​

#### 3.缩写

+ v-bind 缩写为 :    v-bind:id=xxx      :id=xxx
+ v-on 缩写为@     v-on:click=xxx    @click =xxx



### 二.计算属性和侦听器

#### 1.计算属性

模板内的表达式太多难以维护时,可以使用计算属性

**关键字**:computed

+ 例子

  ```vue
  <div id="example">
    <p>Original message: "{{ message }}"</p>
    <p>Computed reversed message: "{{ reversedMessage }}"</p>
  </div>
  var vm = new Vue({
    el: '#example',
    data: {
      message: 'Hello'
    },
    computed: {
      // 计算属性的 getter
      reversedMessage: function () {
        // `this` 指向 vm 实例
        return this.message.split('').reverse().join('')
      }
    }
  })
  ```

  ​

  **说明**:

  + 也可以定义方法`<p>Reversed message: "{{ reversedMessage() }}"</p>`达到同样的效果,不同的是**计算属性是基于它们的依赖进行缓存的**,调用方法的话,需要耗费巨大计算
  + 计算属性默认只有 getter ,不用声明,但是有时也可以编辑setter(做好后勤)

  ```vue
  computed: {
    fullName: {
      // getter
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
  ```

#### 2.侦听器

当需要在数据变化时执行异步或开销较大的操作时,来响应数据的变化

**关键字**:watch

```vue
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<!-- 因为 AJAX 库和通用工具的生态已经相当丰富，Vue 核心代码没有重复 -->
<!-- 提供这些功能以保持精简。这也可以让你自由选择自己更熟悉的工具。 -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
    // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
    // 请参考：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

### 三.Class和style绑定

操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是属性，所以我们可以用 `v-bind` 处理它们：只需要通过表达式计算出字符串结果即可

#### 1.绑定HTML,Class

+ 对象语法 

  - ```vue
    <div
      class="static"
      v-bind:class="{ active: isActive, 'text-danger': hasError }"
    >
    </div>

    data: {
      isActive: true,
      hasError: false
    }
    ```

+ 数组语法

  + ```vue
    <div v-bind:class="[activeClass, errorClass]"></div>
    data: {
      activeClass: 'active',
      errorClass: 'text-danger'
    }
    ```

    ​

#### 2.绑定内联样式

- 对象语法 

  - ```vue
    <div v-bind:style="styleObject"></div>
    data: {
      styleObject: {
        color: 'red',
        fontSize: '13px'
      }
    }
    ```

- 数组语法

  - ```vue
    <div v-bind:class="[activeClass, errorClass]"></div>
    data: {
      activeClass: 'active',
      errorClass: 'text-danger'
    }
    ```

  **注意点**

  1.自动添加前缀 :当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS 属性时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

  2.多重值:从 2.3.0 起你可以为 `style` 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：

  ```
  <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
  ```

  这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 `display: flex`。

  ​

### 四.条件渲染

#### v-if(v-else)

`v-if` 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染。

```
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

**注意:** 

+ 用key管理复用的元素,在某些重复复用的组件里面,需要添加key属性来标识唯一

  ```
  <template v-if="loginType === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username" key="username-input">
  </template>
  <template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address" key="email-input">
  </template>
  ```

+ v-show和v-if:区别在于,v-show已经渲染了,通过display属性隐藏了,v-if是没有渲染,条件为true时,才会渲染.

### 五列表渲染

#### v-for

根据一组数组的选项列表进行渲染

**案例:**

```vue
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

**key属性**

当 Vue.js 用 `v-for` 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 `key` 属性

```vue
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>

```

#### 数组更新检测

+ 变异方法
  + `push()` 向数组的末尾添加一个或多个元素
  + `pop()` 删除并返回数组的最后一个元素
  + `shift()` 删除并返回数组的第一个元素
  + `unshift() `向数组的开头添加一个或更多元素
  + `splice()`向/从数组中添加/删除项目
  + `sort()` 对数组的元素进行排序
  + `reverse()颠倒数组中元素的顺序`

+ 替换数组:相比之下，也有非变异 (non-mutating method) 方法，例如：`filter()`, `concat()` 和 `slice()`

  这些不会改变原始数组，但**总是返回一个新数组**。当使用非变异方法时，可以用新数组替换旧数组：

  ```
  example1.items = example1.items.filter(function (item) {
    return item.message.match(/Foo/)
  })
  ```

  ​

### 六.事件处理

####1.监听事件

可以用 `v-on` 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。

```vue
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

#### 2.事件处理方法

```vue
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
```

#### 3,内联处理器中的方法

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

#### 4.事件修饰符

<!-- 阻止单击事件继续传播:阻止冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 :阻止默认事件-->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即元素自身触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

#### 5.按键修饰符

在监听键盘事件时，我们经常需要检查详细的按键。Vue 允许为 `v-on` 在监听键盘事件时添加按键修饰符：

```
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```

- `.enter`
- `.tab`
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### 六.表单输入绑定

可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

####1.文本

```
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

####2.多行文本

```
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

####3.复选框

+ 单个复选框	

```
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

- 多个复选框

```
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
```

####4.单选按钮

```
<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
```

####5.选择框

+ 单选

```
<div id="example-5">
  <select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```

- 多选框(将v-model绑定到数组)

```
<div id="example-6">
  <select v-model="selected" multiple style="width: 50px;">
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected }}</span>
</div>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
```

#### 6.修饰符

+ .lazy    在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了[上述](https://cn.vuejs.org/v2/guide/forms.html#vmodel-ime-tip)输入法组合文字时)。你可以添加 `lazy` 修饰符，从而转变为使用 `change`事件进行同步：
+ .number   如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：
+ .trim   如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：


### 七.组件基础

#### 1.基本示例

```vue
<div id="components-demo">
  <button-counter></button-counter>
</div>

Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

#### 2.组件复用

定义的组件可以多次复用

```
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

**data必须是函数**:定义组件时,data必须是一个函数



#### 3.组件组织

为了能在模板中使用，这些组件必须先注册以便 Vue 能够识别。这里有两种组件的注册类型：**全局注册**和**局部注册**。

> 可以先创建模板对象
>
> ```
> 方式一
> var mycom = {
>     template:'<p>123</p>'
> }
> 方式二
> <template id="tmp1">
> 	<div>1213</div>
> </template>
> var mycom = {
>     template:'#tmp1'
> }
> ```

> 可以使用es6语法
>
> compontents:{
>
> ​	login
>
> }



+ 全局注册

```
Vue.component('my-component-name', {
  // ... options ...
})
```

+ 局部注册

```
var vm = new Vue({
    el:"#app",
    compontents:{
        
    }
})
```

####组件获取dom元素以及组件的引用【ref】

```
<div id="app">
	<h3 ref="myh3">121212</h3>
	<mylogin ref="mylogin">121212</mylogin>
</div>
var vm = new Vue({
    el:'#app',
    data:{},
    methods:{
        show(data){
            console.log(this.$refs.myh3.innerTest)
            console.log(this.$refs.mylogin.data)
        }
    },
    components:{
        mycom
    }   
})
```



### 八.父子组件通信

####1.父组件向子组件传递数据【props】

> props数据都是只读的

```
<template>
	<blog-post title="hasaki"></blog-post>
	<blog-post title="hello "></blog-post>
	<blog-post title="Why  Vue is so fun"></blog-post>
</template>
//通过props,将父组件中的title属性的值,传递给子组件
//**也可以使用v-bind绑定自定义属性**//
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

 #### 2.父组件传递方法给子组件【$emit】

```
<mycom @func123="show"></mycom>

<template id="tmp1">
	<div>子组件z</div>
	<input type='button' value='子组件按钮' @click='myclick'>
</template>

var mycom = {
    template:'#tmp1',
    methods:{
        myclick(){
            this.$emit('func123',data) 
        }
    }
}

var vm = new Vue({
    el:'#app',
    data:{},
    methods:{
        show(data){
            console.log('调用了父组件show方法'+data)
        }
    },
    components:{
        mycom
    }   
})

```

####3.子组件传递数据给父组件【$emit】

```
<div>
	<mycom @func123="show"></mycom>
	<p>{{dataformSon}}</p>
</div>


<template id="tmp1">
	<div>子组件z</div>
	<input type='text' v-model="data">
	<input type='button' value='子组件按钮' @click='myclick'>
</template>

var mycom = {
    template:'#tmp1',
    data(){
        return{
            data:data
        }
    }
    methods:{
        myclick(){
            this.$emit('func123',data) 
        }
    }
}

var vm = new Vue({
    el:'#app',
    data:{
        dataformSon=''
    },
    methods:{
        show(data){
            dataformSon = data
        }
    },
    components:{
        mycom
    }   
})
```

### 九.路由

>后端路由：URL对应服务器上对应的资源
>
>前端路由：主要是通过URL中的hash（#），实现不同页面之间的跳转

#### 1.安装和配置

+ 下载使用

下载地址`<https://unpkg.com/vue-router/dist/vue-router.js>`

```
<script src="/path/to/vue.js"></script>
<script src="/path/to/vue-router.js"></script>

```

+ npm使用

```
#NPM
npm install vue-router

//如果在一个模块化工程中使用它，必须要通过 Vue.use() 明确地安装路由功能：

import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

#### 2.使用

```
    <div id="app" v-cloak>
<!--        <a href="#/login">login</a>-->
<!--        <a href="#/register">register</a>-->
        <router-link to="/login">login</router-link>
        <router-link to="/register">register</router-link>

        <router-view></router-view>
    </div>

    <script src="node_modules/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router@3.0.6/dist/vue-router.js"></script>
    <script>


        var login = {
            template:'<h1>登录组件</h1>>'
        }
        var register = {
            template:'<h1>注册组件</h1>>'
        }
        /*
        * 创建路由对象
        * */
        var routerObj = new VueRouter({
            /*
            * 路由匹配规则，有两个必须的属性：
            *   path：监听那个路由；链接地址；
            *   component：表示如果路由匹配到path，显示的组件（组件值必须是一个组件的模板对象，不能是模板的引用类型）
            * */
            routes:[
                {path:'/',redirect:""},
                {path:'/login',component:login},
                {path:'/register',component:register}
            ]
        })
        var app=new Vue({
            el:"#app",
            /*注册路由组件*/
            router:routerObj
        })
    </script>
```

> 改变点击url的样式
>
> 1.修改默认的css类
>
> .router-link-active{
>
> ​	...
>
> ​	...
>
> }
>
> 2.配置路由对象
>
> 

##### 改变点击url的样式

+ 1.修改默认的css类

```
.router-link-active{

	...

	...

}
```

+ 配置路由对象

```
 var routerObj = new VueRouter({
            /*
            * 路由匹配规则，有两个必须的属性：
            *   path：监听那个路由；链接地址；
            *   component：表示如果路由匹配到path，显示的组件（组件值必须是一个组件的模板对象，不能是模板的引用类型）
            * */
       	routes:[
                {path:'/',redirect:""},
                {path:'/login',component:login},
                {path:'/register',component:register}
            ],
		linkAcyiveClass:'myactive'
})
        
.myactive{

	...

	...

}
```



##### 得到url的参数

> 方法一：get

```
<div id="app" v-cloak>
        <router-link to="/login?id=10">login</router-link>
        <router-link to="/register?id=15">register</router-link>

        <router-view></router-view>
    </div>


//$routed对象可以得到url
        var login = {
            template:'<h1>登录组件======{{$route.query.id}}</h1>',

        }
        var register = {
            template:'<h1>注册组件======{{$route.query.id}}</h1>'
        }
```

> 方法二:占位符

```
<div id="app" v-cloak>
        <router-link to="/login/10/wolomo">login</router-link>
        <router-link to="/register/20/wolomo00">register</router-link>

        <router-view></router-view>
    </div>

    <script src="node_modules/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router@3.0.6/dist/vue-router.js"></script>
    <script>
        var login = {
            template:'<h1>登录组件======{{$route.params.id}}====={{$route.params.name}}</h1>',

        }
        var register = {
            template:'<h1>注册组件======{{$route.params.id}}====={{$route.params.name}}</h1>'
        }
        var routerObj = new VueRouter({

            routes:[
                {path:'/',redirect:""},
                {path:'/login/:id/:name',component:login},
                {path:'/register/:id/:name',component:register}
            ]
        })
        var app=new Vue({
            el:"#app",
            /*注册路由组件*/
            router:routerObj
        })
    </script>
```

####3.路由的嵌套

 ```
<div id="app" v-cloak>
    <router-link to="/account">account</router-link>
    <router-view></router-view>
</div>
<template id="tmp1">
    <div>
        <div>这是account组件</div>
        <router-link to="/account/login">login</router-link>
        <router-link to="/account/register">register</router-link>
        <router-view></router-view>
    </div>
</template>
<script src="node_modules/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router@3.0.6/dist/vue-router.js"></script>
<script>
    var account = {
        template: '#tmp1',
    }
    //$routed对象可以得到url
    var login = {
        template: '<h1>登录组件===========</h1>'
    }
    var register = {
        template: '<h1>注册组件===========</h1>'
    }
    /*
    * 创建路由对象
    * */
    var routerObj = new VueRouter({
        routes: [
            {
                path: '/account',
                component: account,
                children:[
                    // 如果加了斜线，则以根目录路由
                    {path: 'login',component: login},
                    {path: 'register',component: register},
                ]
            }
        ]
    })
    var app = new Vue({
        el: "#app",
        /*注册路由组件*/
        router: routerObj
    })
</script>
 ```

#### 4.命名视图

```
<div id="app">
        <router-view></router-view>
        <router-view name="left"></router-view>
        <router-view name="main"></router-view>
    </div>
    <script src="node_modules/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router@3.0.6/dist/vue-router.js"></script>
    <script>
        var header = {
            template: '<h1 class="header">Header头部区域</h1>'
        }
        var leftBox = {
            template: '<h1>Left侧边栏区域</h1>'
        }
        var mainBox = {
            template: '<h1 class="main">mainBox主体区域</h1>'
        }
        var routerObj = new VueRouter({
            routes:[
                {
                    path: '/', components: {
                        'default': header,
                        'left': leftBox,
                        'main': mainBox
                    }
                }
            ]
        })
        var app=new Vue({
            el:"#app",
            router:routerObj
        })
    </script>
```

 