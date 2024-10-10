<template>
  <div class="main">
    <el-row :gutter="20">
      <el-col :span="16">
        <!--使用el-row el-col行列布局，页面分为24栏，gutter 属性来指定列之间的间距-->
        <el-row :gutter="20" style="padding-bottom: 10px; height: 50%">
          <el-col :span="12">
            <div class="ve-card ve_card1" @click="goToIndex">
              <el-icon> <User /></el-icon>
              <div>
                <p>待处理预约</p>
                <span>15</span>
              </div>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="ve-card ve_card2">
              <el-icon><UserFilled /></el-icon>
              <div>
                <p>待面诊预约</p>
                <span>0</span>
              </div>
            </div>
          </el-col>
        </el-row>
        <el-row :gutter="20" style="padding-top: 10px; height: 50%">
          <el-col :span="12">
            <div class="ve-card ve_card3">
              <el-icon><Notebook /></el-icon>
              <div>
                <p>待反馈预约</p>
                <span>0</span>
              </div>
            </div>
          </el-col>
          <el-col :span="12">
            <div class="ve-card ve_card4" @click="goTocom">
              <el-icon><Document /></el-icon>
              <div>
                <p>未回复信息</p>
                <span>1</span>
              </div>
            </div>
          </el-col>
        </el-row>
      </el-col>
      <el-col :span="8">
        <el-calendar class="ve_calendar"></el-calendar>
      </el-col>
    </el-row>
  </div>
</template>
<style scoped>
.ve_calendar {
  border-radius: 10px;
}
.ve-card {
  border-radius: 10px;
  height: 100%;
  display: flex;
  align-items: center;
  transition: all 500ms;
  color: #fff;
}
.ve-card i {
  font-size: 100px;
  margin: 0 20px;
}
.ve-card div {
  flex: 1;
  padding-right: 60px;
}
.ve-card div p {
  margin: 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.ve-card div span {
  font-size: 60px;
  font-weight: bold;
}
.ve-card:hover {
  box-shadow: 3px 3px 6px 1px rgba(0, 0, 0, 0.2);
  background: rgb(150, 150, 150);
}
.ve_card1 {
  background: #409eff;
}
.ve_card2 {
  background: #67c23a;
}
.ve_card3 {
  background: #e6a23c;
}
.ve_card4 {
  background: #f56c6c;
}
.main {
  margin-top: 20px;
}
</style>
<script setup>
import { useRouter } from "vue-router";

const router = useRouter();

function goToIndex() {
  router.push("/mybooking");
}
function goTocom() {
  router.push("/communicate");
}
</script>
