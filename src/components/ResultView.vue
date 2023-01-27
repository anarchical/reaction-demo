<template>
  <div style="height: 100px" v-if="!showData">
    <h2>测试结束</h2>
    <el-button type="info" @click="show">查看数据</el-button>
  </div>
  <div v-if="showData">
    <el-button-group style="float:right;margin-bottom: 10px">
      <el-button type="primary" @click="exportData">导出测试数据</el-button>
      <el-button type="danger" @click="close">关闭</el-button>
    </el-button-group>
    <div/>
    <el-table :data="tableData" border style="width: 1500px;margin-top: 10px">
      <el-table-column prop="图形大小" label="图形大小"/>
      <el-table-column prop="测试间隔（秒）" label="测试间隔（秒）"/>
      <el-table-column prop="测试总数" label="测试总数"/>
      <el-table-column prop="点击总数" label="点击总数"/>
      <el-table-column prop="正确率" label="正确率"/>
      <el-table-column prop="最小反应时间（秒）" label="最小反应时间（秒）"/>
      <el-table-column prop="最大反应时间（秒）" label="最大反应时间（秒）"/>
      <el-table-column prop="平均反应时间（秒）" label="平均反应时间（秒）"/>
      <el-table-column prop="标准差反应时间（秒）" label="标准差反应时间（秒）"/>
      <el-table-column prop="反应总时间（秒）" label="反应总时间（秒）"/>
      <el-table-column prop="测试耗时（秒）" label="测试耗时（秒）"/>
      <el-table-column prop="测试日期" label="测试日期"/>
      <el-table-column prop="患者姓名" label="患者姓名"/>
    </el-table>
  </div>


</template>

<script setup lang="ts">
import {ref} from 'vue'
import {ExportToCsv} from 'ts-export-to-csv';

const props = defineProps<{
  data: any;
  size: number,
  interval: number,
  times: number,
  startTime: number,
  endTime: number,
  name: string
}>()

const tableData = ref([{
  '图形大小': props.size,
  '测试间隔（秒）': props.interval,
  '测试总数': props.times,
  '点击总数': props.data.clicks,
  '正确率': ((props.times * 100 / props.data.clicks as any).toFixed(2)) + '%',
  '最小反应时间（秒）': (props.data.min / 1000).toFixed(2),
  '最大反应时间（秒）': (props.data.max / 1000).toFixed(2),
  '平均反应时间（秒）': (props.data.averRespTime / 1000).toFixed(2),
  '标准差反应时间（秒）': (props.data.sd / 1000).toFixed(2),
  '反应总时间（秒）': (props.data.totalRespTime / 1000).toFixed(2),
  '测试耗时（秒）': ((props.endTime - props.startTime) / 1000).toFixed(2),
  '测试日期': timestampToTime(props.startTime),
  '患者姓名': props.name,


}])
const showData = ref(false)
const emit = defineEmits(['initConfig'])

const show = () => {
  showData.value = true
}


function exportData() {
  const options = {
    filename: props.name + timestampToTime(props.startTime),
    fieldSeparator: ',',
    quoteStrings: '"',
    decimalSeparator: '.',
    showLabels: true,
    showTitle: true,
    useTextFile: false,
    title: props.name + ' 反应测试报告',
    useBom: true,
    useKeysAsHeaders: true,
  };
  const csvExporter = new ExportToCsv(options);
  csvExporter.generateCsv(tableData.value);
}

function close() {
  tableData.value = []
  showData.value = false
  emit('initConfig')
}

function timestampToTime(timestamp: number) {
  const date = new Date(timestamp);
  const Y = date.getFullYear() + '-';
  const M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
  const D = date.getDate() + ' ';
  const h = date.getHours() + ':';
  const m = date.getMinutes() + ':';
  const s = date.getSeconds();
  return Y + M + D + h + m + s;
}

</script>

<style scoped>

</style>
