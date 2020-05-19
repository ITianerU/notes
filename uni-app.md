# 创建项目

- HBuilderX创建

文件->新建->项目->uni-app

- vue-cli创建

```
# dcloudio/uni-preset-vue是自定义模板
vue create -p dcloudio/uni-preset-vue 项目名
```

# 配置小程序开发工具

## 配置

运行->运行到小程序模拟器->运行设置->小程序运行配置

填写开发者工具路径(开发者工具的安装目录)

## 测试

运行->运行到小程序模拟器->xxx开发者工具

## manifest.json

app基础配置, 及各先程序配置

# 开发

## 新建页面

右键点击pages文件夹, 新建页面

## 配置路由

在pages.json下添加(在新建页时, 会自动添加路由)

pages中的第一个页面, 将会显示为首页(进入小程序, 默认显示的页面)

```json
"pages": [
    {
        "path": "pages/index/index",  // 页面路径
        "style": {
            "navigationBarTitleText": "首页"  // 页面标题
        },
        "enablePullDownRefresh": true // 开启页面刷新
    }
]
```

## 配置tabBar

在pages.json下添加

```json
"tabBar": {
    "color": "#7A7E83",   // 文字颜色
    "selectedColor": "#3cc51f",  // 选中后的文字颜色
    "borderStyle": "black",   // 边框颜色
    "backgroundColor": "#ffffff",  // 背景颜色
    "list": [{   // tab 按钮
        "pagePath": "pages/index/index",  // 点击跳转的页面路径
        "iconPath": "static/image/icon_component.png",  // 按钮图标
        "selectedIconPath": "static/image/icon_component_HL.png",  // 选中后的图标
        "text": "首页"  // 按钮名称
    }]
}
```

list中配置顺序, 代表tab按钮顺序

## 全局样式

```json
"globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "uni-app",
    "navigationBarBackgroundColor": "#F8F8F8",
    "backgroundColor": "#F8F8F8",
    "enablePullDownRefresh": true // 开启下拉刷新
}
```



## main.js

入口文件

## App.vue

 配置全局样式, app生命周期函数

## 全局事件

常用于父子组件传值

### 父页面

```js
// 父页面加载时, 调用生命周期函数onLoad
onLoad() {
    // 在全局注册方法
    uni.$on("testUniOn", (data) => {
        console.log(data.name);
    })
    // once注册方法, 只会被调用一次
    uni.$once("testUniOnce", (data) => {
        console.log(data.name);
    })
}
```

### 子组件

```html
<button type="primary" @click="test">event</button>
```

```js
methods:{
    test(){
 		// 子组件可调用全局注册的方法 
        uni.$emit("testUniOn", {name: 'ITIanerU'});
    }
}
```

## 生命周期

### app生命周期

| 函数名            | 说明                                           |
| :---------------- | :--------------------------------------------- |
| onLaunch          | 当`uni-app` 初始化完成时触发（全局只触发一次） |
| onShow            | 当 `uni-app` 启动，或从后台进入前台显示        |
| onHide            | 当 `uni-app` 从前台进入后台                    |
| onUniNViewMessage | 对 `nvue` 页面发送的数据进行监听               |

### 页面生命周期

