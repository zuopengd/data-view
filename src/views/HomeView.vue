<template>
  <main>
    <zp-charts :option="option" :geojson="map" :callback="callback" />
  </main>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
// import map from '@/assets/json/aaa.geojson?raw'
import map from '@/assets/json/510700_full.json?raw'
import * as echarts from 'echarts'

let sandianlist: any = []
var data: any = [
  { name: '涪城区', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '游仙区', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '安州区', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '三台县', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '盐亭县', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '梓潼县', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '平武县', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '北川羌族自治县', value: (Math.random() * 1000 + 100).toFixed(0) },
  { name: '江油市', value: (Math.random() * 1000 + 100).toFixed(0) }
]

const option = computed(() => {
  return init()
})

const callback = (chart: any) => {
  return
  var mapFeatures = echarts.getMap('geojson').geoJson.features
}
const sandian = () => {
  let mapFeatures = JSON.parse(map).features
  console.log(mapFeatures)

  sandianlist = mapFeatures.map((item: any) => {
    let value = [...item.properties.centroid]
    let name = item.properties.name

    value.push(
      data.find((it: any) => {
        return it.name == name
      }).value
    )
    // console.log(111, value)

    return { name, value }
  })
}

const init = () => {
  // backgroundColor: '#131C38',
  sandian()
  // console.log(sandianlist)
  return {
    backgroundColor: '#062343',
    geo: [
      // 高亮虚影层
      {
        map: 'geojson',
        aspectScale: 1,
        roam: false, // 是否允许缩放
        zoom: 1.2, // 默认显示级别
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
      // 实际层
      {
        map: 'geojson',
        aspectScale: 1,
        roam: false, // 是否允许缩放
        zoom: 1.2, // 默认显示级别
        // silent: true,
        layoutSize: '80%',
        layoutCenter: ['50%', '50%'],
        itemStyle: {
          borderColor: 'rgba(68, 173, 254,.2)',
          borderWidth: 2,
          areaColor: '#094987'
        },
        emphasis: {
          itemStyle: {
            areaColor: '#0160AD'
          },
          label: {
            show: 0,
            color: '#fff'
          }
        },
        zlevel: 1
      },
      // 底部重影层
      {
        map: 'geojson',
        aspectScale: 1,
        roam: false, // 是否允许缩放
        zoom: 1.2, // 默认显示级别
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
        map: 'geojson',
        aspectScale: 1,
        roam: false, // 是否允许缩放
        zoom: 1.2, // 默认显示级别
        layoutSize: '80%',
        layoutCenter: ['50%', '50%'],
        itemStyle: {
          normal: {
            borderColor: 'rgba(192,245,249,.8)',
            borderWidth: 4,
            shadowColor: '#6FFDFF',
            shadowOffsetY: 0,
            shadowBlur: 10,
            areaColor: 'rgba(29,85,139,.6)'
          }
        },
        emphasis: {
          itemStyle: {
            areaColor: '#0160AD'
          },
          label: {
            show: 0,
            color: '#fff'
          }
        }
      },
      {
        name: 'Top 5',
        type: 'effectScatter',
        coordinateSystem: 'geo',
        data: sandianlist,
        symbolSize: function (val: any) {
          // return 10
          return val[2] / 20
        },
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
            color: 'yellow',
            shadowBlur: 20,
            shadowColor: 'yellow'
          }
        },
        zlevel: 1
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
</style>
