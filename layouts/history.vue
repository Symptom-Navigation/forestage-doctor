<template>
  <el-table ref="tableRef" row-key="date" :data="tableData" style="width: 100%">
    <el-table-column prop="name" label="姓名" width="80" />
    <el-table-column
      prop="tag"
      label="预处理"
      width="80"
      :filters="[
        { text: '通过', value: '通过' },
        { text: '撤回', value: '撤回' },
      ]"
      :filter-method="filterTag"
      filter-placement="bottom-end"
    >
      <template #default="scope">
        <el-tag
          :type="scope.row.tag === '撤回' ? '通过' : 'success'"
          disable-transitions
          >{{ scope.row.tag }}</el-tag
        >
      </template>
    </el-table-column>
    <el-table-column label="操作" width="80px">
      <template v-slot="scope">
        <!-- 修改按钮 -->
        <el-button type="primary" size="mini"
          ><el-icon><edit /></el-icon
        ></el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import type { TableColumnCtx, TableInstance } from "element-plus";
import { Edit } from "@element-plus/icons-vue";
interface User {
  date: string;
  name: string;

  tag: string;
}

const tableRef = ref<TableInstance>();

const filterTag = (value: string, row: User) => {
  return row.tag === value;
};

const tableData: User[] = [
  {
    date: "2016-05-06",
    name: "小明",
    tag: "通过",
  },
  {
    date: "2016-05-04",
    name: "Tom1",
    tag: "撤回",
  },
  {
    date: "2016-05-01",
    name: "Tom2",
    tag: "通过",
  },
];
</script>
