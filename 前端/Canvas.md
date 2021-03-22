# 基础用法

```html
<html lang="en">
<body>
    <!-- 设置canvas的宽高要用属性, 不推荐使用css, css会拉伸图像,使图像发虚 -->
    <canvas id="demo">
        ie9以上才支持canvas
        您的浏览器不支持canvas, 请升级浏览器
    </canvas>
</body>
<script>
    let canvas = document.getElementById("demo");
    canvas.style.border = "1px solid red";
    canvas.height = 600;
    canvas.width = 600;
    // 重新设置画布宽度, 会清空全部内容
    canvas.width = canvas.width
    let ctx = canvas.getContext('2d');
    // 移动坐标
    ctx.moveTo(100, 100);
    // 画线
    ctx.lineTo(200, 100);
    ctx.lineTo(100, 200);
    // 闭合路径
    ctx.closePath();
    // 设置颜色
    ctx.strokeStyle = "#234942";
    // 设置线宽
    ctx.lineWidth = 4;
    // 描边
    ctx.stroke();
    // 设置填充颜色
    ctx.fillStyle = "red";
    // 填充
    ctx.fill();
</script>
</html>
```

## 阴影设置

```js
// 颜色
ctx.shadowColor = "red"
// 模糊级别
ctx.shadowBlur = 10
// 偏移量
ctx.shadowOffsetX = 10
ctx.shadowOffsetY = 10
```

## 渐变

### 线性渐变

```js
// 参数: 起点坐标, 终点坐标
let grd = ctx.createLinearGradient(0,0,170,0)
// 设置渐变颜色, 参数: 渐变的位置0-1之间,  颜色
grd.addColorStop(0, "red")
grd.addColorStop(0.5, "green")
grd.addColorStop(1, "blue")
// 将颜色设置为渐变色
ctx.fillStyle = grad
```

### 圆形渐变

```js
// 参数: 渐变开始内圆坐标, 内圆半径, 结束外圆坐标, 外圆半径
let rlg = ctx.createRadialGradient(300, 300, 10, 300, 300, 200)
rlg.addColorStop(0, 'red')
rlg.addColorStop(0.2, 'pink')
rlg.addColorStop(1, 'black')
ctx.fillStyle = rlg
ctx.fillRect(100, 100, 500, 500)
```



# 绘图状态

```html
<html lang="en">
<body>
    <canvas id="demo">
        ie9以上才支持canvas
        您的浏览器不支持canvas, 请升级浏览器
    </canvas>
</body>
<script>
    let canvas = document.getElementById("demo");
    canvas.style.border = "1px solid red";
    canvas.height = 600;
    canvas.width = 600;
    let ctx = canvas.getContext('2d');
    // 移动坐标
    ctx.moveTo(100, 100);
    // 画线
    ctx.lineTo(200, 100);
    ctx.lineTo(100, 200);
    // 闭合路径
    ctx.closePath();
    // 设置颜色
    ctx.strokeStyle = "#234942";
    // 设置线宽
    ctx.lineWidth = 4;
    // 描边
    ctx.stroke();
    // 设置填充颜色
    ctx.fillStyle = "red";
    // 填充
    ctx.fill();
</script>
</html>
```

## beginPath

```html
<canvas id="demo">
    ie9以上才支持canvas
    您的浏览器不支持canvas, 请升级浏览器
</canvas>
</body>
<script>
    let canvas = document.getElementById("demo");
    canvas.style.border = "1px solid red";
    canvas.height = 600;
    canvas.width = 600;
    let ctx = canvas.getContext('2d');
    // 画红线
    ctx.strokeStyle = 'red';
    ctx.moveTo(100, 100);
    ctx.lineTo(200, 100);
    ctx.stroke();
    // 画黄线
    // 此时需要使用beginPath重新开启状态, 不然会将上面的线重新描边, 颜色会重叠, 之前的样式会继承
    ctx.beginPath();
    ctx.strokeStyle = 'yellow';
    ctx.moveTo(100, 200);
    ctx.lineTo(200, 200);
    ctx.stroke();
</script>
```

# 绘制形状

## 矩形

