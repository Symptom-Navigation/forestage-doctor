<template>
  <div class="common-layout">
    <el-container>
      <el-aside width="200px">
        <el-menu>
          <div class="user">
            <img src="../assets/logo.jpg" alt="" />
            <h5>李世民</h5>
          </div>
          <el-menu-item
            :class="{ 'is-active': activeItem === 'index' }"
            @click="setActive('index')"
          >
            <a href="/main" class="custom-link">
              <el-icon><House /></el-icon> 首页
            </a>
          </el-menu-item>
          <el-menu-item
            :class="{ 'is-active': activeItem === 'mybooking' }"
            @click="setActive('mybooking')"
          >
            <a href="/mybooking" class="custom-link">
              <el-icon><Notification /></el-icon> 我的预约
            </a>
          </el-menu-item>
          <el-menu-item
            :class="{ 'is-active': activeItem === 'feedback' }"
            @click="setActive('feedback')"
          >
            <a href="/feedback" class="custom-link">
              <el-icon><Document /></el-icon> 诊后反馈
            </a>
          </el-menu-item>
          <el-menu-item
            :class="{ 'is-active': activeItem === 'communicate' }"
            @click="setActive('communicate')"
          >
            <a href="/communicate" class="custom-link">
              <el-icon><Comment /></el-icon> 在线问答
            </a>
          </el-menu-item>
          <el-menu-item class="out">
            <el-button plain @click="dialogVisible = true">
              <el-icon><SwitchButton /></el-icon> 退出登录
            </el-button>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-container>
        <el-main>
          <slot />
        </el-main>
      </el-container>
    </el-container>
  </div>

  <!-- 退出弹窗 -->
  <el-dialog v-model="dialogVisible" title="提示" width="500">
    <span>你确定要退出登录吗？</span>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="logout">确定</el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script lang="ts" setup>
import {
  House,
  Notification,
  Document,
  Comment,
  SwitchButton,
} from "@element-plus/icons-vue";
import { ref, watch } from "vue";
import { useRouter, useRoute } from "vue-router";
import { ElMessage } from "element-plus"; // 确保引入 ElMessage

const dialogVisible = ref(false);
const activeItem = ref(""); // 跟踪当前选中的菜单项
const router = useRouter();
const route = useRoute();

const setActive = (item: string) => {
  activeItem.value = item;
};

// 监听路由变化，更新 activeItem
watch(
  () => route.path,
  (newPath) => {
    if (newPath.startsWith("/main")) {
      activeItem.value = "index";
    } else if (newPath.startsWith("/mybooking")) {
      activeItem.value = "mybooking";
    } else if (newPath.startsWith("/feedback")) {
      activeItem.value = "feedback";
    } else if (newPath.startsWith("/communicate")) {
      activeItem.value = "communicate";
    }
  },
  { immediate: true }
);

const logout = () => {
  dialogVisible.value = false;
  // 显示退出成功的提示
  ElMessage({
    message: "退出登录成功",
    type: "success",
  });
  // 跳转到登录页面
  router.push("/login");
};
</script>

<style>
.user {
  margin-top: 40px;
  text-align: center;
  margin-left: -50px;
}
.user img {
  width: 50px;
  border-radius: 50%;
}
.out {
  margin-top: 110%;
}

/* 自定义链接样式 */
.custom-link {
  text-decoration: none;
  color: inherit;
  display: flex;
  align-items: center;
  width: 100%; /* 确保链接占满整个菜单项 */
  padding: 10px; /* 添加一些内边距，使悬停效果更明显 */
}

/* 悬停时改变背景色和文字颜色 */
.el-menu-item:hover {
  background-color: #409eff; /* 悬停时的背景色 */
}

.el-menu-item:hover .custom-link {
  color: #ffffff; /* 悬停时的字体颜色 */
}

/* 移除选中状态样式 */
.is-active {
  background-color: #40a0ff41; /* 恢复默认背景色 */
}

.is-active .custom-link {
  color: inherit; /* 恢复默认字体颜色 */
  font-weight: normal; /* 恢复默认字体粗细 */
}
</style>
