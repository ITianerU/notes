# 安装

```shell
cnpm install -g create-react-app
```

# 创建项目

```shell
create-react-app xxxx
```

# 命令

## 启动

```shell
yarn start
```

## 打包

```shell
yarn build
```

## 测试

```shell
yarn test
```

## 暴露全部webpack配置文件到项目中(不可逆)

```shell
yarn eject
```

# 目录解构

- **public**   模板静态文件夹
  - **favicon.ico**                浏览器tab图标
  - **index.html**                 模板
  - **logo.png**                    react logo. 用于壳的图标
  - **manifest.json**            安卓/ios的壳配置文件
  - **robots.txt**                  爬虫协议
- src
  - **App.css**                     入口组件样式
  - **App.js**                        入口组件
  - **App.test.js**                 测试
  - **index.css**                   公共样式
  - **index.js**                      入口js  相当于 vue的main.js
  - **logo.svg**                     logo图片
  - **reportWebVitals.js**     检测页面渲染性能
  - **setupTests.js**              用于组件测试

# 入口文件配置

```jsx
ReactDOM.render(
  // <React.StrictMode> 使用react的严格模式, 不推荐使用的语法, 会被警告
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

