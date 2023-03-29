<template>
  <main>
    <zp-charts :option="option" :config="{ beforeCallback: beforeCallback }" />
  </main>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import top from '@/assets/image/立方体@2x.png'

const option = computed(() => {
  const xData = ['1001', '1002', '1003', '1004', '1005', '1006', '1007']
  const yData = [100, 200, 300, 400, 300, 200, 100]

  return {
    backgroundColor: '#012366',
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      },
      formatter: function (params: any) {
        const item = params[1]
        return item.name + ' : ' + item.value
      }
    },
    grid: {
      left: '10%',
      right: '10%',
      top: '15%',
      bottom: '10%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: xData,
      axisLine: {
        show: true,
        lineStyle: {
          width: 1,
          color: 'rgba(108, 128, 151, 1)'
        }
      },
      axisTick: {
        show: false
      },
      axisLabel: {
        fontSize: 14,
        color: 'rgba(255, 255, 255, 0.8)'
      }
    },
    yAxis: {
      type: 'value',
      axisLine: {
        show: false
      },
      splitLine: {
        show: true,
        lineStyle: {
          type: 'dashed',
          color: 'rgba(255, 255, 255, 0.1)'
        }
      },
      axisTick: {
        show: false
      },
      axisLabel: {
        fontSize: 14,
        color: 'rgba(255, 255, 255, 0.8)'
      }
      // boundaryGap: ['20%', '20%'],
    },
    series: [
      {
        type: 'custom',
        renderItem: (params: any, api: any) => {
          const location = api.coord([api.value(0), api.value(1)])
          return {
            type: 'group',
            children: [
              {
                type: 'CubeLeft',
                shape: {
                  api,
                  xValue: api.value(0),
                  yValue: api.value(1),
                  x: location[0],
                  y: location[1],
                  xAxisPoint: api.coord([api.value(0), 0])
                },
                style: {
                  fill: 'rgba(27, 126, 242, 1)'
                }
              },
              {
                type: 'CubeRight',
                shape: {
                  api,
                  xValue: api.value(0),
                  yValue: api.value(1),
                  x: location[0],
                  y: location[1],
                  xAxisPoint: api.coord([api.value(0), 0])
                },
                style: {
                  fill: {
                    type: 'linear',
                    x: 0,
                    y: 0,
                    x2: 0,
                    y2: 1,
                    colorStops: [
                      {
                        offset: 0,
                        color: 'rgba(27, 126, 242, 1)' // 0% 处的颜色
                      },
                      {
                        offset: 1,
                        color: 'rgba(27, 126, 242, 0)' //100% 处的颜色
                      }
                    ],
                    global: false // 缺省为 false
                  }
                }
              },
              {
                type: 'image',

                style: {
                  image: top,
                  x: location[0] - 14,
                  y: location[1] - 28,
                  width: 28,
                  height: 29
                }
              }
            ]
          }
        },
        data: yData
      },
      {
        type: 'bar',
        label: {
          normal: {
            show: false
          }
        },
        itemStyle: {
          color: 'transparent'
        },
        tooltip: {},
        data: yData
      }
    ]
  }
})

const beforeCallback = ({ echarts, chart }: any) => {
  const offsetX = 14
  const offsetY = 10
  // console.log(echarts, chart)
  // 绘制左侧面
  const CubeLeft = echarts.graphic.extendShape({
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function (ctx: any, shape: any) {
      // 会canvas的应该都能看得懂，shape是从custom传入的
      const xAxisPoint = shape.xAxisPoint
      // console.log(shape);
      const c0 = [shape.x, shape.y]
      const c1 = [shape.x - offsetX, shape.y - offsetY]
      const c2 = [xAxisPoint[0] - offsetX, xAxisPoint[1] - offsetY]
      const c3 = [xAxisPoint[0], xAxisPoint[1]]
      ctx
        .moveTo(c0[0], c0[1])
        .lineTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .closePath()
    }
  })
  // 绘制右侧面
  const CubeRight = echarts.graphic.extendShape({
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function (ctx: any, shape: any) {
      const xAxisPoint = shape.xAxisPoint
      const c1 = [shape.x, shape.y]
      const c2 = [xAxisPoint[0], xAxisPoint[1]]
      const c3 = [xAxisPoint[0] + offsetX, xAxisPoint[1] - offsetY]
      const c4 = [shape.x + offsetX, shape.y - offsetY]
      ctx
        .moveTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .lineTo(c4[0], c4[1])
        .closePath()
    }
  })
  // 注册三个面图形
  echarts.graphic.registerShape('CubeLeft', CubeLeft)
  echarts.graphic.registerShape('CubeRight', CubeRight)
}
</script>

<style>
main {
  height: 100vh;
  width: 100vw;
}
</style>
