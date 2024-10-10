<template>
  <NuxtLayout name="menu">
    <div class="main">
      <div class="main-left">
        <div class="l-top">
          <div class="l">
            <h1>待处理预约</h1>
            <span>请注意预约时间</span>
          </div>
          <div class="r">
            <span>0-{{ totalTasks }}完成</span>
            <div class="demo-progress">
              <el-progress :percentage="progressPercentage" />
            </div>
          </div>
          <div style="clear: both"></div>
        </div>
        <div class="l-mid">
          <!-- 下拉菜单实现 -->
          <client-only>
            <el-dropdown>
              <el-button type="primary" plain>
                <span class="el-dropdown-link">
                  排序方式
                  <el-icon class="el-icon--right">
                    <arrow-down />
                  </el-icon>
                </span>
              </el-button>
              <template #dropdown>
                <el-dropdown-menu>
                  <el-dropdown-item>按预约时间排序</el-dropdown-item>
                  <el-dropdown-item>按面诊时间排序</el-dropdown-item>
                </el-dropdown-menu>
              </template>
            </el-dropdown>
          </client-only>
          <!-- 搜索框实现 -->
          <el-input placeholder="请输入内容" clearable @clear="getUserList">
            <template #append>
              <el-button @click="getUserList">
                <el-icon><Search /></el-icon>
              </el-button>
            </template>
          </el-input>
        </div>
        <div class="l-bottom">
          <!-- 分页数据模拟及实现 -->
          <div>
            <el-table :data="tableData" style="width: 100%">
              <el-table-column prop="id" label="id" width="160" />
              <el-table-column prop="data" label="姓名" width="160" />
              <el-table-column prop="time" label="预约时间" width="160" />
              <el-table-column prop="might" label="推测疾病方向" width="160" />

              <el-table-column label="操作" width="200px">
                <template v-slot="scope">
                  <!-- 修改按钮 -->
                  <el-button
                    @click="dialogVisible1 = true"
                    type="primary"
                    size="mini"
                    ><el-icon><edit /></el-icon
                  ></el-button>
                  <!-- 删除按钮 -->
                  <el-button type="danger" size="mini"
                    ><el-icon><Delete /></el-icon
                  ></el-button>
                  <!-- 模型反馈按钮 -->
                  <el-tooltip
                    effect="dark"
                    content="模型反馈"
                    placement="top"
                    :enterable="false"
                  >
                    <el-button
                      type="warning"
                      size="mini"
                      @click="dialogVisible = true"
                      ><el-icon><Setting /></el-icon
                    ></el-button>
                  </el-tooltip>
                </template>
              </el-table-column>
            </el-table>
            <el-pagination
              :current-page="searchData.current"
              :page-size="searchData.limit"
              :total="total"
              :pager-count="6"
              style="text-align: center; margin-top: 20px"
              layout="jumper, prev, pager, next, total"
              @current-change="getData"
            />
          </div>
        </div>
      </div>
      <div class="main-right">
        <div class="r-top">
          <div class="r-t-l">
            <h1>处理历史</h1>
          </div>
          <div class="r-t-r">
            <el-button>
              <el-icon style="font-size: 35px"><MoreFilled /></el-icon>
            </el-button>
          </div>
          <div style="clear: both"></div>
        </div>
        <div class="r-bottom"><NuxtLayout name="history"> </NuxtLayout></div>
      </div>
      <div style="clear: both"></div>
    </div>
    <!-- 提示页面 -->
    <el-dialog v-model="dialogVisible1" title="提示" width="500">
      <span>确定设置为待面诊？</span>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="feedbackDialogVisible = false">取消</el-button>
          <el-button type="primary" @click="dialogVisible1 = false">
            确定
          </el-button>
        </div>
      </template>
    </el-dialog>
    <!-- 反馈界面 -->
    <el-dialog v-model="dialogVisible" title="反馈表单" width="500">
      <el-form :model="form">
        <el-form-item label="用户名">
          <el-input v-model="form.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="导诊结果">
          <el-input v-model="form.result" disabled></el-input>
        </el-form-item>
        <el-form-item label="反馈">
          <el-input type="textarea" v-model="form.feedback"></el-input>
        </el-form-item>
      </el-form>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="dialogVisible = false">取消</el-button>
          <el-button type="primary" @click="dialogVisible = false"
            >提交</el-button
          >
        </div>
      </template>
    </el-dialog>
  </NuxtLayout>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";
