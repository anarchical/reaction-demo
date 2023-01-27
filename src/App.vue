<template>
  <ConfigView ref="configViewRef" @getConfig="getConfig" @begin="begin"/>
  <GraphView ref="graphRef"
             :color="color"
             :size="size"
             :interval="interval"
             :times="times"
             :startTime="startTime"
             v-if="showGraph"
             @loadData="loadData"/>
  <ResultView v-if="showResult"
              :data="resultData"
              :size="size"
              :startTime="startTime"
              :endTime="endTime"
              :interval="interval"
              :times="times"
              :name="name"
              @initConfig="initConfig"/>
</template>

<script setup lang="ts">
import GraphView from "./components/GraphView.vue";
import ConfigView from "./components/ConfigView.vue";
import {ref} from "vue";
import ResultView from "./components/ResultView.vue";


const color = ref()
const interval = ref()
const times = ref()
const size = ref()
const name = ref()


function getConfig(config: any) {
  color.value = config.color;
  size.value = config.size;
  interval.value = config.interval;
  times.value = config.times
  name.value = config.name
}

const showGraph = ref(false);

const graphRef = ref(null)
const showResult = ref(false);
const configViewRef = ref(null)
const startTime = ref(new Date().getTime());
const endTime = ref(new Date().getTime());

function begin() {
  console.log("测试人员：" + name.value)
  showGraph.value = true;
  startTime.value = new Date().getTime();
}

const resultData = ref([]);

function loadData(data: any) {
  showGraph.value = false
  showResult.value = true
  resultData.value = data
  endTime.value = new Date().getTime();

}

function initConfig() {
  showResult.value = false
  if (configViewRef.value != null) {
    (configViewRef.value as any).init()
  }

}

</script>

