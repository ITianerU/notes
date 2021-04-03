# vue-cli

3和4使用方法相同

脚手架3在路由的子路由children中，不能使用template，而是要引用单独的组件文件

#### 安装

```shell
npm install -g @vue/cli
cnpm install -g @vue/cli
```

#### 图形界面

```shell
vue ui
```

#### 创建项目

```
vue create vue-cli3-demo
```

#### 启动项目

```
yarn serve 或者 npm run serve
```

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
- Runtime-only   推荐选这个, 包体积更小, 运行效率更高

### 安装/卸载依赖

```
-- save 生产环境 --save-dev 开发环境
cnpm install eslint --save
cnpm uninstall eslint --save
```

### 安装全部依赖

```
npm install
```

### 路由配置

```js
import First from '@/components/First'
import A from '@/components/A'
{
    path: '/first',
    name: 'First',
    component: First，
    // 子路由
    children: [
        {
          path: '/a1',
          component: A
        }
    ]
},
```

### 启动项目

```
npm run dev
```

### 页面跳转

```html
<router-link to="/"></route>
```

### 子路由显示

子路由跳转的组件不会在新页面打开，而是在如下的代码块中展示

```html
<router-view></router-view>
```

# 热更新

在项目根目录创建vue.config.js文件

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

