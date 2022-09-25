<template>
  <div class="container">
    <base-table
      :state="state"
      :columns="columns"
      :filterData="filterData"
      @changeSelection="changeSelection"
    >
      <!--      <template #header>-->
      <!--        <div>啦啦啦啦啦</div>-->
      <!--      </template>-->
      <template #headerRight>右侧按钮插槽</template>
      <template #action="{ data }">
        <a-button type="primary" size="small" @click="edit(data)"
          >编辑</a-button
        >
      </template>
    </base-table>
  </div>
</template>
<script setup>
import BaseTable from "@/components/BaseTable";
import { reactive } from "vue";
const state = reactive({
  searchText: "",
  searchedColumn: "",
});

const filterData = [
  { label: "嘻嘻的客户", value: 1 },
  { label: "啦啦的客户", value: 2 },
  { label: "嘿嘿的客户", value: 3 },
  { label: "呵呵的客户", value: 4 },
  { label: "哈哈的客户", value: 5 },
];
const columns = [
  {
    title: "名称",
    dataIndex: "name",
    key: "name",
    type: "text",
    customFilterDropdown: true,
  },
  {
    title: "年龄",
    dataIndex: "age",
    key: "age",
    type: "select",
    customFilterDropdown: true,
  },
  {
    title: "地址",
    dataIndex: "address",
    key: "address",
    type: "text",
  },
  {
    title: "日期",
    dataIndex: "date",
    key: "date",
    type: "date",
    customFilterDropdown: true,
  },
  {
    title: "操作",
    dataIndex: "action",
    key: "action",
  },
];
const edit = (item) => {
  console.log(item);
};

const changeSelection = (item) => {
  console.log(item);
};
</script>
<style scoped>
.container {
  width: 90%;
  height: 400px;
  margin: 30px auto;
}
</style>
