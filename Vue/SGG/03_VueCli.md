# Vue Cli

> Vue脚手架是Vue官方提供的标准化开发工具（开发平台）

**查看 webpack 配置**
- vue inspect > output.js

## 文件结构
```markdown
.文件目录
├── node_modules 
├── public
│   ├── favicon.ico: 页签图标
│   └── index.html: 主页面
├── src
│   ├── assets: 存放静态资源
│   │   └── logo.png
│   │── component: 存放组件
│   │   └── HelloWorld.vue
│   │── App.vue: 汇总所有组件
│   └── main.js: 入口文件
├── .gitignore: git版本管制忽略的配置
├── babel.config.js: babel的配置文件
├── package.json: 应用包配置文件 
├── README.md: 应用描述文件
└── package-lock.json: 包版本控制文件
```

## render 函数
```javascript
new Vue({
  el:'#app',
  // render函数功能：将App组件放入容器中
  // 简写形式
  render: h => h(App),
  // 完整形式
  // render(createElement){
  //   return createElement(App)
  // }
})
```

### 关于不同版本的区别
- vue.js 与 vue.runtime.xxx.js 的区别 
    - vue.js 是完整版的 Vue，包含：核心功能 + 模板解析器
    - vue.runtime.xxx.js 是运行版的 Vue，只包含核心功能，没有模板解析器
    - esm 就是 ES6 module
- 因为 vue.runtime.xxx.js 没有模板解析器，所以不能使用 template 配置项，需要使用 render 函数接收到的 createElement 函数去指定具体内容

## 默认配置
- vue inspect > output.js可以查看到 Vue 脚手架的默认配置
- 使用 vue.config.js 可以对脚手架进行个性化定制，和 package.json 同级目录

```javascript
module.exports = {
  pages: {
    index: {
      entry: 'src/index/main.js' // 入口
    }
  },
  lintOnSave: false	// 关闭语法检查
}
```

## ref
ref 被用来给元素或子组件注册引用信息（ id 的替代者）
- 应用在 html 标签上获取的是真实 DOM 元素，应用在组件标签上获取的是组件实例对象 vc
- 使用方式 
    - 属性：`<h1 ref="xxx"></h1>或<School ref="xxx"></School>`
    - 获取：`this.$refs.xxx`

```html
<template>
  <div>
    <h1 v-text="msg" ref="title"></h1>
    <button ref="btn" @click="showDOM">点我输出上方的DOM元素</button>
    <School ref="sch"/>
  </div>
</template>

<script>
  import School from './components/School'

  export default {
    name:'App',
    components:{ School },
    data() {
      return {
        msg:'欢迎学习Vue！'
      }
    },
    methods: {
      showDOM(){
        console.log(this.$refs.title)	// 真实DOM元素
        console.log(this.$refs.btn)		// 真实DOM元素
        console.log(this.$refs.sch)		// School组件的实例对象（vc）
      }
    },
  }
</script>
```

## props

> props 让组件接收外部传过来的数据 

- 传递数据 `<Demo name="xxx" :age="18"/>` 这里 age 前加:，通过 v-bind 使得里面的 18 是数字
- 接收数据
    - 第一种方式（只接收）props:['name', 'age'] 
    - 第二种方式（限制类型）props:{name:String, age:Number}
    - 第三种方式（限制类型、限制必要性、指定默认值）

> 备注：props 是只读的，Vue 底层会监测你对 props 的修改，如果进行了修改，就会发出警告
> 若业务需求确实需要修改，那么请复制 props 的内容到 data 中，然后去修改 data 中的数据 

- App.vue
```html
<template>
  <div>
    <Student name="李四" sex="女" :age="18"/>
    <Student name="王五" sex="男" :age="18"/>
  </div>
</template>

<script>
  import Student from './components/Student'

  export default {
    name:'App',
    components:{ Student }
  }
</script>
```

- Student.vue

```html
<template>
  <div>
    <h1>{{ msg }}</h1>
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生性别：{{ sex }}</h2>
    <h2>学生年龄：{{ myAge + 1 }}</h2>
    <button @click="updateAge">尝试修改收到的年龄</button>
  </div>
</template>

<script>
export default {
  name: "Student",
  data() {
    console.log(this);
    return {
      msg: "我是一个UESTC大学的学生",
      myAge: this.age,
    };
  },
  methods: { updateAge() { this.myAge++; }, },
  // 简单声明接收
  // props:['name','age','sex']

  // 接收的同时对数据进行类型限制
  //   props: {
  //     name: String,
  //     age: Number,
  //     sex: String,
  //   }

  // 接收的同时对数据：进行类型限制+默认值的指定+必要性的限制
  props: {
    name: {
      type: String, 	//name的类型是字符串
      required: true, //name是必要的
    },
    age: {
      type: Number,
      default: 99, //默认值
    },
    sex: {
      type: String,
      required: true,
    },
  },
};
</script>
```

