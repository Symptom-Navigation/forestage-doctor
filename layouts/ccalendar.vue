<!-- 日历实现（待做功能：实现每天预约数显示） -->
<template>
  <!-- 数据一览实现 -->

  <!-- 日历实现 -->
  <div class="calendar">
    <el-calendar v-model="value" />
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue";
const value = ref(new Date());
</script>
<style>
.calendar {
  margin-top: 5%;
  width: 50%;
  float: right;
}
</style>
