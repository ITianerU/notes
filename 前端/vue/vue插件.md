# 组件

#### 组件基础

- 创建组件并注册


```js
// 组件名称含多个单词时，需要使用驼峰命名
const CustomButton = {
      template: '<button>自定义按钮</button>'
};
new Vue({
    el: '#app',
    components: {
        CustomButton
    }
})
```

- 使用组件


```html
<div id="app">
      <!-- 组件注册时是驼峰命名，在使用时需要使用 '-' 连接 -->
      <custom-button></CustomButton>
</div>
```

- 全局注册


```js
// 组件全局注册代码需写在vue实例化代码前
Vue.component('CustomButton', {
      template: '<button>自定义按钮</button>'
})
```

- 组件数据, 计算属性

```js
// 组件中的数据使用data函数返回, 与vue数据实例不同的原因是, vue实例的data对象会在整个页面共享数据, 组件的数据每个组件独享,所以需要通过函数创建新数据对象并返回
Vue.component("customButton", {
    template: '<p>有{{ positiveNumbers.length }}个</p>',
    data() {
        return {
            numbers: [5, 4, 2, 1, -1]
        };
    },
    computed: {
        positiveNumbers() {
            // 改行使用过滤器,过滤掉负数
            return this.numbers.filter((value) => value >= 0);
        }
    }
})
```

#### 组件传值

- 传递数据

  ```js
  // 使用props属性暴露组件接口, 组件使用时, 通过props声明的属性进行传参
  Vue.component("colorButton", {
      template: '<button :style="style"></button>',
      props: ['color'],
      computed: {
          style() {
              return {
                  backgroundColor: this.color
              };
          }
      }
  })
  ```

  ```html
  // 使用
  <color-button color="red"></color-button>
  ```

- 数据校验

  ```js
  Vue.component("ruleButton", {
  	template: '<p>价格:{{ price }} 名称:{{ name }}</p>',
      props: {
          price: {
              // type为类型
              type: Number,
              // required为必填
              required: true,
              // 函数校验
              validator(v){
              	return v > 0
            	}
          },
          name: {
              type: String,
              // default为默认值
              default: "可乐"
          }
      }
  })
  ```

- 数据流(子组件实现数据与父组件双向绑定)

  ```js
  // 普通写法
  Vue.component("countFromNumber", {
        template: '<p>当前数字是 {{ number }}</p>',
        props: {
          number: {
            type: Number,
            required: true
          }
        },
        mounted(){
          setInterval(() => {
            // 使用$emit(方法名,参数).调用绑定在子组件上的方法
            // 注意这里的methodName, 与语法糖写法的区别
            this.$emit('methodName', this.number + 1);
          }, 1000)
        }
      })
  ```
  
  ```html
  // 使用 fata是父组件的data中的数据
<count-from-number :number="fdata" @methodName="val => fdata = val"></count-from-number>
  ```

  ```js
  // 语法糖写法
  Vue.component("countFromNumber", {
        template: '<p>当前数字是 {{ number }}</p>',
        props: {
          number: {
            type: Number,
            required: true
          }
        },
        mounted(){
          setInterval(() => {
            // 语法糖写法规定: update:字段
            this.$emit('update:number', this.number + 1);
          }, 1000)
        }
      })
  ```
  
```html
  <count-from-number :number.sync="fdata"></count-from-number>
```

- 组件v-model

  ```js
  // 使用v-model,组件必须监听input事件
  Vue.component("inputUsername", {
      template: '<input type="text" :value="value" @input="handleInput"></input>',
      props: {
          value: {
              type: String,
              required: true
          }
      },
      methods: {
          handleInput(e) {
              // 获取输入的数据, 转换为小写
              const value = e.target.value.toLowerCase();
              this.$emit('input', value);
          }
      }
  })
  ```

  ```html
  <input-username v-model="username"></input-username>
  ```

- 事件总线 (msg.js)

  ```js
  import Vue from 'vue'
  export default new Vue
  ```

  ```js
  import Msg from "./msg.js"
  // 存值
  Msg.$emit("k", "value");
  // 取值
  Msg.$on("k", (e) => {
  	this.kk = e;
  });
  ```

