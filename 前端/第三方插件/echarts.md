# 整合VUE

## 安装

```bash
npm install vue-echarts --Save
yarn add vue-echarts
```

## 配置

```js
// 在main.js中配置
/* 引入echarts */
import * as echarts from 'echarts'
// 设置echarts全局变量
Vue.prototype.$echarts = echarts;
import ECharts from 'vue-echarts'
import { use } from "echarts/core";
import { CanvasRenderer } from "echarts/renderers";
// 导入要使用的图
import { PieChart,LineChart, BarChart  } from "echarts/charts";
// 导入要使用的组件
import {
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent,
  ToolboxComponent,
} from "echarts/components";

use([
  CanvasRenderer,
  PieChart,
  TitleComponent,
  TooltipComponent,
  LegendComponent,
  GridComponent,
  ToolboxComponent,
  LineChart,
  BarChart
]);

// 注册组件后即可使用
Vue.component('vue-chart', ECharts)
```

## 使用

```vue
<vue-chart :option="option"></vue-chart>
```

### 折线图

```json
option: {
    // 整体字体
    textStyle: {
      color: "#FFFFFF",
      fontFamily: "Arial",
    },
    // 提示框
    tooltip: {
        trigger: 'axis'
    },
    // 边距
    grid: {
        left: '3%',
        right: '3%',
        bottom: '0%',
        top: '20%',
        containLabel: true
    },
    // 折线颜色
    color: ["#2DE35E", "#EBBB4B"],
    // 图例
    legend: {
        data: ['今日', '昨日'],
        // 字体样式
        textStyle: {
            color: "#FFFFFF",
            fontSize: 30,
            fontFamily: "Arial",
        },
        // 位置
        left: "right",
        // 图例样式
        icon: "circle",
    },
    // x轴, 与y轴的配置一摸一样
    xAxis: {
        data: this.todayHoursList,
        // 坐标文字样式
        axisLabel: {
            fontSize: 20
        },
        // 坐标轴样式
        axisLine: {
            lineStyle: {
                color: '#35D6E9'
            }
        }
    },
    // y轴
    yAxis: {
        // 根据值的大小, 自动生成y轴坐标
        type: 'value',
        // 控制y轴刻度数量
        splitNumber: 1,
        // 基准线
        splitLine: {
            show: false,
        },
        // 坐标轴样式
        axisLabel: {
            fontSize: 30
        },
    },
    series: [
        {
            name: '今日',
            // 折线图
            type: 'line',
            data: [120, 132, 101, 134, 90, 230, 210, 23, 4, 56, 345, 12]
        },
        {
            name: '昨日',
            type: 'line',
            data: [220, 182, 191, 234, 290, 330, 310, 23, 4, 56, 345, 12]
        },
    ]
}
```

### 柱状图

#### **纵向**

```json
option: {
    // 整体字体
    textStyle: {
      color: "#FFFFFF",
      fontFamily: "Arial",
    },
    // 提示框
    tooltip: {
        trigger: 'axis'
    },
    // 边距
    grid: {
        left: '3%',
        right: '3%',
        bottom: '0%',
        top: '20%',
        containLabel: true
    },
    xAxis: {
        data: this.last30DaysList,
        // 坐标文字样式
        axisLabel: {
            fontSize: 17
        },
        // 坐标轴样式
        axisLine: {
            lineStyle: {
                color: '#35D6E9'
            }
        }
    },
    yAxis: {
        // 根据值的大小, 自动生成y轴坐标
        type: 'value',
        // 控制y轴刻度数量
        splitNumber: 4,
        splitLine: {
            show: false,
        },
        axisLabel: {
            fontSize: 26
        },
    },
    series: [
        {
            type: 'bar',
            data: [120, 132, 101, 134, 90, 230, 210, 23, 4, 56, 345, 12],
            itemStyle: {
                // 柱子渐变色
                color: new this.$echarts.graphic.LinearGradient(
                    0, 0, 0, 1, [
                        {offset: 0, color: "#3CB035"},
                        {offset: 1, color: "#086E3D"},
                    ]
                )
            },
        },
    ]
},
```

#### 横向