```js
// 画矩形前两个参数是坐标, 后两个参数, 宽高
ctx.rect(100, 100, 50, 50)
ctx.stroke()
// 直接描边
ctx.strokeRect(100, 100, 50, 50)
// 填充矩形
ctx.fillRect(200, 200, 100, 150)
// 橡皮擦, 擦除指定区域
ctx.clearRect(200, 200, 100, 150);
```

## 圆形

```js
// 画矩形参数分别为圆心坐标, 半径, 起始角度, 结束角度, 是否逆时针
// 起始角度和结束角度都是使用的弧度
// 弧度角度转换公式   弧度 = 角度 * Math.pi / 180
// moveto让从圆心开始画
ctx.moveTo(100,100)
ctx.arc(100, 100, 100, Math.PI / 180, 90 * Math.PI / 180);
// 画整个圆不需要clostPath()
ctx.closePath()
ctx.stroke()
```

# 绘制文字

```js
// 绘制空心文本 文本, 坐标
ctx.strokeText("xx", 100, 100)
// 参数: 文本, 坐标
ctx.fillText("xx", 100, 100);
// 设置对齐方式 start: 文本在指定位置开始, end: 文本在指定位置结束, left: 左对齐, right: 右对齐, center: 文本中心在指定位置
ctx.textAlign = 'end'
// 设置字体大小, 必须指定字体
ctx.font = "100px 微软雅黑"
// 返回文本宽度对象, 会根据设置的字体大小变化
ctx.measureText('hello world')
```

# 绘制图片

```js
// 创建img对象
let img = new Image()
// 设置图片加载事件
img.onload = function(){
    // 绘制图片, 参数: 图片, 坐标, 宽高
	ctx.drawImage(img, 100, 100, 50, 50)
    // 保持图片宽高比
    let ow = img.width
    let oh = img.height
    ctx.drawImage(img, 100, 100, 50, 50 * oh/ow)
    
}
// 设置src属性, 立刻去加载图片
img.src = 'a.jpg'
```

## 裁剪图片

```js
// 裁剪图片, 参数: 图片, 要裁剪的起始坐标(相对于原图中左上角的点), 裁剪的大小, 绘制的坐标, 绘制的大小
ctx.drawImage(img, 100, 100, 100, 100, 200, 100, 200, 200)
```

## 序列帧

```js
// 每秒绘制10帧
setInterval(function(){
    // 清除指定位置的图片, 再绘制新的图案
    ctx.clearRect(200, 100, 200, 200)
    ctx.drawImage(img, 100, 100, 100, 100, 200, 100, 200, 200)
}, 100)
```

# 图形变换

## 画布位移

```js
// 将画布移动到指定位置, 画布的0,0坐标变为新移动后的位置, 移动之前的绘制的图形不会收到影响
ctx.translate(200,200)
// 移动画布后, 需要moveto到新画布
ctx.moveTo(0, 0)
```

## 画布旋转

```js
// 旋转后绘制的图形才会生效, 旋转之前的绘制的图形不会收到影响
// 围绕00点进行旋转
ctx.rotate(30 * Math.PI / 180)
ctx.fillRect(100, 100, 200, 200)
```

## 画布放大

```js
// 放大后绘制的图形才会生效, 放大之前的绘制的图形不会收到影响
// 参数: x轴放大的倍数, y轴放大的倍数
ctx.scale(2,2)
ctx.fillRect(100, 100, 200, 200)
```

## 画布透明

```js
// 透明度设置好后绘制的图形才会生效, 设置前的绘制的图形不会收到影响
ctx.gloablAlpha = .3
ctx.fillRect(100, 100, 200, 200)
```

## 状态保存与回滚

状态保存后, 绘制的图形还会存在, 保存的是画布的样式

### 保存

```js
ctx.save()
```

### 回滚

```js
ctx.restore()
```

# 画布保存

保存为base64编码图片

```js
// 参数: 保存的类型(image/png, image/jpeg), 保存的质量(0-1)
canvas.toDataURL("image/jpg", 1)
```

# 画布渲染画布

将一个画布的图像, 渲染到另一个画布

```js
// 将画布1的图案, 绘制到画布2
ctx2.drawImage(canvas1, 0, 0)
```

## 优化

在隐藏的画布先画图形因为, 是隐藏的所以不占用gpu, 画完后, 一次复制到显示的画布上, 减少多次绘图性能开销