# 引入样式

* 外部 引入外部css文件
* 内部 写在html文件`<head>`标签内
* 内联 写在标签内  
* VUE脚手架中引入在<Style>@import "xxx.css"</style>

优先级： 内联>内部>外部  
使用!import 可将优先级提升至最高  

# 选择器
* id选择器 #id
* 类选择器 .class
* 标签选择器  
* 属性选择器 
    - [attribute]	用于选取带有指定属性的元素。
    - [attribute=value]	用于选取带有指定属性和值的元素。  
    - [attribute~=value]	用于选取属性值中包含指定词汇的元素。  
    - [attribute|=value]	用于选取带有以指定值开头的属性值的元素，该值必须是整个单词。
    - [attribute^=value]	匹配属性值以指定值开头的每个元素。
    - [attribute$=value]	匹配属性值以指定值结尾的每个元素。
    - [attribute*=value]	匹配属性值中包含指定值的每个元素。
* 后代选择器
    - 两个选择器通过空格分隔，可从所有后代元素中选择
* 子元素选择器
    - 通过 > 分隔，只可从子元素中选择
* 相邻兄弟选择器
    - 通过 + 分隔，只可选择相邻的并且在后面的元素
* 伪类选择器
    - :focus  
    选择已经获取焦点的元素
    - :first-child
    选择第一个子元素
* 伪元素
    - :first-line
    对文本首行设置样式
    - :first-letter
    对文本首字母设置样式
    - :before
    在元素前插入新内容
    - :after
    在元素后插入新内容
# 样式
* 背景 （不可被继承）
    - 背景色 background-color 
        - 颜色单词 red，blue，black等
        - ~#000000~ 或者缩写 ~#000~ (缩写需要每相邻的两位数字相同) 值为0-F 16进制数字
        - rgb(0,0,0), rgba(0,0,0,0) 值为0-255， a为透明度，值为0-1，四个值都可使用百分数  
    - 背景图片 background-image
        - url(图片路径)
    - 背景重复 background-repeat
        - 水平平铺 repeat-x
        - 垂直平铺 repeat-y
        - 不平铺 no-repeat
    - 背景定位 background-position
        - top,right,bottom,left,center
        - 百分比（以图片中心和容器中心做对比） 
        - 长度值（以图片左上角和容器左上角做对比）
    - 背景关联 background-attachment
        - scroll 默认值，背景图片随滚动条滚动
        - fixed 背景图片不随滚动条滚动
    - 同时声明全部属性，顺序如下  
        background: 背景色 背景图片 背景重复 背景关联 背景定位; 
* 文本
    - 颜色 color
    - 背景颜色 background-color
    - 行高 line-height （设置为100%可实现文本垂直居中）
    - 缩进文本 text-indent
    - 水平对齐 text-alignt
        - left，center，right
        - 两端对齐 justify
    - 字(单词)间隔 word-spacing
    - 字母间隔 letter-spacing
    - 字符转换 text-transform
        - 大写 uppercase
        - 小写 lowercase
        - 首字母大写 capitalize
    - 文本装饰 text-decoration
        - 下划线 underline
        - 上划线 overline
        - 删除线 line-through
    - 空白符处理 white-space  
        - pre 不合并空白符，换行符，不允许自动换行
        - nowrap 合并空白符，换行符，不允许自动换行
        - pre-wrap 不合并空白符，换行符，允许自动换行
        - pre-line 合并空白符，保留换行符， 允许自动换行
        - normal 合并空白符，换行符, 允许自动换行
* 字体
    - 字体 font-family
        - 可设置多个字体防止用户本地没有对应的字体
    - 字体风格 font-style
        - 斜体 italic
        - 文本倾斜 oblique（和斜体没什么区别）
    - 字体变形 font-variant
        -  small-caps 可让小写字母变大写并保持小写的大小
    - 字体加粗 font-weight
    - 字体大小 font-size 默认16px
* 链接
    - 未访问的样式 a:link
    - 已访问的样式 a:visited
    - 鼠标指针悬停样式 a:hover
    - 被点击时样式 a:active  
    上述四种样式排序规则 
* 列表
    - 列表项标志: list-style-type
    - 列表项图像: list-style-image
# 盒子模型
* 外边距 margin  
  两个相邻标签的外边距会合并，取最大值
