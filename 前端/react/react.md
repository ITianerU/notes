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

### 注释

```jsx
render(){
    // 使用花括号注释
    return (
        {/* <h1>hello</h1> */}
    )
 }
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

#### 卸载组件

```jsx
// 卸载某一节点下的组件
ReactDOM.unmountComponentAtNode(document.getElementById("app"))
```

### 组件实例三大核心属性

#### state(状态)

有状态的是复杂组件, 没有状态的是简单组件

##### 复杂写法

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

##### 简洁写法(推荐)

```jsx
class Weather extends React.Component{
    // 不使用构造方法
    state = {
        isHot: false
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

#### props(属性)

组件接收的参数, 为只读, 不能修改

##### 基础用法

```jsx
class Person extends React.Component{
    render(){
        // 使用解构赋值
        cosnt {name, age} = this.props
        return (
            <ul>
                <!-- 使用属性 -->
                <li>姓名: {name}</li>
                <li>年龄: {age}</li>
            </ul>
        )
    }
}
// 传递属性, 会存到this.props中
// 传递数字, 需要加{}
ReactDOM.render(<Person name="老王" age={25}/>, document.getElementById("app"))
```

##### 高级用法

```jsx
const p = {name: "老王", age:50}
// 原生的js不支持展开运算符展开对象
// 此处是react 和babel 对...进行了重写, 可以展开对象
// 仅能用于标签属性传递
ReactDOM.render(<Person {...p} />, document.getElementById("app"))
```

##### 校验

```jsx
class Person extends React.Component{
    // 想要给类加属性, 需要使用静态属性
    static propTypes = {
        // 指定数据类型, 是否必填
        // 版本16不再使用, React.PropTypes, 改为PropTypes
        name: PropTypes.string.isRequired
        // 数据类型:
        // string, number, func
    }
}
// 也可以写在类外部, 不推荐
Person.propTypes = {
    name: PropTypes.string.isRequired
}
```

##### 默认值

```jsx
class Person extends React.Component{
    // 设置属性的默认值
    static defaultProps = {
        name: "老王"
    } 
}
// 也可以写在类外部, 不推荐
Person.defaultProps = {
    name: "老王"
}
```

##### 构造函数接收props

```jsx
// 很少使用
class Person extends React.Component{
    constructor(props) {
        super();
        console.log(props);
    }
}
```

##### 函数式组件使用props

```jsx
// 三大属性, 函数式组件只能使用props
function Person(props){
    return (
        <ul>
            <li>姓名: {props.name}</li>
            <li>年龄: {props.age}</li>
        </ul>
    )
}
// 如何想做约束, 和设置默认值, 只能在外部定义
Person.propTypes = {
    name: PropTypes.string.isRequired
}
Person.defaultProps = {
    name: "老王"
}
```

#### refs

用于快速获取html节点

##### 字符串形式(不推荐)

```jsx
class Demo extends React.Component{
    render(){
        return (
            <div>
                <!-- 绑定ref属性, 此处使用的是字符串, 已经不推荐使用, 存在效率问题 -->
                <input ref="c" type="text"/>
                <button onClick={this.click}>点击提示</button>
                <input ref="b" type="text" onBlur={this.blur}/>
            </div>
        )
    }
    click = () => {
        // 获取数据
        console.log(this.refs.c.value)
    }
    blur = () => {
        console.log(this.refs.b.value);
    }
}
```

##### 回调函数形式

###### 内联函数

内联函数会在第一次渲染dom时, 调用一次

在每次dom更新时, 会调用两次, 

没有太大的影响, 可以继续使用

```jsx
class Person extends React.Component{
    render(){
        return (
            <div>
                <!-- 
					ref绑定一个回调函数, 在回调函数中, 给实例对象的一个属性绑定当前节点
 					currentNode会传入当前节点
				-->
                <input ref={currentNode => this.input1 = currentNode} type="text" />
                <button onClick={this.click}>按钮</button>
            </div>
        )
    }
    click = () => {
		// 从属性中获取
    	alert(this.input1.value)
    }
}
```

###### class绑定函数

此方法, 解决内联函数的问题, 只会在第一次渲染时, 调用一次

dom更新时, 不会再次调用

```jsx
class Person extends React.Component{
    render(){
        return (
            <div>
                <!-- 绑定外部函数 -->
                <input ref={this.test} type="text" />
                <button onClick={this.click}>{this.state.isHot}</button>
            </div>

        )
    }
    test = (c) => {
        this.input1 = c;
        console.log(c)
    }
    click = () => {
		// 从属性中获取
    	alert(this.input1.value)
    }
}
```

###### createRef

官方推荐的形式

```jsx
class Demo extends React.Component{
		// React.createRef返回一个容器, 该容器可以存储被ref所标识的节点, 一个容器只能存一个节点
		myRef = React.createRef()
		render(){
			return (
				<div>
					<input ref={this.myRef} type="text"/>
					<button onClick={this.click}>点击提示</button>
				</div>
			)
		}
		click = () => {
			// 取值
			console.log(this.myRef.current.value)
		}
	}
```



### 事件

#### 方式一(推荐)

##### 复杂写法

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

##### 简洁写法(推荐)

```jsx
class Weather extends React.Component{
    // 使用箭头函数, 箭头函数没有自己的this, 会去寻找函数外部的this
    click = () => {
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
```

#### 事件对象

```jsx
class Demo extends React.Component{
    render(){
        return (
            <div>
                <!-- 绑定事件 -->
                <input onBlur={this.blur} type="text"/>
            </div>
        )
    }
    // 会自动往第一个参数传递事件对象
    blur = (event) => {
        // 取值
        console.log(event.target.value)
    }
}
```

### 受控 / 非受控组件

#### 非受控组件

```jsx
class Demo extends React.Component{
		render(){
			return (
				<form onSubmit={this.submit}>
					用户名<input ref={c => this.username = c} type="text" name="username"/>
					密码<input  ref={c => this.password = c} type="text" name="password"/>
					<button>登录</button>
				</form>
			)
		}
		submit = (event) => {
			// 阻止表单提交的默认行为
			event.preventDefault()
			// 取值
			console.log(this.username.value)
		}
	}
```

#### 受控组件

```js
class Demo extends React.Component{
    // 受控组件页面中的输入dom(input), 与state中的值绑定
    state = {
        username: "",
        password: ""
    }
    changeUsername = event => {
        this.setState({
            username: event.target.value
        })
    }
    changePassword = event => {
        this.setState({
            password: event.target.value
        })
    }
    submit = (event) => {
        // 取值
        console.log(this.state)
    }
    render(){
        return (
            <form onSubmit={this.submit}>
            用户名<input onChange={this.changeUsername} type="text" name="username"/>
                密码<input  onChange={this.changePassword} type="text" name="password"/>
                    <button>登录</button>
    		</form>
    )
}
	}
```

##### 简化

###### 函数柯里化写法

```jsx
class Demo extends React.Component{
    state = {
        username: "",
        password: ""
    }
	// 使用闭包返回一个函数
	// 函数返回函数, 并且每个函数都接收参数, 最内部的函数对这些参数统一做处理, 叫做函数柯里化
    changeFormDate = (name) => {
        return (event) => {
            this.setState({
                [name]: event.target.value
            })
        }
    }
    submit = (event) => {
        // 取值
        console.log(this.state)
    }
    render(){
        return (
            <form onSubmit={this.submit}>
                <!-- 绑定相同的函数 -->
                用户名<input onChange={this.changeFormDate("username")} type="text" name="username"/>
                密码<input  onChange={this.changeFormDate("password")} type="text" name="password"/>
                <button>登录</button>
            </form>
        )
    }
}
```

###### 非函数柯里化写法

```jsx
class Demo extends React.Component{
		state = {
			username: "",
			password: ""
		}
		changeFormDate = (name, event) => {
				this.setState({
					[name]: event.target.value
				})
		}
		submit = (event) => {
			// 取值
			console.log(this.state)
		}
		render(){
			return (
				<form onSubmit={this.submit}>
					用户名<input onChange={(event) => {this.changeFormDate("username", event)}} type="text" name="username"/>
					密码<input  onChange={(event) => {this.changeFormDate("password", event)}} type="text" name="password"/>
					<button>登录</button>
				</form>
			)
		}
	}
```

## 生命周期

### 旧版

```jsx
class Demo extends React.Component{
    // 创建实例时, 仅调用一次
    constructor() {super();}
    // 组件挂载前, 仅调用一次
    componentWillMount(){}
    // 初始化渲染, 或者状态更新, 会被调用多次
    render(){}
    // 组件完成挂载, 仅调用一次
	componentDidMount(){}
    // 组件卸载前, 仅调用一次
    componentWillUnmount(){}
}

```

