# 概述

## 整合项目骨架

- **vue**  
- **vue-router**         路由
- **vuex**                  全局状态管理
- **vue-amap**          高德地图
- **vue-touch**          移动端滑动事件
- **vant ui**               有赞vant ui 组件
- **animate.css**      过度动画
- **axios**                 异步请求
- **js-cookie**           操作cookie

# 搭建项目

## 创建项目

```shell
vue create 项目名
```

创建项目时, 选择vue-router, vuex一起安装

## 安装其他依赖包

其他依赖包以官网安装教程为准

### vue-amap 

https://elemefe.github.io/vue-amap/#/zh-cn/introduction/install

#### 配置

main.js

```js
import VueAMap from 'vue-amap'
VueAMap.initAMapApiLoader({
	key: "高德申请的key",
	plugin: ['AMap.Geolocation', 'AMap.MarkerClusterer'],   // 引入的高德地图插件
	uiVersion: '1.0.11',
	v: '1.4.15'
})
Vue.use(VueAMap);
```

#### 使用

```vue
<!-- 地图 -->
<div class="amap-page-container">
    <el-amap ref="map" vid="yxcd-amap" :amap-manager="map.amapManager" :center="map.center" 
             :zooms="map.zooms" class="yxcd-amap" :events="map.events">
        <!-- 点标记 -->
        <el-amap-marker v-for="(marker, index) in map.markers" :position="marker.position" 
                        :events="marker.events" :extData="marker.stationId"
                        :icon="marker.icon" :vid="index" :key="index"></el-amap-marker>
    </el-amap>
</div>
```

```js
import {AMapManager} from 'vue-amap'
let amapManager = new AMapManager();
export default {
    name: 'Map',
    data() {
        return {
            map: {
                // 地图实例
                mapInstance: null,
                // 地图管理插件
                amapManager: amapManager,
                // 地图中心坐标
                center: [116.293308, 40.045565],
                // 缩放层级
                zooms: [6,18],
                // 点
                markers: [],
                // 点聚合
                markerRefs: [],
                // 被点击的marker
                clickedMarker: null,
                // 弹窗
                infoWindow: null,
                // 点图标
                icon: null,
                bigIcon: null,
                // 地图是否可以移动
                isMove: true,
                // 地图是否可以缩放
                isZoomChange: true,
                // 地图事件
                events: {
                    init: (o) => {
                        this.initMap();
                        this.initMarkerIcon();
                        this.loginAuthentication();
                    },
                    // 移动地图事件
                    'moveend': () => {
                        if(this.map.isMove){
                            this.map.isMove = false;
                            this.mapMoveLoadMarkers();
                        }
                    },
                    // 地图缩放事件
                    'zoomchange': () => {
                        if(this.map.isZoomChange){
                            this.map.isZoomChange = false;
                            this.map.isMove = false;
                            this.mapZoomChangeLoadMarkers();
                        }
                    },
                    // 地图点击事件
                    'click': (e) => {
                        this.mapClick();
                    }
                },
            }, 
        }
    },
    methods: {
        // 登录认证
        loginAuthentication(){
            let that = this;
            let params = new URLSearchParams();
            params.append('token', Cookies.get("token"));
            this.$api.user.userinfo(params).then(function (res) {
                // that.getStations();
                // 缩放地图, 因为缩放地图会触发地图缩放事件,
                // 会发接口获取台区数据, 所以放到这里在token正常时, 获取数据
                that.map.mapInstance.setZoom(18);
            }).catch(function(res) {
                console.log(res);
            })
        },
        // 初始化地图
        initMap(){
            this.map.mapInstance = this.map.amapManager.getMap();
        },
        // 初始化marker图标
        initMarkerIcon(){
            this.map.icon = new AMap.Icon({  // 小图标
                size: new AMap.Size(40, 40),
                imageSize: new AMap.Size(40, 40),
                image: markImg
            });
            this.map.bigIcon = new AMap.Icon({  // 大图标
                imageOffset: new AMap.Pixel(-10, -4),
                size: new AMap.Size(60, 60),
                imageSize: new AMap.Size(60, 60),
                image: markImg
            });
        },
        // 地图平移加载markers
        mapMoveLoadMarkers(){
            this.clearAll();
            // 获取移动后的地图中心点坐标
            let center = this.map.mapInstance.getCenter();
            this.map.center = [center.lng, center.lat];
            setTimeout(this.getStations, 1000);
        },
        // 地图缩放加载markers
        mapZoomChangeLoadMarkers(){
            this.clearAll();
            // 获取地图缩放的zoom级别, 计算站点搜索半径
            let zoom = this.map.mapInstance.getZoom();
            if (zoom >= 14) {
                this.param.radius = 10;
            } else {
                this.param.radius =  Math.pow(2, 15 - zoom) + 10;
            }
            setTimeout(this.getStations, 1000);
        },
        // 点击地图事件
        mapClick(){
            // 清楚各种弹窗
            this.clearAll();
        },
    }
}
```



### vue-touch

https://github.com/vuejs/vue-touch/tree/next

#### 配置

main.js

```js
import VueTouch from "vue-touch"
Vue.use(VueTouch, {name: "v-touch"})
```

#### 使用

```vue
<!-- 监听上滑和下滑事件 -->
<v-touch @swipeup="methodxxx" @swipedown="methodxxx2"></v-touch>
```

### vant ui

https://youzan.github.io/vant/#/zh-CN/quickstart