#### 插槽

- 使用

  ```js
  // 使用slot标签
  Vue.component("customButton", {
        template: '<button><slot></slot></button>',
  })
  ```

  ```html
  <custom-button>点击我</custom-button>
  ```

- 默认值

  ```js
  Vue.component("customButton", {
        template: '<button><slot>默认值,也可添加一些默认标签</slot></button>',
  })
  ```

- 命名插槽

  ```js
  Vue.component("slotTest", {
        template: `<div>
          <div>
            <slot name="header"></slot>   // 该插槽命名为header
          </div>
          <div>
            <slot></slot>
          </div>
        </div>`,
  })
  ```

  ```html
  <slot-test>
      <!-- slot属性使用命名插槽, 其他未使用slot的属性自动填充到未命名的插槽 -->
      <h2 slot="header">头部</h2>
      <p>内容</p>
  </slot-test>
  ```

- 作用域插槽

  ```js
  Vue.component("userData", {
        // :attr与等号后面的value名称需要与data中的key名称一致
        template: `<div><slot :user="user"></slot></div>`,
        props: ['username'],
        data(){
            return {
              user: {
                username: this.username,
                pwd: '1234'
              }
            }
        }
  })
  new Vue({
          el: '#app',
          data: {
          username: "LaoWang"
          }
  })
  ```
  
  ```html
<!-- v-slot声明变量适用于vue-cli 2.6之后的版本,之前的版本使用slot-scope -->
  <user-data :username="username" v-slot="userParam">
      <!-- v-slot声明的变量指代组件中的data对象 -->
  	<p v-if="userParam">{{ userParam.user.username }}</p>
  </user-data>
  ```
  

#### 自定义事件

- $emit(触发事件)

  ```js
  Vue.component("counter", {
      template: `<button @click="handleClick">单击了 {{ clicks }}</button>`,
      data(){
          return {
              clicks: 0
          }
      },
      methods: {
          handleClick() {
              this.clicks++;
              // 调用绑定的组件上的方法
              this.$emit('count', this.clicks);
          }
      }
  })
  new Vue({
      el: '#app',
      data: {
          clicks: 0
      },
      methods: {
          handleCount(clicks) {
              this.clicks = clicks;
          }
      }
  })
  ```

  ```html
  <!-- 绑定自定义事件 count -->
  <counter v-on:count="handleCount"></counter>
  ```
  
- $on(绑定事件)

  ```js
  new Vue({
      el: '#app',
      data: {
          clicks: 0
      },
      mounted() {
          // $refs指代所有被ref注册的组件, $on绑定自定义事件
          this.$refs.qwer.$on('count', this.handleCount);
      },
      methods: {
          handleCount(clicks) {
              this.clicks = clicks;
          }
      }
  })
  ```

  ```html
  <!-- ref属性是将该组件注册引用信息 -->
  <counter ref="qwer"></counter>
  <counter v-on:count="handleCount"></counter>
  ```

- $once事件执行一次, $off 解除事件绑定

#### 混入

混入对象几乎可以引用全部Vue组件可以引用的东西

混入的对象与组件如果用重复的生命周期钩子函数, 会将钩子函数全部执行

若有重复的方法,计算属性,非生命周期钩子函数等, 组件内部的属性会覆盖混入的属性

```js
// 混入的代码
const userMixin = {
  methods: {
    getUserInfomation() {
      // 发送请求
        return ...
    }
  }
}
```

```js
import userMinix from '路径'
Vue.component("userAdmin", {
    // 加入混入代码
	mixins: [userMinix],
    template: `<div>{{ user.username }}</div>`,
    props: {
        userId: {
            type: Number
        }
    },
    data(){
        return {
            user: undefined
        }
    },
    mounted(){
        // 调用混入的函数, 将返回结果复制给组件数据
        this.getUserInfomation().then((user) => {
            this.user = user
        });
    }
})
```

#### 非Prop属性

引用组件时,组件绑定的属性非组件内部声明的prop, 属性会绑定到组件的根元素上

如果根元素的属性与组件绑定的属性重复, 组件绑定的属性会覆盖根元素的属性(class, style 除外)

