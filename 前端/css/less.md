# 安装

## node环境

```js
cnpm install -S less
```

## cdn引入

```html
<script src="//cdnjs.cloudflare.com/ajax/libs/less.js/2.5.3/less.min.js"></script>
```

### 使用

```html
<style type="text/less"></style>
```

# 基本语法

## 注释

```less
// 该注释, 不会被编译到css中
/* 该注释会被编译到css中 */  
```

## 变量

### 定义

```less
@color: black;
@background-color: red;
// 定义样式名
@f: font-size;
// 定义选择器名
@i: #id
```

### 使用

```less
@{i}{
    @{f}: 20px;
    color: @color;
    background-color: @background-color
}
```

### 延迟加载/作用域

```less
@var: 0;
.class{
    @var: 1;
    .in{          
        @var: 2;
        three: @var;   // @var为3,  变量是延迟加载的, 在.in里会先全部读取, 再替换@var, 所以是3
        @var:3;
    }
    one: @var;   // @var为1, 变量只会找同级作用域的变量, 所以是1
}
```

## 嵌套规则

```less
#out{
    #in{
        color: red;
        &:hover{          // 嵌套会导致编译后是父子关系, #out #in :hover; 如果不想#in和:hover中间有空格, 要使用&
            color: blue; 
        }
    }
}
```

## 运算

```less
#out{
    windth: (100 + 100px);    // 使用运算符, 可以只要带一个单位
}
```



# 高级用法

## 混合

类似变量, 定义一个css样式集合, 然后使用这个集合减少代码重复

```less
// 定义一个公共样式混合, 可以无参, 也可以带参
//.common(@color:red) 可以设默认值
.common(@color, @size){
    margin: 0;
    color: @color,
}

#wrap{
    .inner1{
        .common(red);   // 使用公共样式
    }
    .inner2{
        .common(blue);
    }
    .inner3{
        .common(@color: blue);  // 指定要传递哪个参数
    }
}
```

### 匹配模式

```less
// 同名的公共混合, 参数为@_, 在编译时会自动执行
.common(@_){
    padding: 0;
    margin: 0;
}
.common(Y, @size){
    font-size: @size;
    color: yello;
}
.common(R, @size){
    font-size: @size;
    color: red;
}

#wrap{
    .inner1{
        .common(Y, 14px);   // 会匹配第一个参数为Y的common样式
    }
}
```

### arguments变量

```less
.common(@size, @type, @color){
    border: @arguments      // @arguments会使用 @size, @type, @color
}

#wrap{
    .inner1{
        .common(14px, solid, red);   
    }
}
```

## 导入

```css
@import "./xxx.less"
```

## 继承

性能比混合高, 灵活性不如混合, 编译后的文件, 体积比混合小

```less
// 继承不能定义参数
.father{
    margin: 0;
    padding: 0;
}
// 使用extend继承父类
.inner:extend(.father){
    height: 100px;
    width: 100px;
}
.inner2:extend(.father){
    height: 200px;
    width: 200px;
}

// 会编译成
.father, .inner, .inner2{
    margin: 0;
    padding: 0;
}
.inner{
    height: 100px;
    width: 100px;
}
.inner2{
    height: 200px;
    width: 200px;
}
```

### 对比混合

```less
.father(){
    margin: 0;
    padding: 0;
}
.inner{
    .father();
    height: 100px;
    width: 100px;
}
.inner2{
    .father();
    height: 200px;
    width: 200px;
}

// 会编译成
.father{
    margin: 0;
    padding: 0;
}
// margin: 0; padding: 0; 会重复写多次
.inner{
    margin: 0;
    padding: 0;
    height: 100px;
    width: 100px;
}
.inner2{
    margin: 0;
    padding: 0;
    height: 200px;
    width: 200px;
}
```

### all

```less
// 当父类定义了伪类时, 需要使用all 来全部继承
.father{
    margin: 0;
    padding: 0;
}
.father:hover{
    background: red;
}
// 使用all, 不然不会继承.father:hover
.inner:extend(.father all){
    height: 100px;
    width: 100px;
}
```

## 避免编译

```less
body{
    width: ~"calc(100px + 100)";      // 使用~"" 不会被编译
}
```



# 编译工具

## koala

```
http://koala-app.com/index-zh.html
```

