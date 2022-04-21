# Vue 

## 特点
- **组件化**，提高复用，更好维护
- **声明式编码**，无需直接操作 DOM

## 模板语法
- 插值
> 用于解析标签体
```html
<h1>Hello {{name}}!</h1>
```

- 指令
> 用于解析标签
v-bind > :

```html
<a :href = "url">google</a>
```

## 数据绑定

- v-bind 单向数据绑定
    - 数据只能从 data 流向页面
- v-model 双向数据绑定
    - 只能用于表单元素
    - v-model:value 默认收集 value 值，简写 `v-model`


```html
单向数据绑定：<input type="text" :value="value"> <br />
双向数据绑定：<input type="text" v-model="value">
```

## el 与 data 的两种写法
```javascript
const vm = new Vue({
    el: '#root', // 1
    data: {
        name: 'Vue' // 1
    }
})

v.$mount('#root') // 2

data() { // 2
    retun {
        name: 'Vue'
    }
}
```

> 由 Vue 管理的函数，使用箭头函数后，this 不再是 Vue 实例，而是 window

## MVVM
- M Model data 中的数据
- V View 模板语法
- VM ViewModel Vue 实例对象

## 数据代理
> 通过一个对象代理对另一个对象中属性的操作

### 回顾 defineProperty
```javascript
let number = 18
let person = {
    name: 'rabbit',
    gender: 'male',
    age: 18
}
Object.defineProperty(person, 'age', {
    value: 18,
    enumerable: true, // 是否可以枚举
    writable: true, // 是否可以修改
    configurable: true, // 是否可以删除
    get() { // 当有人读取 age 属性时，getter 会被调用
        retun number
    },
    set(value) { // 当有人读取 age 属性时，setter 会被调用
        number = value
    }
})
```

### 对象代理
```javascript
let obj = {x: 100}
let obj2 = {x: 200}

Object.defineProperty(obj2, 'x', {
    get() {
        retun obj.x
    },
    set(value) {
        obj.x = value
    }
})
```

### Vue 中数据代理
> 通过 vm 对象来代理 data 对象中属性的操作（读/写）

**原理：**
- 通过 Object.defineProperty() 将 data 对象中所有属性添加到 vm 上
- 为每一个添加到 vm 上的属性，指定 getter/setter
- 在 getter/setter 内部去操作 data 中对应的属性

## 事件处理

### 鼠标事件
- v-on:click 简写为 @click

- 事件修饰符
    - pervent 阻止默认事件
    - stop 阻止事件冒泡
    - once 事件值触发一次
    - capture 使用事件的捕获模式
    - self 只有 event.target 是当前操作的元素才触发事件
    - passive 事件的默认行为立即执行，无需等待事件回调执行完毕

> 修饰符可以链式调用

```html
<button @click.pervent="showInfo($event, 66)">click me</button>
```
```javascript
    new Vue({
        el: '#root',
        data: {
            name: 'Vue',
        },
        methods: {
            showInfo(number, event) {
                console.log(numver + event);
            }
        }
    })
```

### 键盘事件
- enter
- delete
- eac
- space 
- ...

```html
<input type="text" placeholder="按下回车提示" @keyup.enter="showInfo">
```

## 计算属性
```html
<input type="text" v-model="firstName">
<input type="text" v-model="lastName">
<span>{{fullName}}</span>
```

```javascript
new Vue({
    el: '#root',
    data: {
        firstName: '张',
        lastName: '三'
    },
    computed: {
        // 完整写法
        // fullName: {
            // 初次读取会读取
            // 所依赖的数据发生变化时会读取
            // get() {
            //     console.log('get');
            //     retun `${this.firstName}-${this.lastName}`
            // },
            // set(value) {
            //     console.log('set');
            //     const arr = value.split('-')
            //     this.firstName = arr[0]
            //     this.lastName = arr[1]
            // }
        // }
        // 简写 仅能用于没有 set 的情况
        fullName() {
            console.log('get');
            retun `${this.firstName}-${this.lastName}`
        }
    }
})
```

## 监视属性
- 当被监视的属性变化时，回调函数自动调用，进行相关操作
- 只能监视存在的属性
- 两种写法
    - new Vue 时 watch 配置
    - 通过 vm.$watch 配置

```javascript
new Vue({
    el: '#root',
    data: {
        idCold: true
        numbers: {
            a: 1,
            b: 1
        }
    },
    watch: {
        isCold: {
            immediate: true, // 初始化时调用 handler
            handler(newValue, oldValue) {
                console.log('isCold 被修改');
            }
        }
        // 深度监视 监视多层结构
        numbers: {
            deep: true,
            handler() {
                console.log('numbers');
            }
        }
    }
})
```