class与style的值会合并, 如果style里的属性值有重复, 根元素的会被覆盖

总结: 优先使用组件绑定的属性, class与style的属性值会合并

#### 懒加载

做单页面项目, webpack打包生成静态文件会很大, 就需要让,每个组件在使用时再加载, 优化初始化进入时的性能

```
# 懒加载引入组件
component: resolve => require(['组件路径], resolve)
# 或者
const 组件名 = ()=>import("组件路径")
component: 组件名
```

# CSS

#### scoped

组件(.vue文件)的css样式会影响全局, 在组件的<style>标签中加入scoped,变成<style scoped>, 会在标签上自动加入一串随机码, css选择器也会自动匹配该随机码

#### moudle

替代scoped, 在<style>标签中,加入module, 如<style module>, 选择器会被随机名称替换

```html
<div :class="$style.realName"><div>
```

```css
# realName会被随机替换, 通过$style.realName引用
<style module>
	.realName: {
		xxx: xxx
	}
</style>
```

# SCSS

#### 安装

```shell
npm install sass-loader --save-dev
npm install node-sass --save-dev
```

#### 添加配置

```js
~ build/webpack.base.conf.js
在rules下添加
{
    test: /\.scss$/,
    loaders: ['style', 'css', 'sass']
}
```

#### 添加属性

```html
在style标签上添加 lang="scss"
<sytle scoped lang="scss"></style>
```

# Render

#### 使用

动态创建html代码并渲染, 比组件更灵活

```js
new Vue({
    el: '#app',
    data: {
        tagName: "h1"
    },
    // 传递createElement函数,该函数名称随便, 用于创建标签
    render(createElement) {
        return createElement(this.tagName, "Hello World")
    }
})
```

#### 数据对象

数据对象为第二个参数, 可以动态渲染组件

```js
new Vue({
    el: '#app',
    render(createElement) {
        // 引入组件后, 通过render渲染组件
        return createElement("custom-button", {
            // 为组件绑定属性
            attrs: {
                type: 'submit'
            },
            // 为组将绑定自定义属性
            props: {
                text: this.buttonText
            },
            // 绑定html属性
            domProps: {
            	innerHtml: 'html'
        	},
            // 绑定事件
            on: {
                click: this.methodName
            },
            // 使用插槽
            slot: '',
            // 循环的key
            key: '',
            // 注册引用信息
            ref: ''.
            // 类
            class: [],
            // css
            style: {}
        })
    }
})
```

#### 子元素

子元素为第三个参数, 没有传递数据对象时,可作为第二个参数传入

子元素可传递一个可使用创建元素函数的数组, 嵌套生成HTML

只传字符串,会被当作元素中的内容展示

```js
new Vue({
        el: '#app',
        data(){
          return {
            count: 0
          }
        },
        render(createElement) {
          return createElement('div', [
            createElement(
              'button',
              {
                on: {
                  click: () => this.count++
                }
              },
              "单击增加计数"
            ),
            createElement(
              'p',
              `单击了${this.count}次`
            )
          ])
        }
    })
```

#### JSX

一个好用的插件,在使用Render渲染html时,可以直接写html代码,代替上面的复杂代码

- 安装

  ```
  npm install @vue/babel-preset-jsx @vue/babel-helper-vue-jsx-merge-props
  ```

- 使用

  ```js
  render() {
      return (
          <div>
          	<button onClick={this.clickHandler}>点击</button>
  			<p>点击了{count}</p>
  		</div>
  	)
  }
  ```

# Ajax

vue实现ajax使用axios插件

安装

```
yarn add axios
```

引用

```js
~ main.js  全局注册
improt axios from 'axios'  //导入
Vue.prototype.$ajax=axios  //$ajax为变量名
```

使用

```js
this.$ajax.get(url).then(function (res){})
```


# 生命周期

- #### beforeCreate 

  在实例初始化时触发, 此时是一个空的实例, 此时data, methods等都无法调用

- #### created

  实例初始化之后，此时data, methods等都可以调用, 被添加(挂载mounted)到DOM前触发

  这个阶段可以发送请求, 获取数据, 存入data

- #### beforeMount

  将组件, vue的相关指令和数据进行编译后, 准备好添加到DOM，还没添加时触发

