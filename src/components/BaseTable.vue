<template>
  <a-table
    bordered
    :data-source="data"
    :columns="columns"
    :row-selection="{
      selectedRowKeys: state.selectedRowKeys,
      onChange: onSelectChange,
    }"
  >
    <template #title>
      <div class="header-container">
        <div class="header-left">
          <slot name="headerLeft">
            <a-dropdown :trigger="['click']">
              <h3 class="ant-dropdown-link" @click.prevent>
                {{ filterData[filterTypeIndex].label }}
                <DownOutlined />
              </h3>
              <template #overlay>
                <a-menu>
                  <a-menu-item
                    @click="filterType(index)"
                    :key="index"
                    v-for="(item, index) in filterData"
                  >
                    <div>{{ item.label }}</div>
                  </a-menu-item>
                </a-menu>
              </template>
            </a-dropdown>
          </slot>
        </div>
        <div class="header">
          <slot name="header">
            <span
              :key="index"
              class="tag"
              @close.prevent
              v-for="(item, index) in searchInfo"
            >
              {{ item.title + ":  " + item.value }}
              <a-button
                type="link"
                size="small"
                @click="deleteSearchTag(item.dataIndex)"
              >
                <template #icon><CloseOutlined /></template>
              </a-button>
            </span>
          </slot>
        </div>
        <div class="header-right">
          <slot name="headerRight">Header</slot>
        </div>
      </div>
    </template>
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
          @pressEnter="handleSearch(selectedKeys[0], confirm, column.dataIndex)"
        />
        <a-select
          v-if="column.type === 'select'"
          ref="searchSelect"
          :options="columnData[column.dataIndex]"
          :placeholder="`Search ${column.dataIndex}`"
          :value="selectedKeys"
          style="width: 188px; margin: 8px 0; display: block"
          @change="(e) => setSelectedKeys(e)"
        />
        <a-range-picker
          v-if="column.type === 'date'"
          :show-time="{ format: 'HH:mm' }"
          format="YYYY-MM-DD HH:mm"
          :placeholder="['Start Time', 'End Time']"
          @change="(e, date) => setSelectedKeys(date)"
        />
        <div>
          <a-button
            type="primary"
            size="small"
            style="width: 90px; margin-right: 8px"
            @click="handleSearch(selectedKeys, confirm, column, clearFilters)"
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
      </div>
    </template>
    <template #customFilterIcon="{ filtered }">
      <DownCircleOutlined
        :style="{ color: filtered ? '#108ee9' : undefined }"
      />
    </template>
    <template #bodyCell="{ text, column }">
      <slot :name="column.dataIndex" :data="{ text, column }"></slot>
    </template>
  </a-table>
</template>
<script setup>
import {
  SearchOutlined,
  DownOutlined,
  CloseOutlined,
  DownCircleOutlined,
} from "@ant-design/icons-vue";
import { defineProps, onMounted, reactive, ref } from "vue";
defineProps({
  columns: Array,
  filterData: Array,
});
const data = reactive([
  {
    key: "1",
    name: "John Brown",
    age: 32,
    address: "New York No. 1 Lake Park",
    date: "2019-07-13",
  },
  {
    key: "2",
    name: "Joe Black",
    age: 42,
    address: "London No. 1 Lake Park",
    date: "2019-07-13",
  },
  {
    key: "3",
    name: "Jim Green",
    age: 32,
    address: "Sidney No. 1 Lake Park",
    date: "2019-07-13",
  },
  {
    key: "4",
    name: "Jim Red",
    age: 32,
    address: "London No. 2 Lake Park",
    date: "2019-07-13",
  },
]);

onMounted(() => {
  getColumnData();
});
const state = reactive({
  searchText: "",
  searchedColumn: "",
  selectedRowKeys: [],
});
const searchInfo = reactive({});
const columnData = reactive({});
const filterTypeIndex = ref(0);
const searchInput = ref();

const handleSearch = (selectedKeys, confirm, column, clearFilters) => {
  console.log(selectedKeys);
  searchInfo[column.dataIndex] = {
    title: column.title,
    value: column.type === "text" ? selectedKeys[0] : selectedKeys,
    dataIndex: column.dataIndex,
  };
  handleReset(clearFilters);
  confirm();
  state.searchText = selectedKeys;
  state.searchedColumn = column.dataIndex;
  console.log(searchInfo);
};

const handleReset = (clearFilters) => {
  clearFilters({
    confirm: true,
  });
  state.searchText = "";
};

const sortBy = (field, mode) => {
  console.log(field);
  data.sort((x, y) => {
    return mode === "up" ? x[field] - y[field] : y[field] - x[field];
  });
};

const onSelectChange = (selectedRowKeys) => {
  console.log("selectedRowKeys changed: ", selectedRowKeys);
  state.selectedRowKeys = selectedRowKeys;
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
const filterType = (index) => {
  filterTypeIndex.value = index;
};

const deleteSearchTag = (item) => {
  state.searchText = "";
  delete searchInfo[item];
  console.log(state);
};
// const showFilterBox = () => {
//   console.log(111);
// };
</script>
<style scoped>
.header-container {
  display: flex;
  justify-content: flex-start;
}
.header-left {
  width: 12%;
}
.header {
  width: 65%;
}
.header-right {
  width: 28%;
}
.tag {
  display: inline-block;
  font-size: 12px;
  padding: 7px 17px;
  box-sizing: border-box;
  border-radius: 20px;
  border: 1px skyblue solid;
  margin: 0 2px;
}
</style>
