<template>
  <base-table :data="data" :state="state" :columns="columns">
    <template #name="{ text, column }">
      <div>{{ text + "||" + column }}</div>
    </template>
  </base-table>
</template>
<script setup>
import BaseTable from "@/components/BaseTable";
import { reactive } from "vue";
const data = [
  {
    key: "1",
    name: "John Brown",
    age: 32,
    address: "New York No. 1 Lake Park",
  },
  {
    key: "2",
    name: "Joe Black",
    age: 42,
    address: "London No. 1 Lake Park",
  },
  {
    key: "3",
    name: "Jim Green",
    age: 32,
    address: "Sidney No. 1 Lake Park",
  },
  {
    key: "4",
    name: "Jim Red",
    age: 32,
    address: "London No. 2 Lake Park",
  },
];
const state = reactive({
  searchText: "",
  searchedColumn: "",
});

const columns = [
  {
    title: "Name",
    dataIndex: "name",
    key: "name",
    type: "text",
    customFilterDropdown: true,
    scopedSlots: { customRender: "name" },
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
];
</script>
<style scoped>
.highlight {
  background-color: rgb(255, 192, 105);
  padding: 0px;
}
</style>
