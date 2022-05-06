# Vuex
> 专门在 Vue 中实现集中式状态（数据）管理的一个 Vue 插件
> 对 Vue 应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信

## 什么时候使用

- 多个组件依赖于同一状态
- 来自不同组件的行为需要变更同一状态

## Vuex lifecycle diagram

![Vuex lifecycle diagram](https://vuex.vuejs.org/vuex.png)

## 环境搭建
- 下载安装： `npm i vuex`
- 创建 src/store/index.js 该文件用于创建 Vuex 中最为核心的 store

```javascript
import Vue from 'vue'
import Vuex from 'vuex'	// 引入Vuex

Vue.use(Vuex)	// 应用Vuex插件

const actions = {}		// 准备actions——用于响应组件中的动作
const mutations = {}	// 准备mutations——用于操作数据（state）
const state = {}		// 准备state——用于存储数据

// 创建并暴露store
export default new Vuex.Store({
	actions,
	mutations,
	state,
})
```

- 在 src/main.js 中创建 vm 时传入 store 配置项 

```javascript
import Vue from 'vue'
import App from './App.vue'
import store from './store'	// 引入store

Vue.config.productionTip = false

new Vue({
	el: '#app',
	render: h => h(App),
	store,  // 配置项添加store
	beforeCreate() {
		Vue.prototype.$bus = this
	}
})
```

## getters 配置项
> 当 state 中的数据需要经过加工后再使用时，可以使用 **getters** 加工，相当于全局计算属性

- 在 store.js 中追加 getters 配置

```javascript
......

const getters = {
	bigSum(state){
		return state.sum * 10
	}
}

// 创建并暴露store
export default new Vuex.Store({
	......
	getters
})
```

## map 方法的使用

### mapState
> 用于帮助映射 state 中的数据为计算属性

```javascript
computed: {
  	// 借助mapState生成计算属性：sum、school、subject（对象写法一）
  	...mapState({sum:'sum',school:'school',subject:'subject'}),

  	// 借助mapState生成计算属性：sum、school、subject（数组写法二）
  	...mapState(['sum','school','subject']),
},
```

### mapGetters
> 用于帮助生成与 actions 对话的方法，即包含 $store.dispatch(xxx) 的函数  

```javascript
methods:{
    //靠mapActions生成：incrementOdd、incrementWait（对象形式）
    ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})

    //靠mapActions生成：incrementOdd、incrementWait（数组形式）
    ...mapActions(['jiaOdd','jiaWait'])
}
```

### mapActions
> 用于帮助生成与 actions 对话的方法，即包含 $store.dispatch(xxx) 的函数   

```javascript
methods:{
    //靠mapActions生成：incrementOdd、incrementWait（对象形式）
    ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})

    //靠mapActions生成：incrementOdd、incrementWait（数组形式）
    ...mapActions(['jiaOdd','jiaWait'])
}
```

### mapMutations
> 用于帮助生成与 mutations 对话的方法，即包含 $store.commit(xxx) 的函数  

```javascript
methods:{
    //靠mapActions生成：increment、decrement（对象形式）
    ...mapMutations({increment:'JIA',decrement:'JIAN'}),
    
    //靠mapMutations生成：JIA、JIAN（对象形式）
    ...mapMutations(['JIA','JIAN']),
}
```

> 注意：mapActions 与 mapMutations 使用时
> 若需要传递参数需要：**在模板中绑定事件时传递好参数**，否则参数是事件对象 

## 模块化 + 命名空间
> 目的：让代码更好维护，让多种数据分类更加明确

### 操作

- 修改 store.js 为了解决不同模块命名冲突的问题，对不同模块添加配置 `namespaced: true`
- 之后在不同页面中引入 getteractionsmutations 时，需要加上所属的模块名

```javascript
const countAbout = {
  namespaced: true,	// 开启命名空间
  state: {x:1},
  mutations: { ... },
  actions: { ... },
  getters: {
    bigSum(state){ return state.sum * 10 }
  }
}

const personAbout = {
  namespaced: true,	// 开启命名空间
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    countAbout,
    personAbout
  }
})
```

- 开启命名空间后，组件中读取 state 数据  

```javascript
// 方式一：自己直接读取
this.$store.state.personAbout.list
// 方式二：借助 mapState 读取
...mapState('countAbout',['sum','school','subject']),
```

- 开启命名空间后，组件中读取 getters 数据

```javascript
// 方式一：自己直接读取
this.$store.getters['personAbout/firstPersonName']
// 方式二：借助 mapGetters 读取
...mapGetters('countAbout',['bigSum'])
```

- 开启命名空间后，组件中调用 dispatch 

```javascript
// 方式一：自己直接 dispatch
this.$store.dispatch('personAbout/addPersonWang',person)
// 方式二：借助 mapActions
...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
```

- 开启命名空间后，组件中调用 commit 

```javascript
// 方式一：自己直接 commit
this.$store.commit('personAbout/ADD_PERSON',person)
// 方式二：借助 mapMutations
...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
```
