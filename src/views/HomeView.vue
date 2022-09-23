<template>
  <div class="container">
    <base-table :state="state" :columns="columns" :filterData="filterData">
      <!--      <template #header>-->
      <!--        <div>啦啦啦啦啦</div>-->
      <!--      </template>-->
      <template #name="{ data }">
        <div>大大大 {{ data.text }}</div>
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
    title: "Name",
    dataIndex: "name",
    key: "name",
    type: "text",
    customFilterDropdown: true,
  },
  {
    title: "Age",
    dataIndex: "age",
    key: "age",
    type: "select",
    customFilterDropdown: true,
  },
  {
    title: "Address",
    dataIndex: "address",
    key: "address",
    type: "text",
    customFilterDropdown: true,
  },
  {
    title: "date",
    dataIndex: "date",
    key: "date",
    type: "date",
    customFilterDropdown: true,
  },
];
</script>
<style scoped>
.container {
  width: 90%;
  height: 400px;
  margin: 30px auto;
}
</style>