> computed vs watch
> 1. computed 能完成的功能，watch 都可以完成
> 2. watch 能完成的功能，computed 不一定能完成
> 两个小原则
> 1. 所有被 Vue 管理的函数，最好写成普通函数，
> 2. 所有不被 Vue 所管理的函数（定时器的回调函数，ajax 的回调函数等），最好写成箭头函数，
> 这样 this 的指向才是 vm 或组件实例对象

## class 与 style 样式绑定
```html
<div id="root">
    <!-- 前三个常用 -->
    <!-- 字符串写法：适用于样式的类名不确定，需要动态绑定 -->
    <div class="basic" :class="mood" @click="changeMood">{{name}}</div>
    <!-- 数组写法：适用于绑定样式个数不确定，名字也不确定 -->
    <div class="basic" :class="classArr">{{name}}</div>
    <!-- 对象写法：适用于绑定样式个数确定，名字也确定，但要动态决定用不用 -->
    <div class="basic" :class="classObj">{{name}}</div>

    <!-- 对象写法 -->
    <div class="basic" :class="styleObj">{{name}}</div>
    <!-- 数组写法 -->
    <div class="basic" :class="[styleObj, styleObj2]">{{name}}</div>
</div>
```

```javascript
new Vue({
    el: '#root',
    data: {
        name: 'Vue',
        mood: 'normal'
        classArr: [item1, item2, item3],
        classObj: {
            item1: false,
            item2: false,
            item3: false
        },
        styleObj: {
            fontSize: '40px'
        },
        styleObj2: {
            color: 'red'
        }
    },
    methods: {
        
    }
})
```

## 条件渲染
- v-show：频率高（隐藏）
- v-if：频率低（删除）
    - v-else, v-else-if 多分支
    - templete 标签只能与 v-if 配合使用
  
> templete 标签渲染后不影响结构


## 列表渲染
- v-for

```html
<li v-for="(p, index) of persons" :key="p.id">
{{p.name}}-{{p.age}}
</li>
```

### key 的作用及原理
- 根据数据生成虚拟 DOM
- 根据虚拟 DOM 生成真实 DOM
- 更改数据再次生成会对比两个虚拟 DOM
- 对比规则
    - 存在相同 key
        - 内容不变，复用之前的真实 DOM
        - 内容变了，替换之前的真实 DOM
    - 不存在相同 key
        - 创建新的真实 DOM，渲染到页面
- 用 index 作为 key 可能会产生的问题
    - 若对数据进行，逆序添加，逆序删除等破坏顺序操作
        - 会产生没有必要的真实 DOM 更新 > 效率低
    - 如果结构中包含输入类的 DOM
        - 会产生错误 DOM 更新
- 开发中如何选择 key
    - 使用唯一标识


## 列表过滤
```html
<div id="root">
  <h2>人员列表</h2>
  <input type="text" placeholder="请输入名字" v-model="keyWord">
  <ul>
    <li v-for="(p,index) of filPersons" :key="p.id">
      {{ p.name }}-{{ p.age }}-{{ p.sex }}
    </li>
  </ul>
</div>

<script type="text/javascript">
  Vue.config.productionTip = false
  // 用 watch 实现
  // #region 
  /* new Vue({
			el: '#root',
			data: {
				keyWord: '',
				persons: [
					{ id: '001', name: '马冬梅', age: 19, sex: '女' },
					{ id: '002', name: '周冬雨', age: 20, sex: '女' },
					{ id: '003', name: '周杰伦', age: 21, sex: '男' },
					{ id: '004', name: '温兆伦', age: 22, sex: '男' }
				],
				filPersons: []
			},
			watch: {
				keyWord: {
					immediate: true,
					handler(val) {
						this.filPersons = this.persons.filter((p) => {
							return p.name.indexOf(val) !== -1
						})
					}
				}
			}
		}) */
  //#endregion

  // 用 computed 实现
  new Vue({
    el: '#root',
    data: {
      keyWord: '',
      persons: [
        { id: '001', name: '马冬梅', age: 19, sex: '女' },
        { id: '002', name: '周冬雨', age: 20, sex: '女' },
        { id: '003', name: '周杰伦', age: 21, sex: '男' },
        { id: '004', name: '温兆伦', age: 22, sex: '男' }
      ]
    },
    computed: {
      filPersons() {
        return this.persons.filter((p) => {
          return p.name.indexOf(this.keyWord) !== -1
        })
      }
    }
  }) 
</script>
```

