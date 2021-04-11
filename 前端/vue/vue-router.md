# 原理

vue是单页面富应用, 会一次行下载全部的资源数据, 使用路由切换页面时, 会替换路径的hash值或者使用history模式, 并不是替换路径的url

这样的好处是, 替换hash或history不会刷新整体页面, 不会从服务器重新获取一边静态资源, js代码只需要监听hash的改变, 来加载对应的html和js

## js修改的方式

vue项目默认是hash模式, hash模式url中会出现#号

### hash模式

```
window.location.hash = "hashValue"
```

### history模式

配置history模式

```js
const router = new VueRouter({
  mode: 'history',    // 这里添加
  base: process.env.BASE_URL,
  routes
})
```

修改方法

```js
// 会push到一个栈中
window.history.pushState({}, "", "hashValue")
// 前进一层
window.history.forward()
// 回退上一层
window.history.back()
// 跳到某一层, 负数为回退, 正数为前进
window.history.go(num)
// 替换当前栈
window.history.replaceState({}, "", "hashValue")
```

## $route和$router

- $route指代的是当前处于活动状态的路由
- $router指代的是, 在路由配置文件中new出来的路由对象

# 路由配置

```js
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },{
    path: '/about',
    redirect: "/"    // 使用重定向, 到首页
  }
]
```

# 跳转页面

## 页面显示

用于展示路由配置的页面

```html
<router-view></router-view>
```

## 页面跳转

最终会被渲染成a标签

```vue
<router-link to="url"></router-link>
```

### tag属性(在vue-router4已被移除)

tag可设置指定的标签渲染,包裹在a标签外部

```vue
<router-link to="url" tag="li"></router-link>
```

### replace

router-link在页面跳转时, 会在页面栈中添加一条记录, 如果不想用户可以回退, replace属性

```html
<router-link to="url" replace></router-link>
```

### active-class属性

当router-link被点击时, 会自动生成一个类 router-link-active, 可以在css中设置router-link-active的样式

也可通过active-class属性, 指定生成的类

注: 使用时, router-link 的to属性, 尽量不要指定 "/" 路由, 可能会出现, router-link-active一直存在的问题

```vue
<!-- 这里指定生成的类名为active -->
<router-link to="url" tag="li" active-class="active"></router-link>
```

```css
active{
    color: red
}
```

或者也可以在router的配置文件中统一配置

```js
const router = new VueRouter({
  mode: 'history',    
  base: process.env.BASE_URL,
  linkActiveClass: "active",  // 在这里加
  routes
})
```

### 绑定事件

router-link属于一个组件, 想在父组件给组件绑定事件, 需要使用.native

```vue
<router-link to="url" @click.native="method"></router-link>
```

### js跳转

```js
this.$router.push("url")
```

```js
// 替换当前的history记录, 返回键不会回到上一个,而是回到上一个的上一个
this.$router.replace("url")
```

```js
// 跳转到指定的历史记录页
this.router.go()
```

# 传值

## 路径传值

路径需配置传值的属性名，使用:号拼接

### 配置

```js
{
    path: '/home/:attrname',
    name: 'Home'
}
```

### 传值

```vue
<router-link to="/home/123"></router-link>
```

或

```js
// 传值, name指的是路由中配置的name属性, 不能使用path
this.$router.push({name: "Home", params:{msg: "hello"}})
this.$router.push("/home/" + msg)
```

### 取值

```html
<!-- 值写在路径中，在模板中通过如下方式获取-->
<div>{{$route.params.attrname}}</div>
```

## 参数传值

拼在路径的后面?name=value的形式

### 传值

```vue
<router-link :to="{path: '/home', params: {msg: 'hello'}}"></router-link>
```

```js
// 传值, path指的是路由中配置的path属性, 也可以使用name
this.$router.push({path: "/about", query:{msg: "hello"}});
```

### 取值

```html
<!-- 取值方式 -->
<div>{{$route.query.attrname}}</div>
```

# 子路由

使用子路由有两个步骤

- 创建子组件, 并配置到路由中
- 组件内部使用<router-view>标签

## 配置子路由

```js
{
    path: '/home',
    name: 'Home',
    component: Home,
    children: [
      {
        path: '',          // 默认显示的子路由
        redirect: 'message'
      },
      {
        path: 'message',   // 子路由不需要加 /
        name: 'Message',
        component: Message
      },{
        path: 'news',
        name: 'News',
        component: News
      }
    ]
},
```

### Home.vue

```vue
<template>
  <div class="home">
    <router-link to="/home/message" tag="Button">Message</router-link> |
    <router-link to="/home/news" tag="Button">News</router-link>
    <router-view></router-view>
  </div>
</template>
```

# 命名视图

当一个页面有多个<router-view>，可通过命名区分

```vue
<router-view></router-view>
<router-view name="name1"></router-view>
<router-view name="name2"></router-view>
```

```json
{
    path: '/first',
    name: 'first',
    components: {
        default: component
    	name1: componentA,
    	name2: componentB
    }
 }
```

# 别名

- 别名

  ```js
  // a路径与b路径都可访问
  routes: [
  	{
  	 	path: '/a',
  	 	alias: '/b'
  	}
  ]
  ```


# 懒加载

在打包时, 将路由对应的组件打包成一个个的js代码块

只有在路由被访问到的时候, 才加载对应的组件

## 方式一(不常用)

```js
const Home = resolve => {require.ensure(['../components/Home.vue'], () => {resolve(require('../components/Home.vue'))})}
```

## 方式二(不常用)

```js
const Home = resolve => require(['../components/Home.vue'], resolve)
```

## 方式三

```js
const Home = () => import('../components/Home.vue')
```

# 导航守卫

## 全局守卫

在路由配置的js中添加

### 前置守卫

```js
// 动态修改网页title,  在路由中配置meta: {title: "name"}
router.beforeEach((to, from, next) => {
  document.title = to.meta.title
  next()
})
```

### 后置钩子

```js
router.afterEach((to, from) => {
})
```

## 路由独享守卫

```js
{
    path: '/',
    name: 'Home',
    component: Home,
    beforeEnter: (to, from, next) => {
        
    }
}
```

## 组件内守卫

在组件内部定义

```js
beforeRouteEnter(to, from, next){}
beforeRouteUpdate(to, from, next){}
beforeRouteLeave(to, from, next){}
```

# keep-alive

keep-alive是vue的组件, 不是vue-router的

会缓存页面, 防止页面回退后, 状态丢失

使用keep-alive后, 不会执行销毁的声明周期函数

## 生命周期

keep-alive增加了两个生命周期函数

```js
// 该页面处于活跃状态, 必须搭配<keep-alive>使用, 只能作用在路由绑定的页面上
activated(){},
// 不活跃状态
deactivated(){},
```

## 使用

```vue
<!-- exclude 排除掉哪些页面不缓存, 参数为页面的name, 多个name用逗号分隔 -->
<!-- include 指定哪些页面会缓存, 参数为页面的name, 多个name用逗号分隔 -->
<keep-alive exclude="Home,About" include="">
	<router-view></router-view>
</keep-alive>
```

## 注

如果出现页面中也包含router-view的情况, 如果想要保持页面的状态, 需要取消掉子路由的 "/" 路由

使用beforeRouteLeave(to, from, next){}在页面中保存当前页面的path

然后在activated(){}方法中,  $router.push(path)









