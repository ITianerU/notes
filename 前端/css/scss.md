# 基础用法

## 注释

```scss
/* 不压缩代码时, 编译后会保留 */
// 任何情况编译后都不会保留
/*! 编译后会保留 */
```

## 变量

```scss
// 定义
$color: red;
// 变量使用变量
$border: 1px solid $color
// 使用
a {
  color: $color;
}
```

## 嵌套

```scss
#nav{
    h1{
        
    }
}
```

### 伪类

```scss
#nav{
    h1{
        // 如果想使用伪类, 需要加上$
        &:hover{
            
        }    
    }
}
```

### 拼接选择器

```scss
#nav{
    // 会编译成 #nav #nav-text
    & &-text{
        font-size: 15px
    }
}
```

### 属性嵌套

```scss
#nav{
    // 将font-size; font-weight嵌套写
    font{
        size: 15px;
        weight: normal;
    }
}
```

## 导入

- scss文件名以下划线开头的不会被单独编译, 可以专门用于导入

```scss
@import '_xxx.scss';
```



# 高级用法

## 混合

### 无参mixin

```scss
@mixin haha{
  background-color: red;
  width: 100px;
  height: 100px;
  a {
  	color: blue;      
  }
}
#nav{
  & &-div{
    @include haha;
  }
}
```

### 带参mixin

```scss
@mixin haha($bg-color, $a-color){
  background-color: $bg-color;
  width: 100px;
  height: 100px;
  a{
    color: $a-color;
  }
}
#nav{
  div{
    @include haha(yellow, red);
  }
}
#nav{
  div{
    // 传参时加上参数名, 可以不用保证传参顺序
    @include haha($a-color:yellow, $bg-color: red);
  }
}
```

## 继承

```scss
.haha{
  background-color: #2c3e50;
}
.haha a{
  color: red;
}
#nav{
  div{
    // 继承.haha的样式, .haha a也会被继承 会编译成 div a
    @extend .haha;
    color: aliceblue;
  }
}
```

## 函数

### 自定义函数

#### 定义

```scss
$colors: (light: #fff, dark: #000);
@function color($key){
    @return map-get($colors, $key);
}
```

#### 使用

```scss
body{
    background-color: color(light);
}
```

### 常规

#### type-of

```js
// 数据类型有 number, string, color, list
a{	
    // 获取数据类型 number
    num: type-of(1234);
    str: type-of(hello);
    color: type-of(red);
    list: type-of(1px solid black);
}
```

### 颜色

#### darken

```scss
a{	
    // 将颜色加深10%
    color: darken(red, 10%)
}
```

#### lighten

```scss
a{	
    // 将颜色变浅10%
    color: lighten(red, 10%)
}
```

#### soacify(color, val)

增加不透明度

#### transparentize(color, val)

减少不透明度

### 数学

#### abs(num)

绝对值

#### round(num)

四舍五入

#### ceil(num)

向上取整

#### floor(num)

向下取整

#### percentage(num)

转换为百分数

#### min(...nums)

取最小值,  可传任意数量的数字

#### max(...nums)

取最大值,  可传任意数量的数字

### 字符串

#### to-upper-case(str)

转化大写

#### to-lower-case(str)

转化小写

#### str-length(str)

字符串长度

#### str-index(str, fstr)

在str中找到fstr的位置, 索引从1开始

#### str-insert(str, s, index)

在str的index位置插入s

### 列表

#### length(list)

```scss
// 求列表长度, 结果为3
length(1px solid red) 
```

#### nth(list, index)

获取列表的某一项, 索引从1开始

#### index(list, str)

在列表中, 找到某一项的位置

#### append(list, str)

在列表末尾插入一项

#### join(list1, list2, type)

组合两个列表, type可以不传, 当type=comma时, 列表每一项, 会用逗号分隔

### 字典

#### length()

```scss
// 获取键的数量, 返回2
length((h: 50px, w:100px))
```

#### map-get(map, key)

通过键获取值

#### map-keys(map)

获取全部keys

#### map-has-key(map, key)

查询是否包含某个key

#### map-merge(map1, map2)

合并两个map

#### map-remove(map, ..keys)

移除指定的key, 可以有多个

## 运算符

可使用 + - * / ()

```scss
width: 10px * 2
// width: 10px * 10px 不能两个带单位的相乘
// 除法需要加括号, 两个带单位的相除, 单位会被抵消掉
// 不同的单位相除, 会报错
width: (10px / 10)
```

#### 逻辑运算符

and | or | not

## 动态拼接名称

```scss
$a: 'bar';
$b: 'border';
// 动态拼接选择器
#nav-#{$a}{
    // 动态拼接样式名
    #{$b}-color: red;
}
// 也可以动态拼接值
$size: 25;
$wc: #{$size}px;
// 动态拼接注释
/* 哈哈哈 #{$a} 哈啊哈哈 */
```

## 条件控制语句

### @if

```scss
#nav{
   @if true{
   	   width: 10px;	 
   } @else if true{
       width: 15px;	  
   } @else {
       width: 20px;	 
   }
}
```

### @for

- through 包含结束的值
- to 不包含结束的值

```scss
@columns: 4;

@for $i from 1 through 4{
    .col-#{$i}{
        width: 100% / $i;
    }
}
```

### @each

```scss
$icons: sunccess error warning;
@each $icon in $icons {
    .icon-#{$icon}{
        background-image: url("#{$icon}.png")
    }
}
```

### @while

```scss
$icons: sunccess error warning;
$i: 10;
@while $i > 0{
    .item-#{$i}{
        width: 5px * $i;
    }
    $i: $i - 1;
}
```

## 警告

```scss
$colors: (light: #fff, dark: #000);
@function color($key){
    @if not map-has-key($colors, $key){
        // @warn 会输出警告
        @warn "没有找到key";
        // @error 会报错
        @error "没有找到key";
    }
    @return map-get($colors, $key);
}
```



