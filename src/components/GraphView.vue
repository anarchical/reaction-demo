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

const data = {
  'clicks': clicks,
  'min': min,
  'max': max,
}
const emit = defineEmits(['loadData'])

const x = ref(getRandom(0, width - props.size) + 'px')
const y = ref(getRandom(0, height - props.size) + 'px')

let lastClickTime = new Date().getTime();

const clickGraph = () => {
  if (sumTimes == 0) {
    min.value = new Date().getTime() - props.startTime;
    max.value = new Date().getTime() - props.startTime;
  } else {
    getMin(new Date().getTime() - lastClickTime);
    getMax(new Date().getTime() - lastClickTime);

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
    emit('loadData', data)
  }
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