## mixin
> 功能：可以把多个组件共用的配置提取成一个混入对象 

### 定义混入
```javascript
const mixin = {
    data() {....},
    methods: {....}
    ....
}
```

### 使用混入
- 全局混入：`Vue.mixin(xxx)`
- 局部混入：`mixins:['xxx']`

**备注**
- 组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”，在发生冲突时以组件优先 
- 同名生命周期钩子将合并为一个数组，**因此都将被调用**。另外，混入对象的钩子将在组件自身钩子之前调用

## plugin
- 功能：用于增强 Vue
- 本质：包含 install 方法的一个对象，install 的第一个参数是 Vue，第二个以后的参数是插件使用者传递的数据
- 定义插件（见下 src/plugin.js）
- 使用插件：`Vue.use()`

- plugin.js
```javascript
export default {
  install(Vue,x,y,z){
    console.log(x,y,z)
    //全局过滤器
    Vue.filter('mySlice', function(value){return value.slice(0,4)})

    //定义全局指令
    Vue.directive('fbind',{
      //指令与元素成功绑定时（一上来）
      bind(element,binding){element.value = binding.value},
      //指令所在元素被插入页面时
      inserted(element,binding){element.focus()},
      //指令所在的模板被重新解析时
      update(element,binding){element.value = binding.value}
    })

    //定义混入
    Vue.mixin({
      data() {return {x:100,y:200}},
    })

    //给Vue原型上添加一个方法（vm和vc就都能用了）
    Vue.prototype.hello = ()=>{alert('你好啊')}
  }
}
```

## scoped
- 作用：让样式在局部生效，防止冲突
- 写法：`<style scoped>`


## Todo-List example

### 组件化编码流程

- 拆分静态组件：组件要按照功能点拆分，命名不要与html元素冲突
- 实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用
    - 一个组件在用：放在组件自身即可
    - 一些组件在用：放在他们共同的父组件上（状态提升）
- 实现交互：从绑定事件开始

>  关于 props
> - 使用
>     - 父组件 ==> 子组件 通信
>     - 子组件 ==> 父组件 通信（要求父组件先给子组件一个函数）
> - 使用 v-model 时要切记：v-model 绑定的值不能是 props 传过来的值，因为 props 是不可以修改的 
> - props 传过来的若是对象类型的值，修改对象中的属性时 Vue 不会报错，但不推荐这样做

### 组件自定义事件
- 一种组件间通信的方式，适用于：子组件 ===> 父组件
- 使用场景：子组件想给父组件传数据，那么就要在父组件中给子组件绑定自定义事件（事件的回调在 A 中）
- 绑定自定义事件
    - 第一种方式，在父组件中 `<Demo @事件名="方法"/>或<Demo v-on:事件名="方法"/>`
    - 第二种方式，在父组件中 `this.$refs.demo.$on('事件名',方法)`
    -若想让自定义事件只能触发一次，可以使用once修饰符，或$once方法
- 触发自定义事件 `this.$emit('事件名',数据)`
- 解绑自定义事件 `this.$off('事件名)`
- 组件上也可以绑定原生 DOM 事件，需要使用 `native` 修饰符  `@click.native="show"` 上面绑定自定义事件，即使绑定的是原生事件也会被认为是自定义的，需要加 native ，加了后就将此事件给组件的根元素
- 注意：通过 `this.$refs.xxx.$on('事件名',回调函数)` 绑定自定义事件时，回调函数要么配置在 methods 中，要么用箭头函数，否则 this 指向会出问题

### 全局事件总线
> GlobalEventBus 
> 一种可以在任意组件间通信的方式，本质上就是一个对象，它必须满足以下条件
> - 所有的组件对象都必须能看见他 
> - 这个对象必须能够使用$on$emit$off方法去绑定、触发和解绑事件

**使用步骤**
- 定义全局事件总线

```javascript
new Vue({
   	...
   	beforeCreate() {
   		Vue.prototype.$bus = this // 安装全局事件总线，$bus 就是当前应用的 vm
   	},
    ...
})
```
- 使用事件总线 
  - 接收数据：A 组件想接收数据，则在 A 组件中给 $bus 绑定自定义事件，事件的回调留在 A 组件自身 
  - 提供数据：this.$bus.$emit('xxx',data) 
```javascript
export default {
    methods(){
        demo(data){...}
    }
    ...
    mounted() {
        this.$bus.$on('xxx',this.demo)
    }
}
```

- 最好在 beforeDestroy 钩子中，用 $off() 去解绑当前组件所用到的事件

### 消息的订阅与发布
> 消息订阅与发布（pubsub）消息订阅与发布是一种组件间通信的方式，适用于任意组件间通信 
> 很少使用，推荐使用全局事件总线