* 边框 border
    - 边框样式 border-style
    - 宽度 border-width
    - 颜色 border-color
    - 圆角边框 border-radius
* 内边距 padding 
* box-sizing
    - border-box
    设置的height, width为border+padding+content的和
* overflow  
    overflow 属性规定当内容溢出元素框时发生的事情
    - scroll 始终显示滚动条 
    - hidden 隐藏内容   
    - visible 显示在框外 
    - auto 自动处理溢出的内容
# 定位
* position
    - 相对定位 relative  
      相对本身应存在的位置定位  
      占空间
      
    - 绝对定位 absolute  
      相对父标签定位（父标签需要设置position属性为relative/absolute/fixed）
      不占空间，不能和float同时使用
      
    - 浮动定位 fixed  
      相对与浏览器视窗定位 
      
    - 混合 sticky(兼容性差)  
      relative和fixed混合，表现为, 滑动到某一位置时, 悬浮, 停止滑动
      
      需要配合top | bottom | left | right 使用
      
    - 层叠 z-index
      需配合position属性一起使用     
# 对齐
* margin实现水平对齐  
  margin:auto
* position实现左右对齐  
  position：absolute
# flex布局

## 布局(flex):弹性容器

display：flex   
* justify-content: 主轴
    - flex-start： 居左对齐
    - flex-end: 居右对齐
    - center: 居中
    - space-around: 均匀分布，中间间距为两端距离的2倍
    - space-evenly: 均匀分布，中间间距与两端距离相同
    - space-between: 位于两端
* align-items: 侧轴
    - flex-start： 居上对齐
    - flex-end: 局下对齐
    - center: 居中对齐
    - stretch: 拉伸子元素高度至100%（子元素不能设置高度）
    - baseline: 与第一个子元素中的文字对齐
* flex-direction: 修改主轴方向
    - row：默认值
    - row-reverse：从右到左排列
    - column：从上到下
    - column-reverse：从下到上
* flex-wrap: 换行
    - nowrap：不换行，超出行宽度时，会自动调整子元素宽度，挤在一行中
    - wrap：超出行宽，子元素换行
    - wrap-reverse：超出行宽，子元素换行到上面
* align-content：多行，只在多行时起作用
    - stretch：默认，当子元素设置了高度时，保留子元素高度，未设置高度时会拉伸子元素
    - flex-start: 局上
    - flex-end：局下
    - center：居中
    - space-between: 位于两端
    - space-around: 均匀分布，中间间距为两端距离的2倍
    - space-evenly: 均匀分布，中间间距与两端距离相同
## 布局(flex):子元素样式
* flex-basis：相当于width，比width优先级高
* flex-grow：分配剩余空间
* flex-shrink：子元素宽度和超出父元素宽度，该属性设置各个子元素的压缩比例
    - 1：默认值
    - 0：不压缩
    - N：压缩比例，数字越大，压缩比例越大

# 高级用法

## 定义变量

```css
/* :root为跟节点 */
:root{
    --color-text: #666;
    --font-size: 14px;
}
```

## 使用变量

```css
body{
    color: var(--color-text)
}
```



# 通用

## normalize.css

- **保留有用的浏览器默认样式**而不是像一些初始化代码一样删除它们
- **规范化**大部分HTML元素
- **更正**浏览器自身的**bug**并保证各**浏览器的一致性**
- 用一些小技巧**优化CSS可用性**
- 用注释和详细的文档来**解释代码**

### 安装

从github上获取最新版本

## base.css

基本样式

```css
@import "normalize.css";

:root {
  --color-text: #666;
  --color-high-text: #ff5777;
  --color-tint: #ff8198;
  --color-background: #fff;
  --font-size: 14px;
  --line-height: 1.5;
}

*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-font: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei", "微软雅黑",Arial,sans-serif;
  user-select: none; /* 禁止用户鼠标在页面上选中文字/图片 */
  -webkit-tap-highlight-color: transparent;
  background: var(--color-background);
  color: var(--color-text);
  width: 100vw;
}

a {
  color: var(--color-text);
  text-decoration: none;
}

.clear-fix {
  zoom: 1;
}

.clear-fix::after {
  clear: both;
  content: '';
  display: block;
  width: 0;
  height: 0;
  visibility: hidden;
}

.left {
  float: left;
}

.right {
  float: right;
}
```