## 列表排序
```html
<div id="root">
  <h2>人员列表</h2>
  <input type="text" placeholder="请输入名字" v-model="keyWord">
  <button @click="sortType = 2">年龄升序</button>
  <button @click="sortType = 1">年龄降序</button>
  <button @click="sortType = 0">原顺序</button>
  <ul>
    <li v-for="(p,index) of filPersons" :key="p.id">
      {{p.name}}-{{p.age}}-{{p.sex}}
      <input type="text">
    </li>
  </ul>
</div>

<script type="text/javascript">
  Vue.config.productionTip = false
  new Vue({
    el: '#root',
    data: {
      keyWord: '',
      sortType: 0, // 0原顺序 1降序 2升序
      persons: [
        { id: '001', name: '马冬梅', age: 30, sex: '女' },
        { id: '002', name: '周冬雨', age: 31, sex: '女' },
        { id: '003', name: '周杰伦', age: 18, sex: '男' },
        { id: '004', name: '温兆伦', age: 19, sex: '男' }
      ]
    },
    computed: {
      filPersons() {
        const arr = this.persons.filter((p) => {
          return p.name.indexOf(this.keyWord) !== -1
        })
        //判断一下是否需要排序
        if (this.sortType) {
          arr.sort((p1, p2) => {
            return this.sortType === 1 ? p2.age - p1.age : p1.age - p2.age
          })
        }
        return arr
      }
    }
  })
</script>
```

## 数据监视
```html
<div id="root">
  <h2>人员列表</h2>
  <button @click="updateMei">更新马冬梅的信息</button>
  <ul>
    <li v-for="(p,index) of persons" :key="p.id">
      {{p.name}}-{{p.age}}-{{p.sex}}
    </li>
  </ul>
</div>

<script type="text/javascript">
  Vue.config.productionTip = false

  const vm = new Vue({
    el: '#root',
    data: {
      persons: [
        { id: '001', name: '马冬梅', age: 30, sex: '女' },
        { id: '002', name: '周冬雨', age: 31, sex: '女' },
        { id: '003', name: '周杰伦', age: 18, sex: '男' },
        { id: '004', name: '温兆伦', age: 19, sex: '男' }
      ]
    },
    methods: {
      updateMei() {
        // this.persons[0].name = '马老师'	//奏效
        // this.persons[0].age = 50				//奏效
        // this.persons[0].sex = '男'			//奏效
        // this.persons[0] = {id:'001',name:'马老师',age:50,sex:'男'} //不奏效
        this.persons.splice(0, 1, { id: '001', name: '马老师', age: 50, sex: '男' })
      }
    }
  })
</script>
```

### 模拟数据监测
```javascript
let data = {
  name: '尚硅谷',
  address: '北京',
}

function Observer(obj) {
  // 汇总对象中所有的属性形成一个数组
  const keys = Object.keys(obj)
  // 遍历
  keys.forEach((k) => {
    Object.defineProperty(this, k, {
      get() {
        return obj[k]
      },
      set(val) {
        console.log(`${k}被改了，我要去解析模板，生成虚拟DOM.....我要开始忙了`)
        obj[k] = val
      }
    })
  })
}

// 创建一个监视的实例对象，用于监视data中属性的变化
const obs = new Observer(data)
console.log(obs)

// 准备一个vm实例对象
let vm = {}
vm._data = data = obs
```

### 数据监视原理
- Vue会监视 data 中所有层次的数据
- 如何监测 **对象** 中的数据？
    - 通过setter实现监视，且要在new Vue()时就传入要监测的数据 
        - 对象创建后追加的属性，Vue默认不做响应式处理
        - 如需给后添加的属性做响应式，请使用如下API
            - Vue.set(target,propertyName/index,value)
            - vm.$set(target,propertyName/index,value)
- 如何监测 **数组** 中的数据？
    - 通过包裹数组更新元素的方法实现，本质就是做了两件事
        - 调用原生对应的方法对数组进行更新
        - 重新解析模板，进而更新页面
- 在Vue修改数组中的某个元素一定要用如下方法 
- push(), pop(), unshift(), shift(), splice(), sort(), reverse()这几个方法被 Vue 重写了
- Vue.set()或vm.$set()

> 特别注意：Vue.set() 和 vm.$set() 不能给vm或vm的根数据对象（data等）添加属性

### Vue 中的数据劫持
- 将原有的 data 数据添加 getter setter 等对数据的改动进行劫持


