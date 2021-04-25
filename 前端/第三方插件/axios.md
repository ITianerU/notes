# 安装

```
cnpm install -S axios
yarn add axios
```

# 引用

```js
~ main.js  全局注册
improt axios from 'axios'  //导入
Vue.prototype.$ajax=axios  //$ajax为变量名
```

# 使用

## 常用API

```js
// 默认get
axios(config)
axios.request(config)
axios.get(url[,config])
axios.delete(url[,config])
axios.head(url[,config])
axios.post(url[,data[,config])
axios.put(url[,data[,config])
axios.patch(url[,data[,config])
```

### 基础用法

```js
axios({
  url: 'http://123.207.32.32:8000/home/multidata',
  method: 'get'  // get | post | put 等
}).then((data) => {
  console.log(data);
})
```

### 传参

#### get传参

```js
axios({
  url: 'http://123.207.32.32:8000/home/data?type=sell&page=1',  // 直接拼接在路径后边
}).then((data) => {
  console.log(data);
})

axios({
  url: 'http://123.207.32.32:8000/home/data',        // 使用params传参
  params: {
      type: 'sell',
      page: 1
  }
}).then((data) => {
  console.log(data);
})
```

# 并发请求

发送多个异步请求, 想要全部完成再处理

## axios.all() / Promise.all() 

会返回一个结果数组

```js
axios.all([
  axios({
    url: 'http://123.207.32.32:8000/home/data',      
    params: {
      type: 'sell',
      page: 1
    }
  }),
  axios({
    url: 'http://123.207.32.32:8000/home/multidata',        
  })
]).then(results => {
  console.log(results)   // res是一个请求结果数组
})
```

# 全局配置

```js
axios.defaults.baseURL = 'http://123.207.32.32:8000'
axios.defaults.timeout = 5000
axios.defaults.headers.post['Content-Type'] = 'application/x-www-  form-urlencoded'
```

# 封装实例

防止这种第三方插件, 不维护导致, 更换插件需要替换每个文件的问题, 创建一个http文件夹, 保存如下文件

## config.js

```js
export default {
	baseUrl: baseUrl,
	headers: {
		'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8',
		'Access-Control-Allow-Origin': '*',
		"Access-Control-Allow-Headers": "Authorization,Origin, X-Requested-With, Content-Type, Accept",
		"Access-Control-Allow-Methods": "PUT,POST,GET,DELETE,OPTIONS"
	},
	data: {},
	timeout: 10000,
	withCredentials: true,
	responseType: 'json',
}
```

## axios.js

```js
import axios from 'axios';
import config from './config.js'
export default function $axios(options){
  // 创建实例
  const instance = axios.create({
    baseURL: config.baseUrl,
    headers: config.headers,
    timeout: config.timeout,
    withCredentials: config.withCredentials,
  });
  
  // 请求拦截器
  instance.interceptors.request.use(config => {
    return config
  }, error => {
    return Promise.reject(error)
  });
  
  // 请求响应拦截器
  instance.interceptors.response.use(response => {
    return response.data;
  }, err => {
    return Promise.reject(err)
  });
  
  return instance(options)
}
```

## userApi.js

在http文件夹下创建一个apis的文件夹, 存放实际的api. 每个api对应一个java的controller

```js
import axios from '../axios.js'

// 获取用户信息
export const userinfo = (data) => {
	return axios({url: '/baseinfo/userinfo', method: 'post', data: data})
}
```

## apis.js

将全部api统一有apis导出

```js
import * as user from './apis/userApi.js'

export default {
	user
}
```

## index.js

创建一个install函数, 在vue原型上添加一个$api属性, 返回全部注册的api

```js
import api from './apis.js'
const install = Vue => {
	if (install.installed){
		return;
	}
	install.installed = true;
	Object.defineProperties(Vue.prototype, {
		$api: {
			get(){
				return api;
			}
		}
	})
}

export default install
```

## main.js

在main.js中导入http文件夹, 会自动导入index.js

```js
import Vue from 'vue'
import api from './http'
// use方法, 会调用index.js中的install方法
Vue.use(api);
```

## 使用

```js
// this.$api 下有全部注册的api
this.$api.user.userinfo(params).then().catch()
```





