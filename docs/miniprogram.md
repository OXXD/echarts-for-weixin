# 小程序使用文档

参考代码 `tools/demo`

1. 首先在页面的 json 文件加入 usingComponents 配置字段

```json
{
  "usingComponents": {
    "ec-canvas": "echarts-for-weixin"
  }
}
````

2. 项目根目录创建 `package.json` 并执行 npm install 安装依赖

```json
{
  "dependencies": {
    "echarts": "^5.1.2",
    "echarts-for-weixin": "^1.0.0"
  }
}
```

3. 小程序开发者工具中构建 npm

点击开发者工具中的菜单栏：工具 --> 构建 npm

![construction](./docs/construction.png)

4. 在页面中引入 `echarts`，可以从 `npm` 引入 `echarts`，也可以引入本地自定义构建的 `echarts` 以减小体积

```js
import * as echarts from 'echarts' // 从 npm 引入 echarts
import * as echarts from './echarts' // 或者从本地引入自定义构建的 echarts
```

5. 然后可以在对应页面的 wxml 中直接使用该组件

```wxml
<view class="container">
  <ec-canvas id="mychart-dom-bar" canvas-id="mychart-bar" echarts="{{ echarts }}" ec="{{ ec }}"></ec-canvas>
</view>
```

6. `ec-canvas` 的具体用法和如何初始化图表请参考 [Echarts 官方小程序示例](https://github.com/ecomfe/echarts-for-weixin#%E5%88%9B%E5%BB%BA%E5%9B%BE%E8%A1%A8)

<details>

<summary>示例代码</summary>

```js
import * as echarts from 'echarts'

let chart = null;

function initChart(canvas, width, height, dpr) {
  chart = echarts.init(canvas, null, {
    width: width,
    height: height,
    devicePixelRatio: dpr // new
  });
  canvas.setChart(chart);

  var option = {
    tooltip: {
      trigger: 'axis',
      axisPointer: {            // 坐标轴指示器，坐标轴触发有效
        type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
      },
      confine: true
    },
    legend: {
      data: ['热度', '正面', '负面']
    },
    grid: {
      left: 20,
      right: 20,
      bottom: 15,
      top: 40,
      containLabel: true
    },
    xAxis: [
      {
        type: 'value',
        axisLine: {
          lineStyle: {
            color: '#999'
          }
        },
        axisLabel: {
          color: '#666'
        }
      }
    ],
    yAxis: [
      {
        type: 'category',
        axisTick: { show: false },
        data: ['汽车之家', '今日头条', '百度贴吧', '一点资讯', '微信', '微博', '知乎'],
        axisLine: {
          lineStyle: {
            color: '#999'
          }
        },
        axisLabel: {
          color: '#666'
        }
      }
    ],
    series: [
      {
        name: '热度',
        type: 'bar',
        label: {
          normal: {
            show: true,
            position: 'inside'
          }
        },
        data: [300, 270, 340, 344, 300, 320, 310],
        itemStyle: {
          // emphasis: {
          //   color: '#37a2da'
          // }
        }
      },
      {
        name: '正面',
        type: 'bar',
        stack: '总量',
        label: {
          normal: {
            show: true
          }
        },
        data: [120, 102, 141, 174, 190, 250, 220],
        itemStyle: {
          // emphasis: {
          //   color: '#32c5e9'
          // }
        }
      },
      {
        name: '负面',
        type: 'bar',
        stack: '总量',
        label: {
          normal: {
            show: true,
            position: 'left'
          }
        },
        data: [-20, -32, -21, -34, -90, -130, -110],
        itemStyle: {
          // emphasis: {
          //   color: '#67e0e3'
          // }
        }
      }
    ]
  };

  chart.setOption(option);
  return chart;
}

Page({
  data: {
    echarts,
    ec: {
      onInit: initChart
    }
  },
  onReady() {
    setTimeout(function () {
      // 获取 chart 实例的方式
      console.log(chart)
    }, 2000);
  }
})
```

</details>