```json
// 柱子上的label
let label = {
    show: true,
    fontSize: 30,
    position: [22, -40],
    color: "#fff",
    formatter: (data) => {
        return `{name|${data.name}}`;
    },
    // 自定义样式
    rich: {
        name: {
            fontSize: 30,
        }
    }
};
option: {
    // 整体字体
    textStyle: {
      color: "#FFFFFF",
      fontFamily: "Arial",
    },
    // 提示框
    tooltip: {
        trigger: 'axis'
    },
    // 边距
    grid: {
        left: '3%',
        right: '3%',
        bottom: '0%',
        top: '20%',
        containLabel: true
    },
    color: ["#EA5914"],
    xAxis: {
        show: false,
    },
    yAxis: [
        {
            show: false,
            data: ["标题1", "标题1", "标题1", "标题1"],
            axisLabel: {
                show: false
            },
        },
        // 第二个y轴, 默认是在右侧
        {
            show: true,
            data: ["标题1", "标题1", "标题1", "标题1"],
            axisLabel: {
                fontSize: 30
            },
            axisLine: {
                show: false
            }
        }
    ],
    series: [
        {
            type: 'bar',
            data: [
                {name: "充电模拟故障", value: 120, label: label },
                {name: "电度表通信故障", value: 132, label: label },
                {name: "急停按钮动作故障", value: 101, label: label },
                {name: "BMS通信异常", value: 134, label: label },
                {name: "绝缘检测故障", value: 90, label: label },
                {name: "避雷器故障", value: 230, label: label },
            ],
            barWidth: "15.5",
            itemStyle: {
                color: new this.$echarts.graphic.LinearGradient(
                    1, 0, 0, 0, [
                        {offset: 0, color: "#F5B22B"},
                        {offset: 1, color: "#EA5714"},
                    ]
                )
            },
        },
    ]
},
}
```

### 饼图

#### 环形图

```json
options: {
    // 整体字体
    textStyle: {
      color: "#FFFFFF",
      fontFamily: "Arial",
    },
    // 提示框
    tooltip: {
        trigger: 'axis'
    },
    color: ["#56CBE7", "#2E7DDE", "#A3D221"],
    // 图例
    legend: {
        top: 50,
        left: 250,
        right: 0,
        data: ['国网充电卡', '二维码扫码', 'e充电账号'],
        itemWidth: 30,
        itemHeight: 30,
        textStyle: {
            color: ["#56CBE7", "#2E7DDE", "#A3D221"],
            fontSize: 30,
            fontFamily: "Arial",
            padding: [0,0,0,20],
        },
        itemGap: 13,   // 图例间距
    },
    series: [
        {   
            // 指定为饼图
            type: 'pie',
            // 圆环半径, 第一个为内环, 第二个为外环, 相对于父容器
            radius: ["60%", "70%"],
            // 圆环中心坐标, 相对于父容器
            center: ["20%", "50%"],
            // 圆环中心显示的文字
            label: {
                color: "#FFFFFF",
                fontSize: 10,
                show: false,
                position: 'center'
            },
            emphasis: {
                label: {
                    show: true,
                    fontSize: 20,
                    fontWeight: 'bold'
                }
            },
            labelLine: {
                show: false
            },
            data: [
                { value: 1048, name: '国网充电卡' },
                { value: 735, name: '二维码扫码' },
                { value: 580, name: 'e充电账号' },
            ]
        }
    ]
}
```

### 仪表盘

```json
option = {
    // 整体字体
    textStyle: {
      color: "#FFFFFF",
      fontFamily: "Arial",
    },
    // 提示框
    tooltip: {
        trigger: 'axis'
    },
    grid: {
        left: '0%',
        right: '0%',
        bottom: '0%',
        top: '0%',
        containLabel: true
    },
    series: [
        {   
            // 指定为仪表盘
            type: 'gauge',
            // 开始角度
            startAngle: 180,
            // 结束角度
            endAngle: 0,
            // 表盘最大值
            min: 0,
            max: 60,
            // 表盘刻度数量
            splitNumber: 1,
            // 表盘半径, 相对于父容器
            radius: "170%",
            // 表盘中心坐标, 相对于父容器
            center: ["50%", '90%'],
            // 表盘样式
            axisLine: {
                lineStyle: {
                    width: 6,
                    color: [
                        [100, '#39AD35']
                    ]
                }
            },
            // 指针样式
            pointer: {
                length: '70%',
                width: 5,
                // 相对于中心偏移量
                offsetCenter: [0, '-10%'],
                itemStyle: {
                    color: 'auto'
                }
            },
            // 刻度
            splitLine: {
                show: false,
            },
            axisLabel: {
                color: '#39AD35',
                fontSize: 30
            },
            title: {
                show: false,
            },
            detail: {
                show: false
            },
            data: [
                {
                    value: 45
                }
            ]
        }
    ]
}
```



