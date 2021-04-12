# vue-cli2

如果想使用脚手架2, 需要安装一个全局桥接工具

## 安装

```
cnpm install -g @vue/cli-init
```

## 创建项目

```shell
vue init webpack projectName
```

### Vue build选项

- Runtime + Compiler  

  在main.js中代码如下, vue项目运行过程为, template->解析成->ast抽象语法树->编译->render()->render渲染成->虚拟dom->真实dom

  ```js
  new Vue({
      el: "#app",
      template: "<App/>",
      components: { App }
  })
  ```

- Runtime-only   推荐选这个, 包体积更小, 运行效率更高

  在main.js中代码如下, vue项目运行过程为, render()->render渲染成->虚拟dom->真实dom

  ```js
  new Vue({
      el: "#app",
      render: h => h(app)
  })
  ```

  h会传入一个方法 createElement(), 该方法会创建dom节点

  createElement()参数

  - 用法一
    - 标签名(div, h1等)
    - 标签的属性{height: "80px"}
    - 标签的内容["hello world", createElement()]  也可以嵌套createElement方法
  - 用法二
    - 传入一个vue文件对象

  

### 启动项目

```
npm run dev
```

# vue-cli3

3和4使用方法相同

脚手架3在路由的子路由children中，不能使用template，而是要引用单独的组件文件

## 安装

```shell
npm install -g @vue/cli
cnpm install -g @vue/cli
```

## 图形界面

```shell
vue ui
```

## 创建项目

```
vue create vue-cli3-demo
```

## 启动项目

```
yarn serve 或者 npm run serve
```

## 配置

vue-cli3取消了vue-cli2的大量配置文件, 如果想要修改配置, 需要在根目录自己创建一个vue.config.js

```js
module.exports = {
    
}
```

# vue.config.js

在项目根目录创建vue.config.js文件

## 热更新

```js
module.exports = {
  devServer: {
    hot: true,
    open: true,
    port: 8080,
    host: 'localhost'
  }
}
```

## 取消代码打包压缩

```js
module.exports = {
  configureWebpack: {
    optimization: {
      minimize: false,
    },
  },
};
```

## 配置访问路径别名

在html中使用, 需要在路径前加~

```js
const path = require("path");  // 引入path
function resolve(dir) {    // 封装一个通用方法
    return path.resolve(__dirname, dir);
}

module.exports = {
    chainWebpack: config => {
        config.resolve.alias
            .set('views', resolve('src/views'))
    }
}
```

