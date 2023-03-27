<template>
  <div class="zp-charts-container">
    <div class="charts-canvas-container" :ref="chartRef" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted, getCurrentInstance } from 'vue'
import { uuid } from '../util'
import { useAutoresize } from '../util/autoResize'
import * as echarts from 'echarts'
import 'echarts-liquidfill/src/liquidFill.js' //水球图
import 'echarts-gl' // 3D地图

const instance = getCurrentInstance()

const resize = useAutoresize()

const id = uuid(false)
const chartRef = ref(`chart-${id}`)

let chart: any

let autoSwitchIntelval: any = []

const props = defineProps({
  option: {
    type: Object,
    default: null
  },
  config: {
    type: Object,
    default: () => { }
  }
})

interface config {
  callback: Function | null
  autoSwitch: Array<any> | null
  mapjson: string | null
}
const defaultConfig: config = {
  /**
   * 渲染完成后回调
   */
  callback: null,
  /**
   * 自动切换项目
   */
  autoSwitch: null,
  /**
   * 地图json数据
   */
  mapjson: null
}

const mergedConfig = computed(() => {
  return { ...defaultConfig, ...props.config }
})

watch(
  () => props.option,
  (value) => {
    if (chart) {
      let option = {}
      if (value) option = value
      chart.setOption(option, true)
    }
  }
)

onMounted(() => {
  resize.then((data: any) => {
    data.autoResizeMixinInit(instance?.refs[chartRef.value], afterAutoResizeMixinInit, onResize)
  })
})

onUnmounted(() => {
  clearAutoSwitchIntelval()
})

const afterAutoResizeMixinInit = () => {
  initChart()
}
const initChart = () => {
  const { option } = props
  const { autoSwitch, callback, mapjson } = mergedConfig.value
  chart = echarts.init(instance?.refs[chartRef.value] as unknown as HTMLDivElement)

  if (!option) return

  mapjson && echarts.registerMap('mapjson', mapjson)

  chart.setOption(option)

  if (autoSwitch) {
    autoSwitchInit()
    chart.on('mouseover', (params: any) => {
      clearAutoSwitchIntelval()
    })

    chart.on('mouseout', (params: any) => {
      autoSwitchInit()
    })
  }

  callback && callback(chart)
}

const onResize = () => {
  if (!chart) return
  chart.resize()
}

const autoSwitchInit = () => {
  const { option } = props
  const { autoSwitch } = mergedConfig.value
  if (!autoSwitch) {
    return
  }
  let indexs = new Array(autoSwitch.length).fill(0)
  autoSwitch.map((item: any, index: number) => {
    const { seriesIndex, timeout } = item

    let interval = setInterval(() => {
      const length = option.series[seriesIndex].data.length
      switchIndex(seriesIndex, indexs[index] % length)
      indexs[index]++
    }, timeout)
    autoSwitchIntelval.push(interval)
  })
}

const switchIndex = (seriesIndex: number, dataIndex: number) => {
  chart.dispatchAction({ type: 'downplay', seriesIndex: seriesIndex })
  chart.dispatchAction({
    type: 'highlight',
    seriesIndex: seriesIndex,
    dataIndex: dataIndex
  })
  chart.dispatchAction({
    type: 'showTip',
    seriesIndex: seriesIndex,
    dataIndex: dataIndex
  })
}

const clearAutoSwitchIntelval = () => {
  autoSwitchIntelval.map((item: any) => {
    clearInterval(item)
  })
}
</script>

<style lang="scss" scoped>
.zp-charts-container {
  position: relative;
  width: 100%;
  height: 100%;

  .charts-canvas-container {
    width: 100%;
    height: 100%;
  }
}
</style>
