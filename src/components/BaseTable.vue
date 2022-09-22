<template>
  <a-table :data-source="data" :columns="columns" @change="handleChange">
    <template #headerCell="{ column }">
      <template v-if="column.key === 'name'">
        <span style="color: #1890ff">Name</span>
      </template>
    </template>
    <template
      #customFilterDropdown="{
        setSelectedKeys,
        selectedKeys,
        confirm,
        clearFilters,
        column,
      }"
    >
      <div style="padding: 8px">
        <a-button
          style="display: block; margin: 5px 0"
          @click="sortBy(column.dataIndex, 'up')"
          >升序</a-button
        >
        <a-button @click="sortBy(column.dataIndex, 'down')">降序</a-button>
        <a-button style="display: block; margin: 5px 0">清除排序</a-button>
        <a-input
          v-if="column.type === 'text'"
          ref="searchInput"
          :placeholder="`Search ${column.dataIndex}`"
          :value="selectedKeys[0]"
          style="width: 188px; margin-bottom: 8px; display: block"
          @change="
            (e) => setSelectedKeys(e.target.value ? [e.target.value] : [])
          "
          @pressEnter="handleSearch(selectedKeys, confirm, column.dataIndex)"
        />
        <a-select
          v-if="column.type === 'select'"
          ref="searchSelect"
          :options="columnData[column.key]"
          :placeholder="`Search ${column.dataIndex}`"
          :value="selectedKeys[0]"
          style="width: 188px; margin: 8px 0; display: block"
          @change="
            (e) => setSelectedKeys(e.target.value ? [e.target.value] : [])
          "
          @pressEnter="handleSearch(selectedKeys, confirm, column.dataIndex)"
        />
        <a-button
          type="primary"
          size="small"
          style="width: 90px; margin-right: 8px"
          @click="handleSearch(selectedKeys, confirm, column.dataIndex)"
        >
          <template #icon><SearchOutlined /></template>
          搜索
        </a-button>
        <a-button
          size="small"
          style="width: 90px"
          @click="handleReset(clearFilters)"
        >
          重置
        </a-button>
      </div>
    </template>
    <template #customFilterIcon="{ filtered }">
      <search-outlined :style="{ color: filtered ? '#108ee9' : undefined }" />
    </template>
    <template #bodyCell="{ text, column }">
      <slot :name="column.dataIndex" :data="{ text, column }"></slot>
    </template>
  </a-table>
</template>
<script setup>
import { SearchOutlined } from "@ant-design/icons-vue";
import { defineProps, onMounted, reactive, ref } from "vue";
defineProps({
  columns: Array,
});
const data = reactive([
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
]);

onMounted(() => {
  getColumnData();
});
const state = reactive({
  searchText: "",
  searchedColumn: "",
});
const searchInfo = reactive({});
const columnData = reactive({});
const searchInput = ref();

const handleSearch = (selectedKeys, confirm, dataIndex) => {
  searchInfo[dataIndex] = selectedKeys[0];
  confirm();
  state.searchText = selectedKeys[0];
  state.searchedColumn = dataIndex;
  console.log(searchInfo);
};

const handleReset = (clearFilters) => {
  clearFilters({
    confirm: true,
  });
  state.searchText = "";
};

const sortBy = (field, mode) => {
  data.sort((x, y) => {
    return mode === "up" ? x[field] - y[field] : y[field] - x[field];
  });
};

const getColumnData = () => {
  const keys = Object.keys(data[0]);
  keys.map((item) => {
    let temp = [];
    data.map((res) => {
      temp.push({ label: res.age, value: res.age });
    });
    columnData[item] = temp;
    // columnData[item] =
  });
  console.log(columnData.age);
};
</script>
<style scoped>
.highlight {
  background-color: rgb(255, 192, 105);
  padding: 0px;
}
</style>
