# 引入

- CDN

  开发版: 

  ```html
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  ```

  生产版:

  ```html
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.12"></script>
  ```

- NPM

  需要先安装nodejs

  ```shell
  npm install vue   # vue
  npm install -g @vue/cli  # vue脚手架
  # 或者cnpm安装
  ```

  创建vue项目

  ```shell
  vue create hello-world
  ```

  

## 基本样例

```html
<div id="app"></div>
<script>
	const app = new Vue({
        el: "#app",
        data: {}
    })
</script>
```

# 基础用法

- v-once  

  拒绝响应式,  页面显示的数据在初始化之后, 无法再修改

  ```html
  <span v-once>{{msg}}</span>
  ```

- v-html

  插入html代码

  ```html
  <div id="app">
  	<div v-html="btn"></div>
  </div>
  <script>
  	const app = new Vue({
          el: "#app",
          data: {
              btn: "<button>按钮</button>"
          }
      })
  </script>
  ```

- v-text  和 {{}}

  显示数据, v-text不常用, 不灵活, 会覆盖掉已有的内容

  ```html
  <!-- xxx会被覆盖 -->
  <div v-text="msg">xxx</div>
  <!-- 会和xxx拼接 -->
  <div>{{msg}}xxx</div>
  ```

- v-pre

  不对{{}}进行解析, 页面直接显示{{}}

- v-cloak

  在页面全部的数据加载完成,  再显示出来, 不加此指令, 页面上{{}}会一闪而过

  需要再style中添加

  ```css
  [v-cloak]{
      display: none;
  }
  ```

  页面数据渲染完成后. vue会自动删除掉v-clock属性, 对应的css就不会生效

- v-if  v-else-if  v-else

  满足条件, 再生成

  ```html
  <!-- isShow在切换true/false时, 显示对应的输入框, 输入框中的数据不会被清除, 可以使用key属性防止这个情况 -->
  <!-- vue在渲染标签的时候, 会重复利用已有的标签, 替换其中的属性, 减少性能开销 -->
  <span v-if="isShow">
      <input  type="text" id="phone" /> 手机号
      <!-- <input  type="text" id="phone" key="phone" /> -->
  </span>
  <span v-else>
      <input  type="text" id="email" /> 邮箱
      <!-- <input  type="text" id="phone" key="email" /> -->
  </span>
  <script>
  	const app = new Vue({
          el: "#app",
          data: {
              isShow: true
          }
      })
  </script>
  ```

- v-show

  先生成, 满足条件再显示, 只是修改其display属性, none/block

- v-for

  遍历, 数组和对象都可遍历

  ```html
  <ul>
      <!-- 
  		:key提高diff算法性能, :key绑定的值需要有唯一性, 并且与实际的数据要对应, 不能使用索引
  		这样在往数组中间新插入一条数据时, 不会重新渲染, 已经有的部分, 数组后插入数据无影响
  	-->
      <li v-for="(item, index) in books" :key="item">{{index}} -- {{item}}</li>
      <li v-for="(value, key, index) in person" :key="key">{{index}} -- {{key}} -- {{value}}</li>
  </ul>
  <script>
  	const app = new Vue({
          el: "#app",
          data: {
              books: ['a', 'b', 'c'],
              person: {
                  name: "花花",
                  age: 19
              }
          }
      })
  </script>
  ```

- v-bind 可缩写成 :

  ```html
  <img src="xxx" v-bind:height="100" v-bind:width="100">
  <!-- 或 -->
<img src="xxx" :height="100" :width="100">
  ```
  
- v-on 可缩写成 @

  绑定事件

  ```html
  <button v-on:click="console.log(1)"></button>
  <!-- 或 -->
  <button @click="console.log(1)"></button>
  <!-- 不加括号, 默认会将浏览器事件对象作为参数传入 -->
  <button @click="btnClick"></button>
  <!-- 加括号, 不会默认传递参数 -->
  <button @click="btnClick()"></button>
  <!-- 主动传递事件对象 -->
  <button @click="btnClick($event)"></button>
  ```

  修饰符

  ```html
  <!-- 阻止事件冒泡 -->
  <button @click.stop="console.log(1)"></button>
  <!-- 阻止默认事件 -->
  <input type="submit" @click.prevent="console.log(1)" />
  <!-- 监听键盘按键点击 可以写按键别名, 或者按键对应的code-->
  <input type="text" @keyup.enter="console.log(1)" />
  <input type="text" @keyup.13="console.log(1)" />
  <!-- 只触发一次 -->
  <button @click.once="console.log(1)"></button>
  ```
  
- v-model

  双向绑定

  ```html
  <input type="text" v-model="msg" />
  <!-- 等同于 -->
  <input type="text" :value="msg" @input="msg = $event.target.value"/>
  <script>
  	const app = new Vue({
          el: "#app",
          data: {
              msg: ""
          }
      })
  </script>
  ```

  修饰符

  ```html
  <!-- lazy修饰符, 用户在输入完后, 再同步数据, 取消实时同步 -->
  <input type="text" v-model.lazy="msg" />
  <!-- number修饰符, 仅允许输入数字 -->
  <input type="text" v-model.number="msg" />
  <!-- trim修饰符, 删除左右的空格 -->
  <input type="text" v-model.trim="msg" />
  ```

  

# 计算属性(computed)

将数据处理后再展示, 相对于使用方法处理数据, computed只会计算一次, 生成缓存, 多次使用不会重复计算造成性能浪费, 当数据有修改时, 计算属性才会再次计算

