# VueCli
> 基于 Vue.js 进行快速开发的完整系统

## 优势
- 方便下载依赖
- 快速开始零配置**原型开发**
- 一个运行时依赖（cli-service）
    - 可升级
    - 基于 webpack 构建
        - > webpack 打包方式：编译好的项目源码 > 部署到服务器直接使用
    - 可通过项目内的配置文件进行配置
    - 可以通过插件进行扩展（v-charts, elementui）
- 一套完全图形化的创建和管理 Vue.js 的用户界面

## 安装
```bash
npm install -g vue-cli
```

## 使用

### 初始化项目
```bash
vue init webpack projectName
```

### 启动项目
根目录下执行
```bash
npm start
```

### 项目结构
- app 项目名称
    - build：webpack 打包使用的 build 依赖
    - config：配置目录
    - node_modules：管理项目中使用的依赖
    - src：编写 vue 源码
        - assets：存放静态资源
        - components：Vue 组件
        - router：项目路由
        - App.vue：项目根组件
        - main.js：项目入口
    - static：其他静态资源
    - .babelrc：将 ES6 语法转换为 ES5
    - .editorconfig：项目编辑配置
    - .gitignore：git 版本忽略文件
    - .postcssrc.js：处理 CSS 兼容
    - index.html：项目主页
    - package.json：依赖管理
    - package.lock.json：对 package.json 加锁
    - README.md：项目描述

### 整合 axios
```bash
npm install axios --save-dev
```

main.js
```javascript
import axios from 'axios'

Vue.prototype.$http = axios // 修改内部 $http 为 axios
```

### 打包部署
```bash
npm run build 
```
- 生成 dist 目录
    - 放到 springboot static 目录下