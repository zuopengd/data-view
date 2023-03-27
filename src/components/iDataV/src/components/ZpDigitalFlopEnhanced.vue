<template>
  <div class="zp-digital-flop-enhanced" :style="mergedConfig.style" :ref="iref">{{ num }}</div>
</template>
<script lang="ts" setup>
import { reactive, computed, watch, onMounted } from 'vue'
import { numFormat } from '../util'
// 导入动画库
import gsap from 'gsap'

const props = defineProps({
  /**
   * 数字
   */
  num: {
    type: Number,
    default: 0
  },
  config: {
    type: Object,
    default: () => {}
  }
})

const defaultConfig = {
  /**
   * 小数位数
   */
  decimal: 0,
  /**
   * 千位分割 true | false
   */
  kilobit: true,
  /**
   * 自定义样式
   */
  style: () => {
    return {}
  }
}

const iref = 'digital-flop-enhanced'

const data = reactive({
  num: 0
})

const mergedConfig = computed(() => {
  return { ...defaultConfig, ...props.config }
})

const num = computed(() => {
  const { decimal, kilobit } = mergedConfig.value
  let n = data.num
  if (decimal >= 0) n = Number(n.toFixed(decimal))
  return kilobit ? numFormat(n) : n
})

watch(
  () => props.num,
  (val) => {
    animation(val)
  }
)

onMounted(() => {
  animation(props.num)
})

const animation = (value: number) => {
  gsap.to(data, {
    num: value,
    duration: 0.5, // 动画时间 秒
    // ease: "power1.inOut", // 动画速率
    // repeat: 0, //重复次数
    // yoyo: false, // 往返运动
    // delay:2,//延迟2秒运动
    onStart() {
      // console.log('动画开始')
    },
    onComplete() {
      // console.log('动画完成')
    },
    onUpdate() {
      // console.log('属性更新')
    }
  })
}
</script>
<style lang="scss" scoped>
.zp-digital-flop-enhanced {
  display: inline-block;
}
</style>
