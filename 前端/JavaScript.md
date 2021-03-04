# 基础用法

## 块级作用域

### var

var没有块级作用域, 在代码块外也可以使用

```js
{
   var name = 1 
}
console.log(name)  // 在代码块外也可以访问
```

#### 需要使用闭包解决问题

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

### let

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

