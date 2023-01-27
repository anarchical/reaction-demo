<template>
  <el-form ref="formRef" :model="form" label-width="170px" style="width: 500px" v-if="showForm">
    <el-form-item label="图形大小">
      <el-slider max="500" v-model="form.size"/>
    </el-form-item>
    <el-form-item label="图形颜色">
      <el-input v-model="form.color"/>
    </el-form-item>
    <el-form-item label="测试间隔（单位：秒）">
      <el-input-number v-model="form.interval" :min="0" :max="10" step="0.5"/>
    </el-form-item>
    <el-form-item label="测试次数">
      <el-input-number v-model="form.times" :min="5" :max="200"/>
    </el-form-item>
    <el-form-item label="患者姓名" prop="name" :rules="{
        required: true,
        message: '患者姓名不能为空',
        trigger: 'blur',
      }">
      <el-input v-model="form.name"/>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit(formRef)">创建测试</el-button>
    </el-form-item>

  </el-form>
  <div style="height: 500px;width: 500px" v-if="showBegin">
    <h2 v-if="showForm">预览图形</h2>
    <h2 v-if="!showForm">点击图形开始测试</h2>
    <el-button circle :color="form.color"
               :style="{width: translate(form.size), height:translate(form.size)}"
               @click="begin"
               :disabled="showForm"
    />
  </div>
</template>

<script setup lang="ts">
import {reactive, ref} from 'vue'
import type {FormInstance} from 'element-plus'

const formRef = ref<FormInstance>()
const emit = defineEmits(['getConfig', 'begin'])

const form = reactive({
  color: 'red',
  size: 100,
  interval: 1,
  times: 5,
  name: ''
})
const translate = (size: number) => {
  return size + 'px';
}


const showForm = ref(true);
const onSubmit = (formEl: FormInstance | undefined) => {
  if (!formEl) return
  formEl.validate((valid) => {
    if (valid) {
      emit('getConfig', form)
      showForm.value = false
    } else {
      return false
    }
  })


}

const showBegin = ref(true);
const begin = () => {
  showBegin.value = false
  emit('begin', form)
}

function init(): void {
  showForm.value = true
  showBegin.value = true
  form.name=''
}

defineExpose({
  init
})


</script>

<style scoped>

</style>