```html
<div id="app">{{realPrice}}</div>
<script>
	const app = new Vue({
        el: "#app",
        data: {
            price: 100,
        },
        computed: {
            // 建议名称不加动词, 不是一个方法, 是由下面的代码简写成
            realPrice: function(){
                return this.price * 0.8;
            }
            /*
            realPrice: {
            	set: function(){},
            	get: function(){
            		return this.price * 0.8;
            	}
            }
            */
        }
    })
</script>
```

# 过滤器(filters)

```html
<div id="app">{{price | showPrice}}</div>
<script>
	const app = new Vue({
        el: "#app",
        data: {
            price: 100,
        },
        filters: {
            showPrice(price){
                return "$" + price;
            } 
        }    
    })
</script>
```

# 组件

## 基础用法

```html
<div id="app">
    <hello></hello>
</div>
<script>
    // 创建组件
    const demo = Vue.extend({
        template: '<div><h1>Hello</h1></div>'
    })
    // 注册组件(全局注册)
    Vue.component("hello", demo);
    // 也可以这么写, 省略掉Vue.extend
    Vue.component("hello", {
        template: '<div><h1>Hello</h1></div>'
    });
	const app = new Vue({
        el: "#app",
        components: {
            hello: demo  // 局部注册
        }
    })
</script>
```

分离写法

```html
<div id="app">
    <hello></hello>
</div>
<!-- 编写组件代码 -->
<template id="demo">
    <div><h1>Hello</h1></div>
</template>
<script>
    // 通过id选择器注册组件
    Vue.component("hello", {
        template: "#demo"
    })
    const app = new Vue({
        el: "#app",
    })
</script>
```

## 父子组件

```html
<div id="app">
    <!-- 动态绑定组件属性 -->
    <hello2 :name="name"></hello2>
</div>
<script>
    // 创建组件1
    const demo1 = Vue.extend({
        template: '<div><h1>Hello</h1></div>'
    })
    // 创建组件2
    const demo2 = Vue.extend({
        template: `<div>
                        <hello></hello>
                        <h1>{{name}}</h1>
                        <h1>{{msg}}</h1>
                    </div>`,
        data(){             // 注: 在组件中data是函数, 因为组件要重复使用, 是用函数返回数据, 保证了每个组件的数据是独立的
            return {
                msg: "xxx"
            }
        },
        props: ["name"],    // 自定义组件属性
        components: {
            hello: demo1    // 将组件1, 注册到组件2中, 组件1是组件2的子组件
        }
    })
    const app = new Vue({
        el: "#app",
        data: {
            name: "jack"
        },
        components: {
            hello2: demo2
        }
    })
</script>
```

定义组件属性

```js
// 支持的类型有 String, Number, Boolean, Array, Object, Date, Function, Symbol
const demo2 = Vue.extend({
    props: {
        name: {
            type: String,
            default: 'jack'  // 默认值
            required: true  // 是否必填
            validator: function(value){ // 自定义校验
        		return true
    		}
        },
        books: Array,    // 可以直接设置类型
    },    
})
```

子组件向父组件传值

```html
<div id="app">
    <!-- 给自定义事件绑定方法 -->
    <demo @demo-event="changeMesssage"></demo>
    <span>{{message}}</span>
</div>
<script>
    const demo = Vue.extend({
        template: `<div>
                        <input type="text" v-model="msg" @input="changeMsg"/>
                    </div>`,
        data(){
            return {
                msg: ""
            }
        },
        props: ["name"],
        methods: {
            changeMsg(){
                this.$emit("demo-event", this.msg)   // 定义自定义事件
            }
        }
    })
    const app = new Vue({
        el: "#app",
        data: {
            message: ""
        },
        components: {
            demo: demo
        },
        methods: {
            changeMesssage(value){
                this.message = value;
            }
        }
    })
</script>
```

父子组件相互访问

父访问子

- $refs

```js
// 使用子组件的方法
this.$refs("子组件的ref属性").method()
// 访问子组件的data中的属性
this.$refs("子组件的ref属性").property
```

- $children 不常用, 会返回子组件列表, 需要索引指定, 常用来遍历

```js
// 使用子组件的方法
this.$children[0].method()
// 访问子组件的data中的属性
this.$children[0].property
```

子访问父(不推荐使用, 会导致耦合度过高, 依赖于父组件搭配使用)

- $parent

```js
this.$parent.method()
this.$parent.property
```

- $root 访问根组件的属性, 方法

## 插槽(slot)

组件预留的位置,  供父组件在使用时, 填入想要展示的内容

```html
<div id="app">
   	<!-- 往插槽中, 填入<span>{{message}}</span> -->
    <demo><span>{{message}}</span></demo>
    <!-- 使用命名插槽 -->
    <demo><button slot="slotName">按钮</button></demo>
</div>
<script>
    const demo = Vue.extend({
        // 组件中, 设置插槽
        template: `<div>
                        <h1>hello</h1>
					  <slot><span>默认值1</span></slot>
					  <h1>world</h1>
					  <slot name="slotName">默认值2</slot>
                    </div>`,
        
    })
    const app = new Vue({
        el: "#app",
        data: {
            message: "jack"
        },
        components: {
            demo: demo
        },
    })
</script>
```



# 监听器(watch)

监听属性的改变

```html
<div id="app">
    <input type="text" v-model="msg1"/> 输入1
    <input type="text" v-model="msg2"/> 输入2
</div>
<script>
    const app = new Vue({
        el: "#app",
        data: {
            msg1: "",
            msg2: ""
        },
        watch: {
            msg1(newValue, oldValue){
                this.msg2 = newValue + "...";
            }
        }
    })
</script>
```

