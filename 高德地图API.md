# 高德地图API

#### 1.创建地图

```javascript
var map = new AMap.Map('container', {
        resizeEnable: true,
        zoom:11,
        center: [116.397428, 39.90923]
});
```

#### 2.设置 / 获取地图中心点和级别 

##### 设置、获取地图中心点（`setCenter`、`getCenter`）

```javascript
// 传入经纬度，设置地图中心点
var position = new AMap.LngLat(116, 39);  // 标准写法
// 简写 var position = [116, 39]; 
map.setCenter(position); 
// 获取地图中心点
var currentCenter = map.getCenter(); 
```

##### 设置、获取地图缩放级别（`setZoom`、`getZoom`）

```javascript
// 设置地图级别，级别为数字。
// PC上，参数zoom可设范围：[3,18]；
// 移动端：参数zoom可设范围：[3,19]
map.setZoom(13); 
// 获取地图级别
var currentZoom = map.getZoom(); 
```

##### 同时设置地图中心点和缩放级别（`setZoomAndCenter`）

```javascript
// 同时传入缩放级别和中心点经纬度
map.setZoomAndCenter(14, [116.205467, 39.907761]);
```

#### 3.创建，渲染点标记

##### 自定义图标

```javascript
// 创建一个 Icon
    var startIcon = new AMap.Icon({
        // 图标尺寸
        size: new AMap.Size(25, 34),
        // 图标的取图地址
        image: '//a.amap.com/jsapi_demos/static/demo-center/icons/dir-marker.png',
        // 图标所用图片大小
        imageSize: new AMap.Size(135, 40),
        // 图标取图偏移量
        imageOffset: new AMap.Pixel(-9, -3)
    });
```

##### 创建点标记

```javascript
 // 将 icon 传入 marker
    var startMarker = new AMap.Marker({
        // map指定渲染的地图，创建marker后，自动渲染
        map: map
        position: new AMap.LngLat(116.35,39.89),
        icon: startIcon,
        offset: new AMap.Pixel(-13, -30)
    });
```

##### 给点标记绑定，解绑事件

```javascript
// 绑定事件
map.on('click', clickHandler);
// 解绑事件
map.off('click', clickHandler);
```

#### 4.创建信息窗体

```js
// 创建 infoWindow 实例	
var infoWindow = new AMap.InfoWindow({
   anchor: 'top-left', //窗体锚点的设置
   content: content  //传入 dom 对象，或者 html 字符串
});
// 打开信息窗体
infoWindow.open(map, [116.401337,39.907886]);
```

