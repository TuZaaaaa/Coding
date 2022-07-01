# Vue Router
> Vue 的一个插件库，专门用来实现 **SPA** 应用

> SPA
> - 单页Web应用（single page web application，SPA）
> - 整个应用**只有一个完整的页面**
> - 点击页面中的导航链接**不会刷新**页面，只会做页面的**局部更新**
> - 数据需要通过 ajax 请求获取

## What is Router?
> 一个路由就是一组映射关系（key - value）
> key 为路径，value 可能是 function 或 componen

- 路由分类
- 后端路由
    - 理解：value 是 function，用于处理客户端提交的请求
    - 工作过程：服务器接收到一个请求时，根据请求路径找到匹配的函数来处理请求，返回响应数据
- 前端路由
    - 理解：value 是 component，用于展示页面内容
    - 工作过程：当浏览器的路径改变时，对应的组件就会显示

## Install & Config

- 安装
```bash
npm i vue-router
```

- 应用插件
```javascript
Vue.use(VueRouter)
```

- 编写配置项
```javascript
import VueRouter from 'vue-router'			// 引入VueRouter
import Home from '../components/Home'		// 路由组件

// 创建router实例对象，去管理一组一组的路由规则
const router = new VueRouter({
	routes:[
		{
			path:'/home',
			component:Home
		}
	]
})

// 暴露router
export default router
```

- 实现切换
    - `<router-link>` 会被替换为 `<a>` 标签
```html
<router-link active-class="active" to="/about">About</router-link>
```

- 指定展示位置 `<router-view>`
- `src/router/index.js` 用于编写路由器配置

> Tips
> - 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载
> - 每个组件都有自己的 **$route** 属性，里面存储着自己的路由信息
> - 整个应用只有一个 router，可以通过组件的 **$router** 属性获取到
> - 配置多级路由规则，使用 children 配置项

## Query

- 传递参数
```html
<!-- 跳转并携带query参数，to的字符串写法 -->
<router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">跳转</router-link>
				
<!-- 跳转并携带query参数，to的对象写法（推荐） -->
<router-link 
	:to="{
		path:'/home/message/detail',
		query:{
		   id: m.id,
       title: m.title
		}
	}"
>跳转</router-link>
```

- 接收参数
```javascript
$route.query.id
$route.query.title
```

## 命名路由
> 简化路由跳转

```javascript
{
	path:'/demo',
	component:Demo,
	children:[
		{
			path:'test',
			component:Test,
			children:[
				{
                    name:'hello' // 给路由命名
					path:'welcome',
					component:Hello,
				}
			]
		}
	]
}
```

```html
<!--简化前，需要写完整的路径 -->
<router-link to="/demo/test/welcome">跳转</router-link>

<!--简化后，直接通过名字跳转 -->
<router-link :to="{name:'hello'}">跳转</router-link>

<!--简化写法配合传递参数 -->
<router-link 
	:to="{
		name:'hello',
		query:{
		    id:666,
        title:'你好'
		}
	}"
>跳转</router-link>
```

## params

- 声明接收
```javascript
{
	path:'/home',
	component:Home,
	children:[
		{
			path:'news',
			component:News
		},
		{
			component:Message,
			children:[
				{
					name:'xiangqing',
					path:'detail/:id/:title', // 🔴使用占位符声明接收params参数
					component:Detail
				}
			]
		}
	]
}
```

- 传递参数
> 路由携带 params 参数时，若使用 to 的对象写法，则不能使用 path 配置项，必须使用 name 配置！

- 接收参数
```javascript
$route.params.id
$route.params.title
```

## props
> 让路由组件更方便的收到参数

```javascript
{
	name:'xiangqing',
	path:'detail/:id',
	component:Detail,

	//第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
	// props:{a:900}

	//第二种写法：props值为布尔值，为true时，则把路由收到的所有params参数通过props传给Detail组件
	// props:true
	
	//第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
	props($route){
		return {
			id: $route.query.id,
			title: $route.query.title
		}
	}
}
```

## replace
> 作用：控制路由跳转时操作浏览器历史记录的模式

> 浏览器的历史记录有两种写入方式：push和replace
>   - push 是追加历史记录
>   - replace 是替换当前记录，路由跳转时候默认为push方式

- 开启 `replace` 模式
    - `<router-link :replace="true" ...>News</router-link>`
    - 简写：`<router-link replace ...>News</router-link>`

总结：浏览记录本质是一个栈，默认 `push`，点开新页面就会在栈顶追加一个地址，
后退，栈顶指针向下移动，改为 `replace` 就是不追加，而将栈顶地址替换

## 编程式路由导航（without `<router-link>`）
> 作用：不借助 `<router-link>` 实现路由跳转，让路由跳转更加灵活
- this.$router.push({})	内传的对象与 <router-link> 中的 to 相同
- this.$router.replace({})	
- this.$router.forward()	前进
- this.$router.back()		后退
- this.$router.go(n)		可前进也可后退，n 为正数前进 n，为负数后退

## 缓存路由组件
> 作用：让不展示的路由组件保持挂载，不被销毁
- `<keep-alive include="News"><router-view></router-view></keep-alive>`
- `<keep-alive :include="['News', 'Message']"><router-view></router-view></keep-alive>`

## 路由守卫

### activated deactivated 
> 由组件所独有的两个钩子，用于捕获路由组件的激活状态
- 具体使用
    - activated 路由组件被激活时触发
    - deactivated 路由组件失活时触发
### 路由守卫
> 作用：对路由进行权限控制 
> 分类：全局守卫、独享守卫、组件内守卫 

- 全局守卫
> `meta` 路由源信息
```javascript
// 全局前置守卫：初始化时、每次路由切换前执行
router.beforeEach((to,from,next) => {
	console.log('beforeEach',to,from)
	if(to.meta.isAuth){ // 判断当前路由是否需要进行权限控制
		if(localStorage.getItem('school') === 'atguigu'){ // 权限控制的具体规则
			next()	// 放行
		}else{
			alert('暂无权限查看')
		}
	}else{
		next()	// 放行
	}
})

// 全局后置守卫：初始化时、每次路由切换后执行
router.afterEach((to,from) => {
	console.log('afterEach',to,from)
	if(to.meta.title){ 
		document.title = to.meta.title //修改网页的title
	}else{
		document.title = 'vue_test'
	}
})
```

- 独享守卫
```javascript
beforeEnter(to,from,next){
	console.log('beforeEnter',to,from)
    if(localStorage.getItem('school') === 'atguigu'){
        next()
    }else{
        alert('暂无权限查看')
    }
}
```

- 组件内守卫
```javascript
//进入守卫：通过路由规则，进入该组件时被调用
beforeRouteEnter (to, from, next) {... next()},

//离开守卫：通过路由规则，离开该组件时被调用
beforeRouteLeave (to, from, next) {... next()},
```

> 路由器的两种工作模式
对于一个url来说，什么是 hash 值？
`#` 及其后面的内容就是 hash 值，hash 值不会包含在 HTTP 请求中，即：hash 值不会带给服务器 
- hash 模式 
    - 地址中永远带着 # 号，不美观
    - 若以后将地址通过第三方手机 app 分享，若 app 校验严格，则地址会被标记为不合法
    - 兼容性较好
-  history模式
    - 地址干净，美观
    - 兼容性和 hash 模式相比略差
    - 应用部署上线时需要后端人员支持，解决刷新页面服务端 404 的问题

```javascript
const router =  new VueRouter({
	mode:'history',
	routes:[...]
})

export default router
```