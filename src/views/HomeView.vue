<template>
  <div class="container">
    <base-table
      :state="state"
      :columns="columns"
      :column-data="columnData"
      :filterData="filterData"
      @changeSelection="changeSelection"
    >
      <!--      <template #header>-->
      <!--        <div>啦啦啦啦啦</div>-->
      <!--      </template>-->
      <template #image="{ data }">
        <a-image :width="30" :src="data.text" />
      </template>
      <template #headerRight>右侧按钮插槽</template>
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
const columnData = {
  agency: [
    { label: "NASA", value: "NASA" },
    { label: "ESA", value: "ESA" },
    { label: "JAXA", value: "JAXA" },
  ],
};
const filterData = [
  { label: "嘻嘻的客户", value: 1 },
  { label: "啦啦的客户", value: 2 },
  { label: "嘿嘿的客户", value: 3 },
  { label: "呵呵的客户", value: 4 },
  { label: "哈哈的客户", value: 5 },
];
const columns = [
  {
    title: "ID",
    dataIndex: "id",
    key: "id",
    width: 300,
  },
  {
    title: "宇航员",
    dataIndex: "name",
    key: "name",
    type: "text",
    width: 280,
    customFilterDropdown: true,
  },
  {
    title: "机构",
    dataIndex: "agency",
    key: "agency",
    type: "select",
    customFilterDropdown: true,
  },
  {
    title: "状态",
    dataIndex: "status",
    key: "status",
    type: "text",
    width: 300,
  },
  {
    title: "照片",
    dataIndex: "image",
    key: "image",
  },
  {
    title: "操作",
    dataIndex: "action",
    key: "action",
    width: 400,
  },
];
// const edit = (item) => {
//   console.log(item);
// };

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
