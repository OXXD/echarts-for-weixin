# Taro 使用文档

相比小程序引用，`Taro` 引用更具灵活性，可以从 `npm` 引入 `echarts` 也可以引入本地自定义构建的 `echarts` 以减小体积。但是需要一些额外的配置。

另外 `Taro` 也可以直接引入 `echarts/core` 和需要的组件达到按需引用的效果，详细操作可以参考 [Taro 按需引用](./taro-manual-load.md)

## 准备工作

1. 安装依赖

```bash
npm install echarts-for-weixin
```

2. 在项目根目录中新建文件 `project.package.json` 或者自定义命名，此文件是小程序的 `package.json`，并在下一步中添加小程序自定义构建 npm 方式。这么做的目的是为了能够共享项目 `node_modules`

3. 在 `project.config` 的 `setting` 中添加小程序自定义构建 npm 方式，参考 [自定义 node_modules 和 miniprogram_npm 位置的构建 npm 方式](https://developers.weixin.qq.com/miniprogram/dev/devtools/npm.html#%E8%87%AA%E5%AE%9A%E4%B9%89-node-modules-%E5%92%8C-miniprogram-npm-%E4%BD%8D%E7%BD%AE%E7%9A%84%E6%9E%84%E5%BB%BA-npm-%E6%96%B9%E5%BC%8F)

```json
{
	"setting": {
    "packNpmManually": true,
    "packNpmRelationList": [
      {
        "packageJsonPath": "../project.package.json",
        "miniprogramNpmDistDir": "./"
      }
    ]
	}
}
```

4. 执行 `Taro` 的开发或者打包命令进行项目开发

```bash
npm run dev:weapp
```

5. 小程序开发者工具中构建 npm

点击开发者工具中的菜单栏：工具 --> 构建 npm

![construction](./construction.png)

## 引入 Echarts

1. 在全局的 `app.config.js` 中添加或者在单个需要使用到 `echarts` 的页面配置中添加引用组件

```json
{
  "usingComponents": {
    "ec-canvas": "echarts-for-weixin"
  }
}
````

2. 在页面中引入 `echarts`，可以从 `npm` 引入 `echarts`，也可以引入本地自定义构建的 `echarts` 以减小体积

```js
import * as echarts from 'echarts' // 从 npm 引入 echarts
import * as echarts from './echarts' // 或者从本地引入自定义构建的 echarts
```

3. 将引入的 `echarts` 传给组件

```js
<ec-canvas 
  id='mychart-dom-area' 
  canvas-id='mychart-area' 
  echarts={echarts} // 将引入的 echarts 传给组件
  ec={this.state.ec}
/>
```

4. `ec-canvas` 的具体用法和如何初始化图表请参考 [Echarts 官方小程序示例](https://github.com/ecomfe/echarts-for-weixin#%E5%88%9B%E5%BB%BA%E5%9B%BE%E8%A1%A8)

示例代码

```js
function initChart(canvas, width, height) {
  const chart = echarts.init(canvas, null, {
    width: width,
    height: height
  })
  canvas.setChart(chart)
  const model = {
    yCates: ['Saturday', 'Friday', 'Thursday',
      'Wednesday', 'Tuesday', 'Monday',
      'Sunday'],
    xCates: ['1', '2', '3', '4', '5'],
    data: [
      // [yCateIndex, xCateIndex, value]
      [0, 0, 5], [0, 1, 7], [0, 2, 3], [0, 3, 5], [0, 4, 2],
      [1, 0, 1], [1, 1, 2], [1, 2, 4], [1, 3, 8], [1, 4, 2],
      [2, 0, 2], [2, 1, 3], [2, 2, 8], [2, 3, 6], [2, 4, 7],
      [3, 0, 3], [3, 1, 7], [3, 2, 5], [3, 3, 1], [3, 4, 6],
      [4, 0, 3], [4, 1, 2], [4, 2, 7], [4, 3, 8], [4, 4, 9],
      [5, 0, 2], [5, 1, 2], [5, 2, 3], [5, 3, 4], [5, 4, 7],
      [6, 0, 6], [6, 1, 5], [6, 2, 3], [6, 3, 1], [6, 4, 2]
    ]
  }

  const data = model.data.map(function (item) {
    return [item[1], item[0], item[2] || '-']
  })

  const option = {
    tooltip: {
      position: 'top'
    },
    animation: false,
    grid: {
      bottom: 60,
      top: 10,
      left: 80
    },
    xAxis: {
      type: 'category',
      data: model.xCates
    },
    yAxis: {
      type: 'category',
      data: model.yCates
    },
    visualMap: {
      min: 1,
      max: 10,
      show: false,
      calculable: true,
      orient: 'horizontal',
      left: 'center',
      bottom: 10,
      inRange: {
        color: ['#37A2DA', '#32C5E9', '#67E0E3', '#91F2DE', '#FFDB5C', '#FF9F7F'],
      }
    },
    series: [{
      name: 'Punch Card',
      type: 'heatmap',
      data: data,
      label: {
        normal: {
          show: true
        }
      },
      itemStyle: {
        emphasis: {
          shadowBlur: 10,
          shadowColor: 'rgba(0, 0, 0, 0.5)'
        }
      }
    }]
  }

  chart.setOption(option)
  return chart
}

export default class Echarts extends React.Component {

  state = {
    ec: {
      onInit: initChart
    }
  }

  render () {
    return (
      <View className='echarts'>
        <ec-canvas 
          id='mychart-dom-area' 
          canvas-id='mychart-area' 
          echarts={echarts} 
          ec={this.state.ec}
        />
      </View>
    )
  }
}
```
