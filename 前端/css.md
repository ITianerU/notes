# 引入样式
* 外部 引入外部css文件
* 内部 写在html文件`<head>`标签内
* 内联 写在标签内  
* VUE脚手架中引入在<Style>@import "xxx.css"</style>

优先级： 内联>内部>外部  
使用!import 可将优先级提升至最高  
***
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
***
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
***

# 盒子模型
![box][box-model]
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
***

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
    - 混合 sticky  
      relative和fixed混合，在屏幕范围内是relative，超出屏幕为fixed
    - 层叠 z-index
      需配合position属性一起使用     
***

# 对齐
* margin实现水平对齐  
  margin:auto
* position实现左右对齐  
  position：absolute
***


# 布局(flex):弹性容器
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
***
# 布局(flex):子元素样式
* flex-basis：相当于width，比width优先级高
* flex-grow：分配剩余空间
* flex-shrink：子元素宽度和超出父元素宽度，该属性设置各个子元素的压缩比例
    - 1：默认值
    - 0：不压缩
    - N：压缩比例，数字越大，压缩比例越大

    
[box-model]:data:image/gif;base64,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