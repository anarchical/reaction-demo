<template>
  <div class="test" v-if="display" @click="clickBackground">
    <el-button v-if="display"
               circle
               :color="props.color"
               :style="{
             width:props.size+'px',
             height:props.size+'px',
             marginLeft:x,
             marginTop:y}"
               @click="clickGraph"/>
  </div>


</template>

<script setup lang="ts">
import {ref} from 'vue'

const props = defineProps<{ interval: number, color: string, size: number, times: number, startTime: number }>()

const width = document.body.clientWidth;
const height = document.body.clientHeight;

let display = ref(true)
let sumTimes = 0
let clicks = ref(0)
const min = ref()
const max = ref()
const sd = ref()
const averRespTime = ref()
const totalRespTime = ref()
const data = {
  'clicks': clicks,
  'min': min,
  'max': max,
  'sd': sd,
  'averRespTime': averRespTime,
  'totalRespTime': totalRespTime,
}
const emit = defineEmits(['loadData'])

const x = ref(getRandom(0, width - props.size) + 'px')
const y = ref(getRandom(0, height - props.size) + 'px')

let lastClickTime = new Date().getTime();
let temp: number[] = [];

const clickGraph = () => {
  if (sumTimes == 0) {
    min.value = new Date().getTime() - props.startTime;
    max.value = new Date().getTime() - props.startTime;
    temp.push(new Date().getTime() - props.startTime)
  } else {
    getMin(new Date().getTime() - lastClickTime);
    getMax(new Date().getTime() - lastClickTime);
    temp.push(new Date().getTime() - lastClickTime)
  }
  refreshPosition()
  display.value = false;
  sumTimes++;
  const timer = setTimeout(() => {
    display.value = true;
    lastClickTime = new Date().getTime();
  }, 1000 * props.interval)

  if (sumTimes == props.times) {
    clearTimeout(timer)
    sumTimes = 0
    data.clicks.value += 1
    calculate()
    emit('loadData', data);
    temp = [];
  }
}

function calculate() {
  console.log("反应时间列表：" + temp)
  totalRespTime.value = 0
  temp.forEach((value, index, array) => {
    totalRespTime.value += value
  })

  averRespTime.value = totalRespTime.value / temp.length
  console.log("平均反应时间：" + averRespTime.value)

  let variance: number[] = []
  temp.forEach((value, index, array) => {
    variance.push(Math.pow((value - averRespTime.value), 2))
  })
  console.log("反应时间方差：" + variance)

  let meanVariance: number;
  let sumVariance: number = 0;
  variance.forEach((value, index, array) => {
    sumVariance += value;
  })

  meanVariance = sumVariance / variance.length
  console.log("反应时间平均方差：" + meanVariance)

  sd.value = Math.sqrt(meanVariance)
  console.log("反应时间标准差：" + sd.value)

}

function clickBackground() {
  ++clicks.value
}

function refreshPosition() {

  const size = props.size
  x.value = getRandom(0, width - size) + 'px';
  y.value = getRandom(0, height - size) + 'px';
}

function getRandom(min: number, max: number): number {
  return Math.round(Math.random() * (max - min) + min)
}

function getMax(time: number) {
  if (time > max.value) {
    max.value = time
  }
}

function getMin(time: number) {
  if (time < min.value) {
    min.value = time
  }
}


</script>

<style scoped>
.test {

  transform: translate3d(0, 0, 0) !important;
  -ms-transform: translate3d(0, 0, 0) !important;

  position: fixed !important;
  top: 0 !important;
  bottom: 0 !important;
  left: 0 !important;
  right: 0 !important;

  text-align: left;
}

</style>

