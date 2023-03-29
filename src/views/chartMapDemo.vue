<template>
  <main>
    <!-- <zp-digital-flop-enhanced
      :num="123234.1111"
      :config="{ decimal: 2, kilobit: false, style: { fontSize: '33px' } }"
    /> -->
    <zp-charts
      :option="option"
      :config="{ mapjson: map, autoSwitch: [{ seriesIndex: 0, timeout: 2000 }] }"
    />
  </main>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import map from '@/assets/json/510700_full.json?raw'
import * as echarts from 'echarts'
import sj from '@/assets/image/a.png?url'

let sandianlist: any = []
let data: any = [
  { name: '涪城区', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '游仙区', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '安州区', value: (Math.random() * 1000 + 100).toFixed(0) },
  // { name: '三台县', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '盐亭县', value: (Math.random() * 1000 + 100).toFixed(0) }
  // { name: '梓潼县', value: (Math.random() * 1000 + 100).toFixed(0) },
  // { name: '平武县', value: (Math.random() * 1000 + 100).toFixed(0) },
  // { name: '北川羌族自治县', value: (Math.random() * 1000 + 100).toFixed(0) },
  // { name: '江油市', value: (Math.random() * 1000 + 100).toFixed(0) }
]

const option = computed(() => {
  return init()
})

const sandian = () => {
  let mapFeatures = JSON.parse(map).features

  sandianlist = data.map((item: any) => {
    let name = item.name
    let value = [item.value]

    value = [
      ...mapFeatures.find((it: any) => {
        return it.properties.name === name
      }).properties.centroid,
      value
    ]

    return { name, value }
  })
}

const init = () => {
  // backgroundColor: '#131C38',
  sandian()
  // console.log(sandianlist)
  return {
    // backgroundColor: '#062343',
    tooltip: {
      show: true,
      enterable: true, //鼠标可进入浮层内
      // triggerOn: "click", // 点击触发
      backgroundColor: 'transparent',
      extraCssText: 'box-shadow:none;border: 0px solid white;min-width: 138px;',
      textStyle: {},
      formatter: function (params: any) {
        return `<div class="tooltip">
          <div>${params.name}</div>
          <div><span>求职数：</span><span>120</span><span>个</span></div>
          <div><span>岗位数：</span><span>300</span><span>个</span></div>
          </div>
            `
      }
    },
    geo: [
      // 高亮虚影层
      {
        map: 'mapjson',
        aspectScale: 0.96,
        roam: false, // 是否允许缩放
        zoom: 1, // 默认显示级别
        silent: true,
        layoutSize: '80%',
        layoutCenter: ['50%', '50%'],
        itemStyle: {
          normal: {
            borderColor: 'rgba(68, 173, 254,1)',
            borderWidth: 6,
            shadowColor: 'rgba(68, 173, 254,1)',
            shadowOffsetY: 0,
            shadowBlur: 4
          }
        },
        zlevel: -1
      },
      // 底部重影层
      {
        map: 'mapjson',
        aspectScale: 0.96,
        roam: false, // 是否允许缩放
        zoom: 1, // 默认显示级别
        silent: true,
        layoutSize: '80%',
        layoutCenter: ['50%', '51.2%'],
        itemStyle: {
          borderColor: 'rgba(68, 173, 254,.2)',
          borderWidth: 1,
          areaColor: '#094987'
        },
        zlevel: -2
      }
    ],
    series: [
      // map
      {
        type: 'map',
        map: 'mapjson',
        aspectScale: 0.96,
        roam: false, // 是否允许缩放
        zoom: 1, // 默认显示级别
        layoutSize: '80%',
        layoutCenter: ['50%', '50%'],
        itemStyle: {
          normal: {
            label: {
              show: false,
              color: '#fff'
            },
            color: '#fff',
            borderColor: '#32CBE0',
            borderWidth: 1.5,
            areaColor: {
              type: 'linear-gradient',
              x: 0,
              y: 1000,
              x2: 0,
              y2: 0,
              colorStops: [
                {
                  offset: 0.5,
                  color: '#0D59C1' // 0% 处的颜色
                },
                {
                  offset: 1,
                  color: '#53C9C7' // 100% 处的颜色
                }
              ],
              global: true // 缺省为 false
            }
          },
          emphasis: {
            label: {
              show: false,
              color: '#fff'
            },
            borderWidth: 3,
            borderColor: 'rgba(255, 230, 175,0.8)',
            shadowColor: 'rgba(255, 230, 175,0.5)',
            shadowBlur: 30,
            textStyle: {
              color: '#fff',
              fontSize: 12,
              backgroundColor: 'transparent'
            },
            areaColor: {
              x: 0,
              y: 0,
              x2: 0,
              y2: 1,
              type: 'linear',
              global: false,
              colorStops: [
                {
                  offset: 0,
                  color: '#1cfbfe'
                },

                {
                  offset: 1,
                  color: '#3348e7'
                }
              ]
            }
          }
        },
        data: sandianlist

        // emphasis: {
        //   itemStyle: {
        //     areaColor: '#0160AD'
        //   },
        //   label: {
        //     show: 0,
        //     color: '#fff'
        //   }
        // }
      },
      {
        name: 'Top 5',
        type: 'effectScatter',
        coordinateSystem: 'geo',
        data: sandianlist,
        symbolSize: [15, 10],
        showEffectOn: 'render',
        rippleEffect: {
          brushType: 'stroke'
        },
        hoverAnimation: true,
        label: {
          normal: {
            formatter: '{b}',
            position: 'right',
            show: true
          }
        },
        itemStyle: {
          normal: {
            color: '#F8E71C',
            shadowBlur: 20,
            shadowColor: '#F8E71C'
          }
        },
        zlevel: 1
      },
      {
        type: 'custom',
        coordinateSystem: 'geo',
        renderItem: function (params: any, api: any) {
          //具体实现自定义图标的方法
          return {
            type: 'image',
            style: {
              image: sj, // 自定义的图片地址
              x: api.coord(sandianlist[params.dataIndex].value)[0] - 3, // 数据的设置
              y: api.coord(sandianlist[params.dataIndex].value)[1] - 66
            }
          }
        },
        zlevel: 2,
        data: sandianlist
      }
    ]
  }
}
</script>

<style>
main {
  height: 100vh;
  width: 100vw;
}

.tooltip {
  color: #fff;
  font-size: 16px;
  font-weight: 700;
  /* background-color: red; */
}

.tooltip div {
  background-repeat: no-repeat;
  background-size: 100% 100%;
}

.tooltip div:first-child {
  font-size: 19px;
  height: 34.69px;
  background-image: url('@/assets/image/b.png');
  color: rgba(68, 201, 222, 1);
  line-height: 42px;
  padding-left: 18px;
  padding-right: 12px;
}

.tooltip div:nth-child(2),
.tooltip div:nth-child(3) {
  height: 33px;
  background-image: url('@/assets/image/c.png');
  margin-top: 1px;
  line-height: 33px;
  position: relative;
  padding-left: 18px;
}

.tooltip div:nth-child(2)::after,
.tooltip div:nth-child(3)::after {
  content: '';
  position: absolute;
  width: 0px;
  height: 0px;
  border: 5px solid transparent;
  /*以下四个样式对应四种三角形，任选其一即可实现*/
  /* border-top-color:lightseagreen; */
  border-left-color: #fff;
  left: 10px;
  top: 12px;
  /* border-right-color:lightseagreen; */
  /* border-bottom-color: lightseagreen; */
}

.tooltip div span:nth-child(2) {
  color: rgba(203, 189, 0, 1);
}
</style>
