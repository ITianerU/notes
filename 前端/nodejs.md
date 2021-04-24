# npm

## npm镜像

```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
```

## cnpm

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 配置依赖包仓库路径

### 方式一

- 找到nodejs安装路径/node_modules/npm/
- 编辑npmrc文件, 指定自己创建的依赖包仓库路径
- 修改环境变量Path, 添加依赖包仓库路径

### 方式二

```shell
# npm
npm config set prefix "D:\node_package\npm"
npm config set cache "D:\node_package\npm_cache"

# yarn
yarn config set global-folder "D:\node_package\yarn"
yarn config set cache-folder "D:\node_package\yarn_cache"
```



# Express

#### 安装

```shell
cnpm install express-generator -g
```

#### 创建项目

```shell
express projectname
# 创建好之后,安装相关的包
cnpm install
```

#### 运行

```shell
# 默认以3000端口启动
# windows启动
set DEBUG=projectname & npm start
# linux启动 
DEBUG=myapp npm start
```

#### 热部署

安装supervisor

```shell
cnpm install -g supervisor
```

配置软连接

```shell
sudo ln -s /usr/local/node路径/bin/supervisor /usr/local/bin/
```

使用热部署启动项目

```
supervisor bin/www
```

#### 安装加密工具

```js
cnpm install crypto -save
// 使用
var md5 = crypto.createHash('md5');
var token_before = id + init_token;
return md5.update(token_before).digest('hex');
```

#### 安装mongoose

```shell
cnpm install mongoose --save
```

#### mongoose连接数据库

```js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/pets?authSource=用户', {
    user: '用户',
    pass: '密码'
  })
```

#### 创建数据集

```js
// 创建
var user = new mongoose.Schema({
    username: String
})
// 绑定方法
user.statics.findAll = (callBack) => {
    this.find({}, callBack);
};
// 生成模型
var userModel = mongoose.model('user', user);
```

