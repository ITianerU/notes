# 简介

主要用于多组件数据共享, 全局状态管理, 并实现了响应式，主要由state, getter, mutation, action四部分组成

## 常存放哪些属性

- 登录状态
- 用户名称, 头像
- 位置信息
- 收藏
- 购物车

# 安装

```
npm install vuex
```

# 目录结构

- stroe
  - index.js
  - actions.js
  - mutations.js
  - getters.js
  - modules
    - cart.js    # 购物车模块
    - products.js   # 产品模块

# 使用

## 引用

在根目录下创建 stroe/index.js

注: 建议对state, getter, mutations, action, modules拆分成不同的js文件

```js
import Vuex from "vuex"
// 引用
Vue.use(Vuex)
```

在main.js中挂载

```js
new Vue({
    store
})
```

## state

```js
const store = new Vuex.store({
    state: {
        count: 0
    }
})
```

### 获取数据

#### 直接使用

```js
// 在页面中读取
{{$store.state.count}}
```

#### 计算属性

```js
// 或者通过计算属性的方式
computed: {
	count(){
		return this.$store.state.count;
	}
}
```

### state辅助函数

```js
import {mapState} from 'vuex';

const zj = {
    template: '<div>{{ count }}</div>'
    // mapState可直接从state获取数据
	computed: mapState(['count'])
    // 函数写法, 可对数据做操作后返回
    computed: mapState({
        count: (state) => state.message.length
    })
	// 也可使用展开操作符
	computed: {
        ...mapState({
        	count: (state) => state.message.length
    	})
    }
}
```

## getter

类似计算属性

### 创建

```js
const store = new Vuex.store({
    state: {
        count: 0
    },
    getters: {
        doubleCount(state){
            return state.count * 2;
        }
    }
})
```

### 调用

```js
$store.getters.name
```

### getters调用getters

getters中的函数的第二个参数, 可调用getters中的其他函数

```js
getters: {
    name1: 处理函数,
    name2: (state, getters) => getters.name1
}
```

### 接收参数

getters可以通过闭包, 返回一个函数, 该函数可以传递参数

该方法, 不会缓存计算结果

```js
getters: {
    name(state){
        return function(num){
            return  state.count * num;
        }
    }
}
```



### getter辅助函数

```js
import {mapGetters} from 'vuex';
// 数组写法
computed: mapGetters(["name"])
// 对象写法
computed: mapGetters({
    name: 'name'
})
```

## mutations

用于修改数据, 只能同步修改

动态添加要使用Vue.set(obj, key, value), 动态删除Vue.delete(obj, key)

### 创建

```js
const store = new Vuex.store({
    state: {
        count: 0
    },
    mutations: {
    	// 修改数据的方法，第一个参数为数据仓库，后面参数数量随意，调用时提供
		countIncrease(state, ...){
			state.count++
		}
	}
})
```

### 使用

```js
// 在methods中创建方法
methods:{
    // 方法名
	countIncrease(){
        // 调用数据仓库的commit方法，参数为mutations的方法名
		this.$store.commit('countIncrease');
        // 后面的参数为调用的方法的参数
         this.$store.commit('countIncrease', ...);
         // 或, 使用这种方法提交的是一个对象
         this.$store.commit({
         	type: 'countIncrease' ,
            ...
         });
	}
}
```

### 类型常量

官方推荐在定义mutation事件时, 先创建一个常量

```js
const COUNT = "count"
const store = new Vuex.store({
    state: {
        count: 0
    },
    mutations: {
    	// 使用[COUNT]方式, 创建事件
		[COUNT](state, ...){
			state.count++
		}
	}
})
```



### 辅助函数

与getters的辅助函数用法相同   

```js
import {mapMutations} from 'vuex';
```

import {mapGetters} from 'vuex';

## action 

可异步变更数据, 需要与mutation配合

### 创建

```js
export default new Vuex.Store({
    state: {
        messages: []
    },
    mutations: {
        addMessages(state, newMessage){
            state.messages.push(...newMessage);
        }
    },
    actions: {
        // context只Store对象, 在action中在调用mutation
        getMessages(context, ...) {
            fetch(url).then(
            	(res) => res.json()
            ).then(
                (data) => {
                    context.commit('addMessages', data.messages)
                }
            )
        }
    }
})
```

### 调用

```js
// 调用
this.$store.dispatch("getMessages", ...)
```

### action辅助函数

```js
// 用法与前面相同
import {mapActions} from 'vuex';
```

### Promise

控制异步请求结束后执行的代码

```js
actions: {
    getMessages(context) {
        // 添加return, 返回promise对象
        return fetch(url).then(
            (res) => res.json()
        ).then(
            (data) => {
                context.commit('addMessages', data.messages)
            }
        )
    }
}
```

```js
// 调用
this.$store.dispatch("getMessages").then(
	// 确保异步加载完成,执行这里的代码
)
```

## modules

模块化, 防止state太大导致难以维护, 对state划分模块

### 创建

```json
const modulesA = {
    state:{},
    // mutations不要重复    
    mutations:{},
    // actions不要重复   
    actions: {
        // context指的是, 当前的modoule对象, 只能使用自己的模块的mutations
        xxx(context){
            // 拿到跟节点的getters, state 
            context.rootGetters
            context.commit("")
        }
    },
    // getters不要重复 
    getters: {
        // rootState为主state
        getCount(state, getters, rootState){
            return 
        }
    }
}

state:{},
mutations:{},
actions: {},
getters: {},
modules: {
    a: modulesA
}
```

### 调用

```js
// 获取属性
this.$store.state.a.xxx
// mutations, actions, getters 和之前的用法相同, 他会优先在主mutations中找, 找不到再去modules中找
this.$store.commit("xxx")
```