## 表单数据收集
- 若<input type="text"/>，则 v-model 收集的是 value 值，用户输入的内容就是 value 值
- 若<input type="radio"/>，则 v-model 收集的是 value 值，且要给标签配置 value 属性
- 若<input type="checkbox"/>
    - 没有配置 value 属性，那么收集的是 checked 属性（勾选 or 未勾选，是布尔值）
    - 配置了 value 属性
        - v-model 的初始值是非数组，那么收集的就是 checked（勾选 or 未勾选，是布尔值）
        - v-model 的初始值是数组，那么收集的就是 value 组成的数组
- v-model 的三个修饰符
    - lazy 失去焦点后再收集数据
    - number 输入字符串转为有效的数字
    - trim 输入首尾空格过滤

### 过滤器（Vue3 已移除）
> 对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）
- 注册过滤器
    - Vue.filter(name, callback)全局过滤器
    - new Vue {filters: {}} 局部过滤器
- 使用过滤器：{{ xxx | 过滤器名}} 或 v-bind:属性 = "xxx | 过滤器名" 
- 备注：
    - 过滤器可以接收额外参数，多个过滤器也可以串联
    - 并没有改变原本的数据，而是产生新的对应的数据
- 处理时间的库 moment 体积较大 dayjs 轻量级
）
## 内置指令
- 之前学过的指令：
    - v-bind	单向绑定解析表达式，可简写为 :
    - v-model	双向数据绑定
    - v-for		遍历数组/对象/字符串
    - v-on		绑定事件监听，可简写为@
    - v-show	条件渲染 (动态控制节点是否展示)
    - v-if		条件渲染（动态控制节点是否存存在）
    - v-else-if	条件渲染（动态控制节点是否存存在）
    - v-else	条件渲染（动态控制节点是否存存在）

### v-text
> 作用：向其所在的节点中渲染文本内容 

- 与插值语法的区别：v-text 会替换掉节点中的内容，{{xxx}}则不会，更灵活

### v-html
> 作用：向指定节点中渲染包含html结构的内容 

- 与插值语法的区别： 
    - v-html会替换掉节点中所有的内容，{{xxx}}则不会
    - v-html可以识别html结构
- 严重注意v-html有安全性问题！！！ 
    - 在网站上动态渲染任意html是非常危险的，容易导致 XSS 攻击
    - 一定要在可信的内容上使用v-html，永远不要用在用户提交的内容上！！！

### v-cloak
- 本质是一个特殊属性，Vue 实例创建完毕并接管容器后，会删掉 v-cloak 属性
- 使用 css 配合 v-cloak 可以解决网速慢时页面展示出 {{xxx}} 的问题

### v-once
- v-once 所在节点在初次动态渲染后，就视为静态内容了 
- 以后数据的改变不会引起 v-once 所在结构的更新，可以用于优化性能

### v-pre
- 跳过 v-pre 所在节点的编译过程
- 可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译

## 自定义指令

语法：
```javascript
// 局部指令
new Vue({															
  directives:{ 
    指令名:配置对象 
  }   
})

new Vue({															
  directives:{ 
    指令名:回调函数 
  }   
})

// 全局指令
// Vue.directive(指令名, 配置对象)
// 或
// Vue.directive(指令名, 回调函数)

Vue.directive('fbind', {
    // 指令与元素成功绑定时（一上来）
    bind(element, binding) {	// element就是DOM元素，binding就是要绑定的
      element.value = binding.value
    },
    // 指令所在元素被插入页面时
    inserted(element, binding) {
      element.focus()
    },
    // 指令所在的模板被重新解析时
    update(element, binding) {
      element.value = binding.value
    }
})
```

- 配置对象中常用的3个回调函数 
    - bind(element, binding)	 指令与元素成功绑定时调用
    - inserted(element, binding)指令所在元素被插入页面时调用
    - update(element, binding)	 指令所在模板结构被重新解析时调用
- element 就是 DOM 元素，binding 就是要绑定的对象，它包含以下属性：namevalueoldValueexpressionargmodifiers
- 备注
    - 指令定义时不加v-，但使用时要加v-
    - 指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名  

## 生命周期
> 又名生命周期回调函数、生命周期函数、生命周期钩子
> 生命周期函数中的 this 指向是 vm 或组件实例对象

[Lifecycle-Diagram](https://v2.vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)
![advator](https://v2.vuejs.org/images/lifecycle.png)
- 常用的生命周期钩子
    - mounted：发送 ajax 请求、启动定时器、绑定自定义事件、订阅消息等初始化操作 
    - beforeDestroy：清除定时器、解绑自定义事件、取消订阅消息等收尾工作 
- 关于销毁Vue实例
    - 销毁后借助Vue开发者工具看不到任何信息
    - 销毁后自定义事件会失效，但原生DOM事件依然有效
    - 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了