# 地图

```js
function randomValue () {
    return Math.round(Math.random()*1000);
}
let dataList = [
    {name:"南海诸岛",value:  0},
    {name: '北京',   value: this.randomValue()},
    {name: '天津',   value: this.randomValue()},
    {name: '上海',   value: this.randomValue()},
    {name: '重庆',   value: this.randomValue()},
    {name: '河北',   value: this.randomValue()},
    {name: '河南',   value: this.randomValue()},
    {name: '云南',   value: this.randomValue()},
    {name: '辽宁',   value: this.randomValue()},
    {name: '黑龙江', value: this.randomValue()},
    {name: '湖南',   value: this.randomValue()},
    {name: '安徽',   value: this.randomValue()},
    {name: '山东',   value: this.randomValue()},
    {name: '新疆',   value: this.randomValue()},
    {name: '江苏',   value: this.randomValue()},
    {name: '浙江',   value: this.randomValue()},
    {name: '江西',   value: this.randomValue()},
    {name: '湖北',   value: this.randomValue()},
    {name: '广西',   value: this.randomValue()},
    {name: '甘肃',   value: this.randomValue()},
    {name: '山西',   value: this.randomValue()},
    {name: '内蒙古', value: this.randomValue()},
    {name: '陕西',   value: this.randomValue()},
    {name: '吉林',   value: this.randomValue()},
    {name: '福建',   value: this.randomValue()},
    {name: '贵州',   value: this.randomValue()},
    {name: '广东',   value: this.randomValue()},
    {name: '青海',   value: this.randomValue()},
    {name: '西藏',   value: this.randomValue()},
    {name: '四川',   value: this.randomValue()},
    {name: '宁夏',   value: this.randomValue()},
    {name: '海南',   value: this.randomValue()},
    {name: '台湾',   value: this.randomValue()},
    {name: '香港',   value: this.randomValue()},
    {name: '澳门',   value: this.randomValue()}
]
let option = {
    tooltip: {
        formatter:function(params,ticket, callback){
            return "数据量:" + params.value;
        }//数据格式化
    },
    visualMap: {
        type: 'piecewise',  // 分段型
        pieces: [     // 分段
            {gt: 1000, label: '1000以上', color: '#02C07C'},
            {gt: 500, lte: 999, label: '500-999', color: "#02E99E"},
            {gt: 100, lte: 499, label: '100-499', color: "#03FCAB"},
            {gt: 0, lte: 99, label: '0-99', color: '#9CFFDF'},
            {value: 0, label: '无', color: '#F2F2F2'}
        ],
        left: 'left',
        top: 'bottom',
        inRange: {
            color: ['#e0ffff', '#006edd']//取值范围的颜色
        },
        show:true//图注
    },
    geo: {
        map: 'china',
        roam: false,//不开启缩放和平移
        zoom:1.23,//视角缩放比例
        label: {
            normal: {
                show: true,
                fontSize:'10',
                color: 'rgba(0,0,0,0.7)'
            }
        },
        itemStyle: {
            normal:{
                borderColor: 'rgba(0, 0, 0, 0.2)'
            },
            emphasis:{
                areaColor: '#F3B329',//鼠标选择区域颜色
                shadowOffsetX: 0,
                shadowOffsetY: 0,
                shadowBlur: 20,
                borderWidth: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
            }
        }
    },
    series : [
        {
            name: '信息量',
            type: 'map',
            geoIndex: 0,
            data: dataList
        }
    ]
};
this.myChart = echarts.init(document.getElementById('map'));
this.myChart.setOption(this.option);
```
