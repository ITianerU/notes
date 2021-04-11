# 块级作用域

## var

var没有块级作用域, 在代码块外也可以使用

```js
{
   var name = 1 
}
console.log(name)  // 在代码块外也可以访问
```

### 需要使用闭包解决问题

```js
// 给每个按钮绑定点击事件, 每个按钮点击, 在控制台打印其位置
var btns = doucument.getElementsByTagName('button');
for(var i=0; i<btns.length; i++){
    // 如果不使用闭包, 直接console.log(i), i在for循环最后, 会变成最后的值
    // btns[i].addEventListener('click', function(){
    //     console.log(i);
    // })
    // 使用闭包, 函数有作用域, 将i作为参数传入, 闭包, console.log打印的是闭包的参数
    (function (num){
        btns[i].addEventListener('click', function(){
            console.log(num);
        })
    })(i)
}
```

## let

let有作用域

```js
var btns = doucument.getElementsByTagName('button');
// 使用let 可以直接打印
for(let i=0; i<btns.length; i++){
    btns[i].addEventListener('click', function(){
         console.log(i);
    })
}
```

# 对象

new的内部执行过程

- 在内存中开辟一块空间
- 创建一个空对象
- this指向这个空对象    this = {}
- 把空对象的内部原型指向构造函数的原型对象    this.\_proto_ = Person.prototype
- 构造函数执行, 给this添加属性和方法
- 最后把当前对象返回

创建对象

```js
// 创建一个构造方法
function Person(name){
    this.name = name
    this.say(){
        console.log("my name is " + this.name);
    }
}
let jack = new Person("jack")
jack.say()
```

原型对象

```js
// Person是一个类, 动态的往类中添加一个方法
// 已经创建的实例  new Person(), 可以调用这个方法
Person.prototype.run = function(){}
```

# this

```js
function sum(){
    // this, 函数内部的this指代的是window对象
    console.log(this)
}
() => {
    // 匿名函数内部没有this, 会向上级找, 直到找到this, 这里的this也找到的是window对象
    console.log(this)
}

function Person() {
    this.name = "老王"
    function sum(){
        // 构造方法内部中的this, 都指向构造方法创建时的实例
        console.log(this)
    }
    this.fun = () => {
        // 构造方法内部中的this, 都指向构造方法创建时的实例
        console.log(this)
    }
}

let obj = {
    aaa() {
        setTimeout(function sum(){
            // this, 函数内部的this指代的是window对象
            console.log(this)
        }, 100)
        setTimeout(() =>{
            // 匿名函数内部没有this, 会向上级找, 这里向上一级找到的是obj对象
            console.log(this)
        }, 100)
    }
}
```

# 页面跨域传值

```js
// 获取跨域页面窗口的window对象
let win = document.getElementById("iframe").contentWindow
// 给指定域传值
win.postMessage("value", "跨域的窗口的域名")
```

# 原型

## 添加属性

```js
function Obj(){}
Object.defineProperty(Obj, 属性名, 属性描述{
                      value: 123,
                      writable: true|false  // 属性的值是否可以被重写
                      enumerable: true|false  // 属性的值是否可以被枚举
                      configurable: true|false  // 是否可以删除, 或这修改属性的描述
                      })
obj.property.属性名 = 值
```

