#vuex && 购物车

+++

## 一.vuex

### 1.vuex的介绍

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

> //安装使用
>
> ```
> npm install vuex --save
>
> //依赖primise
> import Vue from 'vue'
> import Vuex from 'vuex'
>
> Vue.use(Vuex)
> ```

### 2.vuex的使用

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。Vuex 和单纯的全局对象有以下两点不同：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

#### 1)创建Store对象

```
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

####2)获取store对象

```
//调用store方法(我们不应当在组件使用自定义方法来改变store数据,因为这样可能会导致store的数据遭到污染,例如格式的变化,)(最好使用store中mutation上的方法)
store.commit('increment')
//调用store数据
console.log(store.state.count) // -> 1
```

### 3.vuex详解

#### 1)state  ==>data

**定义**

```
//在main.js中创建store
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
}) 


//2.将将state挂载在vm实例上
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store
})
```

**调用方法**

```
//方法一
<h1>{{count}}</h1>
computed:{
    count(){
      return this.$store.state.count
  }
}


//方法二
<h1>{{count}}</h1>
computed:{
 	...mapState(["count"])
}
```



####2)getter ==>computed

Getter 就是把组件中共有的对state 的操作进行了提取，它就相当于 对state 的computed. 所以它会获得state 作为第一个参数。

#### 3)Mutation  ==>method 

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。

> Mutation  中不能有异步操作

**基本使用**

```
//定义
const store = new Vuex.Store({
  // 全局状态
  state: {
    count: 0
  },
  // 更改state状态,放只读方法
  mutations: {
    increase(state) {
      state.count++
    },
    decrease(state) {
      state.count--
    }
  }
})

<template>
  <div class="hello">
      <button @click="increase">++++++</button>
      <button @click="decrease">------</button>
  </div>
</template>

//调用方法一
  methods:{
    increase(){
      this.$store.commit("increase")
    },
    decrease(){
      this.$store.commit("decrease")
    }
  }
  
//调用方法二
  methods:{
    ...mapMutations(["increase","decrease"]),
  }
  
  
//调用方法三
methods:{
    ...mapMutations(["increase","decrease"]),
    increasee(){
      // this.$store.commit("increase")
      this.increase()
    },
    decreasee(){
      // this.$store.commit("decrease")
      this.decrease()
    }

  }
```

**添加参数 (参数只能有一个,可以为对象)**

```
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}

this.$store.commit('increment', 10)
```

#### 4)Action

```
//定义
mutations: {
    increase(state){
      state.count++
    },
    decrease(state){
      state.count--
    }
  },
//业务逻辑
 actions: {
  myIncrease(context){
  	context.commit("increase")
  },
  myDecrease(context){
  	context.commit("decrease")
  },
 }
 
//调用方法一
    increasee(){
      this.$store.dispatch('myIncrease')
    },
    decreasee(){
      this.$store.dispatch('myIncrease')
    }
  }
//调用方法二
import {mapMutations,mapActions} from  "vuex"

methods:{
    ...mapActions(["myIncrease","myDecrease"]),
    increasee(){
      this.myIncrease()
    },
    decreasee(){
      this.myDecrease()
    }
  }
```