- #### mounted

  挂载到页面之后触发，此时有可能还没有被添加到DOM上，可以使用

  ```js
  // 确保DOM完成更新
  this.$nextTick(() => {})
  ```

- #### beforeUpdate

  数据更新要对DOM更改前触发, 页面上显示的数据还是旧的数据

- #### updated

  DOM更改完毕后触发, 页面上显示的数据是新的数据

- #### beforeDestroy

  在组件即将被销毁并且从DOM上删除时触发

- #### destroyed

  组件被销毁后触发

# 过渡和动画

让css样式变化较平缓，不那么生硬

```html
<transition name="name">
	<有样式变化的标签></有样式变化的标签>
</transition>
```

```css
.name-enter-active, .name-leave-active {
    transition: opacity 0.5s;
}
.name-enter, .name-leave-to {
    opacity: 0;
}
```

# Mock.js

#### 安装

```
npm install mockjs -save-dev
```

#### 编写

在src下创建mock文件夹, 创建mock.js文件

```js
import Mock from 'mockjs'

Mock.mock('http://192.168.1.109:8080/user', {
  'name': '@name',  // 随机生成姓名
  'email': '@email', // 随机生成邮箱
  'age|1-10':5,  // 年龄在1~10岁
})

```

#### 使用

```js
// 导入创建的mock.js
import mock from '@/mock/mock.js'

// 调用mock接口
axios.get('http://192.168.1.109:8080/menu').then(res => {
      alert(JSON.stringify(res.data))
})
```

# js-cookie

#### 安装

```shell
cnpm install js-cookie --save
```

# 图标库

#### 安装

```
cnpm install font-awesome
yarn add font-awesome
```

#### 引用

```js
~ main.js
import 'font-awesome/css/font-awesome.min.css'
```

# 多语言国际化

#### 安装

```
cnpm install vue-i18n
yarn add vue-i18n
```

#### 配置

```js
~ @/il8n/index.js
import Vue from 'vue'
import VueI18n from 'vue-i18n'

Vue.use(VueI18n)

// 注册i18h实例并引入语言文件
const i18n = new VueI18n({
  locale: 'zh_cn',
  messages: {
    'zh_cn': require('@/assets/languages/zh_cn.json'),
    'en_us': require('@/assets/languages/en_us.json'),
  }
})

export default i18n

```

#### 语言配置文件

```json
~ @/assets/languages/zh_cn.json
{
  "common": {
    "home": "首页",
    "login": "登录"
  },
  "action": {
    "operation": "操作",
    "add": "新增"
  }
}

~ @/assets/languages/en_us.json
{
  "common": {
    "home": "Home",
    "login": "Login"
  },
  "action": {
    "operation": "Operation",
    "add": "Add"
  }
}
```

#### 引用

```js
~ main.js
import i18n from "./i18n";

new Vue({
  el: '#app',
  i18n, // 新加
  router,
  components: { App },
  template: '<App/>'
})
```

#### 使用

```js
// $t('common.doc') 会读取语言配置文件对应的值
<h3>{{$t('common.doc')}}</h3>
<el-button type="success" @click="changeLanguage('zh_cn')">简体中文</el-button>
<el-button type="success" @click="changeLanguage('en_us')">English</el-button>

changeLanguage(lang) {
    lang === '' ? 'zh_cn' : lang;
    this.$i18n.locale = lang;
    this.langVisible = false
}
```



# UI框架

## iview

#### 安装

```
cnpm install iview --save
```

#### 引用

```
~ main.js
import iView from 'iview'
import 'iview/dist/styles/iview.css'
Vue.use(iView)
```

#### 安装babel-plugin-import

该插件会按需加载iview框架中的组件, 不需要全部引入, 节省资源

```
# ***** 注: 当使用该插件时, 在main.js中不能使用import引入和use使用, 会与该插件冲突,导致找不到iview *****
npm install babel-plugin-import --save-dev
# 在.babelrc配置
["import", {
    "libraryName": "iview",
    "libraryDirectory": "src/components"
  }]
```

## ElementUI

#### 安装

```
npm i element-ui -S
```

