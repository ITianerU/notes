# 概念

是一个现代的JS应用的静态模块化打包工具

# 安装

全局安装(不常用)

```shell
npm install -g webpack
npm install -g webpack-cli
```

本地安装

```shell
# 安装到开发时依赖
npm install webpack --save-dev
npm install webpack-cli --save-dev
```

# 常用命令

## 初始化项目

```shell
# 在项目根目录下使用, 会生成一个package.json文件
npm init
```

## 查看版本

```shell
webpack -v
```

## 打包

### 方式一

```shell
# 不常用
npx webpack ./src/源文件 -o ./dist --mode development  
```

参数--mode

- development
- production
- none

### 方式二

在项目根目录下创建webpack.config.js

```js
// 从node中导入path模块
const path = require('path')

module.exports = {
    // 打包的源代码路径
    entry: './src/main.js',
    // 打包之后导出的路径
    output: {
        // path.resolve拼接路径, __dirname是node自带的全局变量
        path: path.resolve(__dirname, 'dist'),
        // 打包生成的压缩文件名
        filename: 'bundle.js',
        // 防止index.html找不到静态资源, 值为静态资源的路径
        publicPath: 'dist/'
    },
}
```

输入命令

```shell
# 在项目根目录下使用, 参数和方式一相同
webpack --mode development  
```

### 方式三(常用)

结合方式二的webpack.config.js文件

优点, 使用这种方式, 会优先使用本地的webpack 

```json
// 在package.json中配置scripts, 脚本指令, 可以自定义
"build": "webpack --mode production"     
```

输入命令

```shell
npm run build
```

# loader

补充webpack没有的功能, 加载css, 图片, ES6转ES5, TS转ES5, scss, less转css, .vue转.js等

## css-loader & style-loader 

css-loader是css加载器, style-loader是css解析器, 不使用css不生效

### 安装

```shell
npm install --save-dev css-loader
npm install --save-dev style-loader
npm install --save-dev less-loader less
```

### 配置

在webpack.config.js补充module

```js
module.exports = {
    module: {
        rules: [
            {
                // 哪些文件生效
                test: /\.css$/,
                // webpack使用多个loader时, 从右向左读, 顺序不能改变
                use: ['style-loader','css-loader']
            }
        ]
    }
}
```

### 打包

```js
// 在使用css时, 不需要再html中引入, 要再js中使用import或者require引入, 才会被webpack打包
import "xxx.css"
```

## less-loader

less-loader将less文件, 解析为css

### 安装

```shell
npm install --save-dev less-loader less
```

### 配置

在webpack.config.js补充module中rules

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.less$/,
                use: ['style-loader','css-loader', 'less-loader']
            }
        ]
    }
}
```

## url-loader & file-loader

用于加载图片, 图片在加载时, 会去检查url-loader配置的limit大小, 小于该值, 会被转换成base64加载

大于limie值, 会使用file-loader加载

### 安装

```shell
cnpm install url-loader --save-dev
```

### 配置

在webpack.config.js补充module中rules

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.(png|jpg|gif|jpeg)$/,
                use: [{
                    loader: 'url-loader',
                    options: {
                        limit: 8192,
                        // img表示打包后创建一个img文件夹
                        // [name].[hash:8].[ext] 表示生成的文件名的规则
                        // [name]使用原有的文件名, 拼接[hash:8]8为hash值, 拼接[ext]文件原有的后缀名
                        name: 'img/[name].[hash:8].[ext]'  
                    }  
                }]
            }
        ]
    }
}
```

## babel-loader

es6转es5

### 安装

```shell
cnpm install --save-dev babel-loader@7 babel-core babel-preset-es2015
```

### 配置

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['es2015']
                    }
                }
            },
        ]
    }
}
```

## vue-loader

对vue文件进行编译

### 安装

```shell
cnpm install --save-dev vue-loader vue-template-compiler
```

### 配置

```js
const {VueLoaderPlugin} = require('vue-loader')
module.exports = {
    module: {
        rules: [
            {
                test: /\.vue$/,
                use: ['vue-loader']
            }
        ]
    },
    plugins: [
        new VueLoaderPlugin()    // 引入vueloader插件
    ]
}
```

# Vue

## 安装

```shell
cnpm install -S vue
```

## 配置

在webpack.config.js中配置vue的编译器为runtime-compiler

```js
module.exports = {
    resolve: {
        alias: {
            // 在安装的vue中找到vue.esm.js, 这个文件中包含runtime-compiler
            // 当import vue的时候, 会来这里找是否指定了, 要使用的编译器
            // 如果编译报错, 在import vue时, 可手动指定导入node_modules/vue/dist/vue.esm
            'vue$': 'vue/dict/vue.esm.js'   
        }
    }
}
```

