# 简述

相对于scss, less省略了括号, 分号, 逗号等符号

# 安装

## npm

```shell
cnpm install -D stylus
cnpm install -D stylus-loader
```

# 基础用法

## 变量

```stylus
color = green
```

## 注释

```scss
/* 不压缩代码时, 编译后会保留 */
// 任何情况编译后都不会保留
```

## 嵌套

```stylus
body
    div
        color red
```

## 伪类

```stylus
body
    div
        // 伪类要使用&
        &:hover
            color red
```

# 高级用法

## 混合

```stylus
// 定义混合
size()
	width 15px
	height 200px
// 使用
div
    size()
```