- 使用步骤
    1. 安装pubsub：npm i pubsub-js 
    2. 引入：import pubsub from 'pubsub-js' 
    3. 接收数据：A组件想接收数据，则在A组件中订阅消息，订阅的回调留在A组件自身  
    4. 提供数据：pubsub.publish('xxx',data) 
    5. 最好在beforeDestroy钩子中，使用pubsub.unsubscribe(pid)取消订阅

```javascript
export default {
    methods: {
        demo(msgName, data) {...}
    }
    ...
    mounted() {
			this.pid = pubsub.subscribe('xxx',this.demo)
    }
}
```

### $nextTick
> 这是一个生命周期钩子
> `this.$nextTick(回调函数)` 在下一次 DOM 更新结束后执行其指定的回调
> 什么时候用：当改变数据后，要基于更新后的新 DOM 进行某些操作时，要在 nextTick 所指定的回调函数中执行

### 过渡与动画
> Vue 封装的过度与动画：在插入、更新或移除 DOM 元素时，在合适的时候给元素添加样式类名

**语法**
- 准备好样式
    - 元素进入的样式
        - v-enter 进入的起点
        - v-enter-active 进入过程中
        - v-enter-to 进入的终点
    - 元素离开的样式
        - v-leave 离开的起点
        - v-leave-active 离开过程中
        - v-leave-to 离开的终点
- 使用 `<transition>` 包裹要过度的元素，并配置 name 属性，此时需要将上面样式名的 v 换为 name 
- 要让页面一开始就显示动画，需要添加 appear 属性
- 若有多个元素需要过度，则需要使用 `<transition-group>`，且每个元素都要指定key值 

**第三方动画库**
```html
<transition-group appear
          name="animate__animated animate__bounce"
          enter-active-class="animate__swing"
          leave-active-class="animate__backOutUp">
  <h1 v-show="!isShow" key="1">你好啊！</h1>
  <h1 v-show="isShow" key="2">尚硅谷！</h1>
</transition-group>
```

## 配置代理

### 方法一（单个代理）
- 优点：配置简单，请求资源时直接发给前端（8080）即可
- 缺点：不能配置多个代理，不能灵活的控制请求是否走代理
- 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，才会将请求会转发给服务器 （优先匹配前端资源）

```javascript
module.exports = {
  devServer:{
    proxy:"http://localhost:5000"
  }
}
```

### 方法二（多个代理）
- 优点：可以配置多个代理，且可以灵活的控制请求是否走代理
- 缺点：配置略微繁琐，请求资源时必须加前缀

```javascript
module.exports = {
	devServer: {
      proxy: {
      '/api1': {													// 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',	// 代理目标的基础路径
        pathRewrite: {'^/api1':''},				// 代理往后端服务器的请求去掉 /api1 前缀
        ws: true,													// WebSocket
        changeOrigin: true,
        
      },
      '/api2': {
        target: 'http://localhost:5001',
        pathRewrite: {'^/api2': ''},
        changeOrigin: true
      }
    }
  }
}
/*
   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080
   changeOrigin默认值为true
*/
```

## slot 插槽
> - 让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式
> - 适用于 父组件 ===> 子组件
> - 分类：默认插槽、具名插槽、作用域插槽 

### 默认插槽

```javascript
父组件中：
        <Category>
           <div>html结构1</div>
        </Category>
子组件中：Category
        <template>
            <div>
               <!-- 定义插槽 -->
               <slot>插槽默认内容...</slot>
            </div>
        </template>
```

### 具名插槽
> 父组件指明放入子组件的哪个插槽 `slot="footer"`，如果是 `template` 可以写成 `v-slot:footer`

```javascript
父组件中：
        <Category>
            <template slot="center">
              <div>html结构1</div>
            </template>

            <template v-slot:footer>
               <div>html结构2</div>
            </template>
        </Category>
子组件中：
        <template>
            <div>
               <!-- 定义插槽 -->
               <slot name="center">插槽默认内容...</slot>
               <slot name="footer">插槽默认内容...</slot>
            </div>
        </template>
```

### 作用域插槽
> `scope` 用于父组件往子组件插槽放的html结构接收子组件的数据
> 理解：数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定
>（games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定） 

```javascript
父组件中：
        <Category>
            <template scope="scopeData">
                <!-- 生成的是ul列表 -->
                <ul>
                  <li v-for="g in scopeData.games" :key="g">{{g}}</li>
                </ul>
            </template>
        </Category>

        <Category>
            <template slot-scope="scopeData">
                <!-- 生成的是h4标题 -->
                <h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
            </template>
        </Category>
子组件中：
        <template>
            <div>
                <slot :games="games"></slot>
            </div>
        </template>
		
        <script>
            export default {
                name:'Category',
                props:['title'],
                //数据在子组件自身
                data() {
                    return {
                        games:['红色警戒','穿越火线','劲舞团','超级玛丽']
                    }
                },
            }
        </script>
```

> 注意：关于样式，既可以写在父组件中，解析后放入子组件插槽；也可以放在子组件中，传给子组件再解析 