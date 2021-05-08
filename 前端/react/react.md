# 简述

一个使用虚拟dom渲染数据的js框架

# 安装

## cdn

### babel

使用babel编译jsx

```html
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
```

### react

react核心库

```html
<script src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
```

### react-dom

react虚拟dom库

```html
<script src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

# 基础用法

## 虚拟dom

### jsx方式

```html
<!-- type="text/babel" 指定下面的jsx语法使用babel翻译 -->
<script type="text/babel">
	// 创建虚拟dom
	const VDOM = <h1>Hello, React</h1>
	// 复杂的标签可以用小括号
	const VDOM = (
		<h1>
    		<span>Hello, React</span>
    	</h1>
	)
	// 渲染到视图中
	ReactDOM.render(VDOM, document.getElementById('app'))
</script>
```

### js方式(不推荐)

js方式不需要使用babel, 这种方式嵌套太多

```html
<script>
	// 创建虚拟dom, 参数: 标签名, 属性, 内容, 内容可以嵌套标签
	const VDOM = React.createElement('h1', {id: 'title'}, 'hello react')
    // 嵌套标签写法
	const VDOM = React.createElement('h1', {id: 'title'}, 
		React.createElement("span", {}, 'hello react')
	)
	ReactDOM.render(VDOM, document.getElementById("app"))
</script>
```

## JSX语法

全称JavaScript XML

### 定义虚拟DOM

```jsx
// 不需要加引号
// 根标签只能有一个
const VDOM = (
    <h1>
        <span>Hello, React</span>
    </h1>
    // <h2></h2>  根标签只能有一个
)
```

### 使用变量 / 表达式

```jsx
let msg = "hello"
let list = ["数学", "语文", "英语"]
// 使用花括号, 引用变量或者表达式
const VDOM = (
    <h1>
        <span>{msg}</span>
        <ul>
            {   
                // 使用表达式
                list.map((item) => {
                    // 指定唯一值key
                    return <li key="{item}">{item}</li>   // 表达式里使用变量, 也需要花括号
                })
            }
        </ul>
    </h1>
)
```

### 使用class

```css
/* 定义一个类样式 */
.class1{
    color: red
}
```

```jsx
// 使用className指定类
const VDOM = (
    <h1 className="class1">
        <span>{msg}</span>
    </h1>
)
```

### 内联样式

```jsx
// 使用行内样式, 要使用双层花括号
// 带有横线的样式, 需要写成驼峰命名, 例: font-size  -> fontSize
const VDOM = (
		<h1 style={{color: "yellow", fontSize: '50px'}}>
			<span>{msg}</span>
		</h1>
	)
```

# 高级用法

## 组件

### 定义组件

#### 函数式组件

适用于简单组件

```jsx
// 定义组件, 需要首字母大写
function Demo(){
 return <h1>组件</h1>
}
// React解析组件标签, 找到组件
// 发现组件是函数组件, 调用该函数
// 将方法返回的虚拟DOM转换为真实DOM, 渲染到页面上
ReactDOM.render(<Demo/>, document.getElementById("app"))
```

#### 类式组件

适用于复杂组件

```jsx
// 必须继承 React.Component
class Demo extends React.Component{
 // 必须有render函数
 render(){
  return (
   <h1>hello</h1>
  )
 }
}
// React解析组件标签, 找到组件
// 发现组件是类组件, 实例化类, 调用类的render函数
// 将类返回的虚拟DOM转换为真实DOM, 渲染到页面上
ReactDOM.render(<Demo/>, document.getElementById("app"))
```

### 组件实例三大核心属性

#### state(状态)

有状态的是复杂组件, 没有状态的是简单组件

```jsx
class Weather extends React.Component{
    constructor(props) {
        super(props);
        // 初始化数据, 类似vue的data
        this.state = {
            isHot: false
        }
    }
    render(){
        return (
            <h1>今天天气很{this.state.isHot ? "炎热" : "寒冷"}</h1>
        )
    }
}
```

##### 状态修改

```jsx
// 不能直接修改, 需要使用api
this.setState({
    isHot: !this.state.isHot
})
```



### 事件

#### 方式一(推荐)

```jsx
class Weather extends React.Component{
    constructor(props) {
        super(props);
        // 定义一个属性, 该属性指向一个方法
        // this.click 会先在属性中找, 找不到去原型对象上找
        // 在原型对象上找到click方法之后, 将this绑定到click方法, 生成新的方法
        // 将新的方法, 赋值给this.click属性
        this.click = this.click.bind(this)
    }
    // 定义一个方法, 该方法是绑定在Weather原型对象上
    click(){
        alert("被点击了")
    }
    render(){
        return (
            // 绑定事件, 绑定的是click属性
            <h1 onClick={this.click}>今天天气很炎热</h1>
        )
    }
}
```

#### 方式二(不推荐)

```jsx
class Weather extends React.Component{
    render(){
        return (
            <h1 id="weather">今天天气很炎热</h1>
        )
    }
}
ReactDOM.render(<Weather/>, document.getElementById("app"))
// 渲染后, 获取要绑定的节点的id, 绑定事件
document.getElementById("weather").addEventListener("click", () => {
    alert("被点击了")
});