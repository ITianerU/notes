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
    let ctx = canvas.getContext('2d');
    // 移动坐标
    ctx.moveTo(100, 100);
    ctx.lineTo(200, 100);
    ctx.lineTo(100, 200);
    // 闭合路径
    ctx.closePath();
    // 设置颜色
    ctx.strokeStyle = "#234942";
    // 设置线宽
    ctx.lineWidth = 4;
    // 画线
    ctx.stroke();
    // 设置填充颜色
    ctx.fillStyle = "red";
    // 填充
    ctx.fill();
</script>
</html>
```

