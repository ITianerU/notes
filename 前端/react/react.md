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

