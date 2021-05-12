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

## new的内部执行过程

- 在内存中开辟一块空间
- 创建一个空对象
- this指向这个空对象    this = {}
- 把空对象的内部原型指向构造函数的原型对象    this.\_proto_ = Person.prototype
- 构造函数执行, 给this添加属性和方法
- 最后把当前对象返回

## 创建对象

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

## 原型对象

```js
// Person是一个类, 动态的往类中添加一个方法
// 已经创建的实例  new Person(), 可以调用这个方法
Person.prototype.run = function(){}
```

## 读取变量的值作为名称

```js
let a = "name"
// 将变量的值作为key
let obj = {
    [a] : "老王"    // name: "老王"
}
// [a]的原理, 上面是下方的简写
let a = "name"
let obj = {
}
obj[a] = "老王"
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

# Promise

异步操作, 解决回调地狱的问题

## 例

- 未使用Promise


```js
// setTimeout为异步操作, 不使用Promise, 在嵌套多层时, 会出现回调地狱
setTimeout(() => {
    console.log("hello js")
    console.log("hello js")
    console.log("hello js")
    
    setTimeout(() => {
        console.log("hello vue")
        console.log("hello vue")
        console.log("hello vue")
        
        setTimeout(() => {
            console.log("hello html")
            console.log("hello html")
            console.log("hello html")
        },1000)
    },1000)
},1000)
```

- 使用Promise

  链式调用,  catch只能有一个, 需要放到最后

  **方式一**

```js
// resolve 成功   reject 失败
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
        // reject()
    },1000)
}).then(() => {
    console.log("hello js")
    console.log("hello js")
    console.log("hello js")
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve()
        },1000)
    })
}).then(() => {
    console.log("hello vue")
    console.log("hello vue")
    console.log("hello vue")
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve()
        },1000)
    })
}).then(() => {
    console.log("hello html")
    console.log("hello html")
    console.log("hello html")
}).cathc(err => {
    console.log("err")
})
```

- **方式二**

  then()和catch()合并

```js
// resolve 成功   reject 失败
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
        // reject()
    },1000)
}).then(() => {
    console.log("hello js")
    console.log("hello js")
    console.log("hello js")
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve()
        },1000)
    })
}, (err) => {
    
})
```

## 简写

- 未简写

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("hello")
    },1000)
}).then((data) => {
    console.log(data)
    // 未简写
    return new Promise((resolve, reject) => {
        resolve(data + "老王")
    })
}).then((data) => {
    console.log(data)
    // 未简写
    return new Promise((resolve, reject) => {
        reject()
    })
}).then((data) => {
    console.log(data)
}).catch(() => {
    console.log("err")
})
```

- 简写

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("hello")
    },1000)
}).then((data) => {
    console.log(data)
    // 简写
    return Promise.resolve(data + "老王")
}).then((data) => {
    console.log(data)
    // 简写
    return Promise.reject(data + ", 吃了么")
}).then((data) => {
    console.log(data)
}).catch(() => {
    console.log("err")
})
```

- 再简写

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("hello")
    },1000)
}).then((data) => {
    console.log(data)
    // 再简写
    return data + "老王"
}).then((data) => {
    console.log(data)
    // 再简写	
    throw "err msg"
}).then((data) => {
    console.log(data)
}).catch(() => {
    console.log("err")
})
```

## 等待全部任务完成

```js
// Promise.all()接收一个Promise数组
Promise.all([new Promise(((resolve, reject) => {
    setTimeout(() => {
        resolve("1")
    }, 5000)
})), new Promise(((resolve, reject) => {
    setTimeout(() => {
        resolve("2")
    }, 7000)
})), new Promise(((resolve, reject) => {
    setTimeout(() => {
        resolve("3")
    }, 10000)
}))]).then((results) => {
	// results是上面每一个Promise数组的结果
    console.log(results);
}).catch((err) => {
    console.log(err)
})
```

# 函数

## 改变this指向

### bind

bind函数会使用旧函数生成一个新函数, 并且改变新新函数内部this的指向

```js
function fn(a, b, c) {
    console.log(this)
    console.log("a", a);
    console.log("b", b);
    console.log("c", c);
    return a + b + c;
}
// bind函数的第一个参数会作为新函数的this, 剩余的函数会作为旧函数的参数一次传递
var _fn = fn.bind("这里是新this", 10);
var ans = _fn(20, 30); // 60
```

### call  /  apply

call和apply的作用相同, 改变this指向, 只是传参方式不同

#### 和bind的区别

bind会生成一个新函数, call  和 apply不会生成新函数, 并且会立即执行函数

#### 样例一

```js
function add(a, b) {
    console.log(this)
    return a + b;
}
// add方法里的this被替换为 字符串 "这里是新this"
// apply 函数接收的参数为数组
console.log(add.apply("这里是新this", [2, 5]));  // 7
// call 函数接收的参数需要分开传
console.log(add.call("这里是新this", 2, 5));  // 7
```

#### 样例二

```js
function demo1() {
    let t = new this();
    return t.t
}

function demo2() {
    this.t = 10
}
// demo1函数的this被替换为函数demo2
console.log(demo1.apply(demo2))  // 10
```

#### 样例三

```js
function People(name, age) {
    this.name = name;
    this.age = age;
}

function Student(name, age, grade) {
    People.call(this, name, age);
    this.grade = grade;
}

// 等价为
function People(t, name, age) {
    t.name = name;
    t.age = age;
}

function Student(name, age, grade) {
    People(this, name, age);
    this.grade = grade;
}

var student = new Student('小明', 21, '大三');
console.log(student.name + student.age + student.grade); //小明21大三
```

## 高阶函数

### 概念

- 参数接收一个函数的函数   例: promise,  map, filter
- 返回值返回一个函数的函数   例: 闭包

### 函数柯里化

函数返回函数, 并且每个函数都接收参数, 最内部的函数对这些参数统一做处理

```js
function a(a){
    return function(b){
        return function(c){
            return a + b + c;
        }
    }
}
a(1)(2)(3) // 6
```



# 拷贝

## 深拷贝

```js
let p1 = {
    name: "老王"
}
// 使用展开运算符, 可以深拷贝一个对象
let p2 = {...p1}
// 复制的同时, 可以添加或者修改
let p2 = {...p1, age: 12}
```