import {
  Search,
  Setting,
  Edit,
  Delete,
  MoreFilled,
} from "@element-plus/icons-vue";
import Sortable from "sortablejs";

const dialogVisible = ref(false);
const dialogVisible1 = ref(false);
const feedbackDialogVisible = ref(false);
const feedbackDialogVisible1 = ref(false);
const feedbackForm = reactive({
  username: "",
  diagnosisResult: "",
  feedback: "",
});
const form = reactive({
  username: "姓名2", // 示例用户名
  result: "患者头痛、单侧痛、且有发热，推测为偏头痛，建议为神经内科", // 示例导诊结果
  feedback: "如导诊有问题，可在此反馈", // 示例反馈内容
});
const tableData = ref([]);
const total = ref(0);
const searchData = reactive({
  current: 1,
  limit: 10,
});

const completedTasks = 1;
const totalTasks = ref(0);
const progressPercentage = ref(0);

async function fetchAppointmentData() {
  try {
    const response = await axios.get(
      "http://localhost:8080/appointments/doctor/pending",
      {
        headers: {
          Authorization: `Bearer {你的JWT令牌}`,
        },
      }
    );
    const appointments = response.data.map((appointment) => ({
      id: appointment.id,
      data: appointment.user.username,
      time: appointment.appointmentData,
      might: appointment.description,
    }));
    tableData.value = appointments;
    total.value = tableData.value.length;
    totalTasks.value = total.value;
    progressPercentage.value = (completedTasks / totalTasks.value) * 100;
    pageQuery(searchData.current, searchData.limit);
  } catch (error) {
    console.error("Error fetching appointment data:", error);
  }
}

function pageQuery(current, limit) {
  let begin = current * limit - limit;
  let end = current * limit;
  tableData.value = tableData.value.slice(begin, end);
}

function getData(val = 1) {
  searchData.current = val;
  fetchAppointmentData();
}

onMounted(() => {
  getData();
  rowDrop();
});

const rowDrop = () => {
  const el = document.querySelector(".el-table__body tbody");
  Sortable.create(el, {
    animation: 150,
    ghostClass: "ghostClass",
    chosenClass: "chosenClass",
    dragClass: "dragClass",
    handle: ".el-table__row",
    draggable: ".el-table__row",
    onEnd({ newIndex, oldIndex }) {
      if (tableData.value.length < 2) return;
      const movedItem = tableData.value.splice(oldIndex, 1)[0];
      tableData.value.splice(newIndex, 0, movedItem);
      // Add your API call here to save the new order
    },
  });
};
</script>

<style scoped>
* {
  margin: 0;
  border: 0;
}
.main-left {
  width: 70%;
  padding-right: 15px;
  border-right: 1px solid #0c0c0c80;
}
/* 下拉菜单样式 */
.example-showcase .el-dropdown-link {
  cursor: pointer;
  color: var(--el-color-primary);
  display: flex;
  align-items: center;
}
.l-bottom {
  margin-top: 30px;
}
.l-top h1 {
  font-size: 30px;
  color: #3b4c68;
}
.l-top {
  margin-left: 20px;
}
.l-top .l span {
  color: #b7c1d1;
}
.l-top .l {
  float: left;
}
.l-top .r {
  width: 200px;
  float: right;
  color: #4099ff;
}
.l-mid {
  margin-top: 20px;
}
.el-input {
  width: 400px;
  float: right;
}
/* 进度条样式 */
.demo-progress .el-progress--line {
  margin-bottom: 15px;
  max-width: 600px;
}
.main-left {
  float: left;
}
.main-right {
  float: left;
  margin-left: 5%;
  width: 20%;
}
.r-top h1 {
  font-size: 30px;
  color: #3b4c68;
}
.r-t-l {
  float: left;
}
.r-t-r {
  float: right;
}
.r-bottom {
  margin-top: 10%;
}
</style>