| 函数名                              | 说明                                                         | 平台差异说明                                         | 最低版本 |
| :---------------------------------- | :----------------------------------------------------------- | :--------------------------------------------------- | :------- |
| onLoad                              | 监听页面加载，其参数为上个页面传递的数据，参数类型为Object（用于页面传参） |                                                      |          |
| onShow                              | 监听页面显示。页面每次出现在屏幕上都触发，包括从下级页面点返回露出当前页面 |                                                      |          |
| onReady                             | 监听页面初次渲染完成。注意如果渲染速度快，会在页面进入动画完成前触发 |                                                      |          |
| onHide                              | 监听页面隐藏                                                 |                                                      |          |
| onUnload                            | 监听页面卸载                                                 |                                                      |          |
| onResize                            | 监听窗口尺寸变化                                             | App、微信小程序                                      |          |
| onPullDownRefresh                   | 监听用户下拉动作，一般用于下拉刷新                           |                                                      |          |
| onReachBottom                       | 页面滚动到底部的事件（不是scroll-view滚到底），常用于上拉加载下一页数据。如使用scroll-view导致页面级没有滚动，则触底事件不会被触发 |                                                      |          |
| onTabItemTap                        | 点击 tab 时触发，参数为Object，具体见下方注意事项            | 微信小程序、百度小程序、H5、App（自定义组件模式）    |          |
| onShareAppMessage                   | 用户点击右上角分享                                           | 微信小程序、百度小程序、字节跳动小程序、支付宝小程序 |          |
| onPageScroll                        | 监听页面滚动，参数为Object                                   |                                                      |          |
| onNavigationBarButtonTap            | 监听原生标题栏按钮点击事件，参数为Object                     | App、H5                                              |          |
| onBackPress                         | 监听页面返回，返回 event = {from:backbutton、 navigateBack} ，backbutton 表示来源是左上角返回按钮或 android 返回键；navigateBack表示来源是 uni.navigateBack ；详细说明及使用：[onBackPress 详解](https://ask.dcloud.net.cn/article/35120) | App、H5                                              |          |
| onNavigationBarSearchInputChanged   | 监听原生标题栏搜索输入框输入内容变化事件                     | App、H5                                              | 1.6.0    |
| onNavigationBarSearchInputConfirmed | 监听原生标题栏搜索输入框搜索事件，用户点击软键盘上的“搜索”按钮时触发。 | App、H5                                              | 1.6.0    |
| onNavigationBarSearchInputClicked   | 监听原生标题栏搜索输入框点击事件                             | App、H5                                              | 1.6.0    |

### 组件生命周期

```js
beforeMount(){
    console.log("在挂在之前被调用");
},
    mounted(){
        console.log("挂载之后被调用");
        // $nextTick在dom元素渲染完之后,再执行
        this.$nextTick(function(){
            // 渲染完毕
            console.log("ok");
        })
    }
```

## 路由

### uni.navigateTo

保留当前页面，跳转到应用内的某个页面，使用`uni.navigateBack`可以返回到原页面。

nui.navigateTo会打开新页面, 不关闭源页面, 超出页面堆栈会无法使用

无法跳转与tabBar绑定的页面

```js
//在起始页面跳转到test.vue页面并传递参数
uni.navigateTo({
    url: 'test?id=1&name=uniapp'
});
```

```js
// 在test.vue页面接受参数
export default {
    onLoad: function (option) { //option为object类型，会序列化上个页面传递的参数
        console.log(option.id); //打印出上个页面传递的参数。
        console.log(option.name); //打印出上个页面传递的参数。
    }
}
```

### uni.redirectTo

关闭当前页面，跳转到应用内的某个页面。

无法跳转与tabBar绑定的页面

```js
uni.redirectTo({
    url: 'test?id=1'
});
```

### uni.reLaunch

关闭所有页面，打开到应用内的某个页面, 可打开所有页面

```js
uni.reLaunch({
    url: 'test?id=1'
});
```

### uni.switchTab

跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面。

无法跳转非tabBar绑定的页面

```js
uni.switchTab({
    url: '/pages/index/index'
});
```

### uni.navigateBack

关闭当前页面，返回上一页面或多级页面。可通过 `getCurrentPages()` 获取当前的页面栈，决定需要返回几层。

H5端页面刷新之后页面栈会消失，此时`navigateBack`不能返回，如果一定要返回可以使用`history.back()`导航到浏览器的其他历史记录。

```js
// 注意：调用 navigateTo 跳转时，调用该方法的页面会被加入堆栈，而 redirectTo 方法则不会。见下方示例代码
// 在C页面内 navigateBack，将返回A页面
uni.navigateBack({
    delta: 2
});
```

