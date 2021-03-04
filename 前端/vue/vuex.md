# Vuex

主要用于多组件数据共享，主要由state, getter, mutation, action四部分组成

#### 安装

```
npm install vuex
```

#### 使用

```js
// 在main.js中引入
import Vuex from "vuex"
// 引用
Vue.use(Vuex)
```

#### state 数据仓库

```js
~ main.js
// 创建Vuex数据仓库实例，也可单独建文件使用import引入
const store = new Vuex.store({
    state: {
        count: 0
    }
})

new Vue({
    // 挂在到vue实例上
    store，
    。。。
})
```

#### 获取数据

```js
// 在页面中读取
{{this.$store.state.count}}
// 或者通过计算属性的方式
computed: {
	count(){
		return this.$store.state.count;
	}
}
// 设置好计算属性后，读取计算属性
{{count}}
```

#### state辅助函数

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

#### getter

多个组件使用通一条数据,并对该数据做处理时,可将处理数据的函数,拿到store内部

```js
const store = new Vuex.store({
    state: {
        count: 0
    },
    getters: {
        name: 处理函数
    }
})
```

```js
// 调用
this.$store.getters.name
```

getters中的函数的第二个参数, 可调用getters中的其他函数

```js
getters: {
        name1: 处理函数,
        name2: (state, getters) => getters.name1
}
```

#### getter辅助函数

```js
import {mapGetters} from 'vuex';
// 数组写法
computed: mapGetters(["name"])
// 对象写法
computed: mapGetters({
    name: 'name'
})
```

#### mutation  修改数据

mutation只能同步修改

```js
// new Vue({}) 添加mutations的配置
new Vue({
    // 挂在到vue实例上
    store，
    mutations: {
    	// 修改数据的方法，第一个参数为数据仓库，后面参数数量随意，调用时提供
		countIncrease(state, ...){
			state.count++
		}
	}
})
```

```js
// 在methods中创建方法
methods:{
    // 方法名
	countIncrease(){
        // 调用数据仓库的commit方法，参数为mutations的方法名
		this.$store.commit('countIncrease');
        // 后面的参数为调用的方法的参数
         this.$store.commit('countIncrease', ...);
	}
}
// 大致步骤为，先创建一个数据仓库store，定义数据集
// 定义mutations，操作数据集的方法
// 在组件页面通过调用mutations中的方法来修改数据集
```

#### mutation辅助函数

与getters的辅助函数用法相同   

```js
import {mapMutations} from 'vuex';
```

import {mapGetters} from 'vuex';

#### action  提交修改

可异步变更数据

```js
import Vuex from 'vuex';
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
        getMessages(context) {
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

```js
// 调用
this.$store.dispatch("getMessages")
```

#### action辅助函数

```js
// 用法与前面相同
import {mapActions} from 'vuex';
```

#### Promise

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

