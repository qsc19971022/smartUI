<template>
  <a-table
    size="middle"
    class="ant-table-striped"
    :data-source="data"
    :columns="columns"
    :row-class-name="
      (_record, index) => (index % 2 === 1 ? 'table-striped' : null)
    "
    :row-selection="{
      selectedRowKeys: state.selectedRowKeys,
      onChange: onSelectChange,
    }"
  >
    <template #title>
      <div v-if="!state.selectedRowKeys.length" class="header-container">
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
      <div v-else class="header-selected">
        <div>已选中 {{ state.selectedRowKeys.length }} 项</div>
        <a-button
          class="header-right-selected"
          type="link"
          size="small"
          @click="closeSelected"
        >
          <template #icon><CloseOutlined /></template>
        </a-button>
      </div>
    </template>
    <template #headerCell="{ column }">
      <template v-if="column.key === 'name'"> </template>
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
          type="text"
          size="small"
          style="display: block; margin: 10px 0"
          @click="sortBy(column.dataIndex, 'up')"
        >
          <template #icon><SortDescendingOutlined /></template>
          升序
        </a-button>
        <a-button
          size="small"
          type="text"
          style="display: block; margin: 10px 0"
          @click="sortBy(column.dataIndex, 'down')"
        >
          <template #icon><SortAscendingOutlined /></template>
          降序</a-button
        >
        <a-divider style="margin: 12px 0" />
        <div style="margin-left: 6px; margin-bottom: 10px; font-size: 14px">
          <FilterFilled />
          筛选
        </div>
        <a-input
          v-if="column.type === 'text'"
          ref="searchInput"
          :placeholder="`请输入${column.title}`"
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
          :placeholder="`请输入${column.title}`"
          :value="selectedKeys"
          style="width: 188px; margin: 8px 0; display: block"
          @change="(e) => setSelectedKeys(e ? [e] : [])"
        />
        <a-range-picker
          v-if="column.type === 'date'"
          :show-time="{ format: 'HH:mm' }"
          format="YYYY-MM-DD HH:mm"
          :placeholder="['开始时间', '结束时间']"
          @change="(e, date) => setSelectedKeys(date)"
        />
        <div style="margin-top: 18px; text-align: right">
          <a-button
            size="small"
            style="width: 70px; margin-right: 8px"
            @click="handleReset(clearFilters)"
          >
            重置
          </a-button>
          <a-button
            type="primary"
            size="small"
            style="width: 70px"
            @click="handleSearch(selectedKeys, confirm, column, clearFilters)"
          >
            搜索
          </a-button>
        </div>
      </div>
    </template>
    <template #customFilterIcon="{ filtered }">
      <DownCircleOutlined
        :style="{ color: filtered ? '#108ee9' : undefined }"
      />
    </template>
    <template #bodyCell="{ text, record, index, column }">
      <slot
        :name="column.dataIndex"
        :data="{ text, record, index, column }"
      ></slot>
    </template>
  </a-table>
</template>
<script setup>
import {
  DownOutlined,
  CloseOutlined,
  DownCircleOutlined,
  SortDescendingOutlined,
  SortAscendingOutlined,
  FilterFilled,
} from "@ant-design/icons-vue";
import {
  defineProps,
  onMounted,
  reactive,
  ref,
  defineEmits,
  defineExpose,
} from "vue";
const state = reactive({
  searchText: "",
  searchedColumn: "",
  selectedRowKeys: [],
});
defineProps({
  columns: Array,
  filterData: Array,
});
const emit = defineEmits(["changeSelection"]);
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
const searchInfo = reactive({});
const columnData = reactive({});
const filterTypeIndex = ref(0);
const searchInput = ref();

const handleSearch = (selectedKeys, confirm, column, clearFilters) => {
  console.log(selectedKeys);
  if (selectedKeys.length) {
    searchInfo[column.dataIndex] = {
      title: column.title,
      value: selectedKeys[0],
      dataIndex: column.dataIndex,
    };
  }
  handleReset(clearFilters);
  confirm();
  state.searchText = selectedKeys[0];
  state.searchedColumn = column.dataIndex;
};

const handleReset = (clearFilters) => {
  clearFilters({
    confirm: false,
  });
  state.searchText = "";
};

const sortBy = (field, mode) => {
  data.sort((x, y) => {
    return mode === "up" ? x[field] - y[field] : y[field] - x[field];
  });
};

const onSelectChange = (selectedRowKeys) => {
  state.selectedRowKeys = selectedRowKeys;
  emit("changeSelection", selectedRowKeys);
};

const getColumnData = () => {
  const keys = Object.keys(data[0]);
  keys.map((item) => {
    let temp = [];
    data.map((res) => {
      temp.push({ label: res.age, value: res.age });
    });
    columnData[item] = temp;
  });
};
const filterType = (index) => {
  filterTypeIndex.value = index;
};

const closeSelected = () => {
  state.selectedRowKeys = [];
};
const deleteSearchTag = (item) => {
  state.searchText = "";
  delete searchInfo[item];
};

defineExpose({
  state,
});
</script>
<style scoped>
.header-container {
  display: flex;
  justify-content: flex-start;
  box-sizing: border-box;
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
  padding: 2px 8px;
  box-sizing: border-box;
  border-radius: 20px;
  border: 1px skyblue solid;
  margin: 0 2px;
}
.header-selected {
  position: relative;
  padding: 7px 0;
  box-sizing: border-box;
}
.header-right-selected {
  position: absolute;
  right: -10px;
  top: 4px;
}
.ant-table-striped :deep(.table-striped) td {
  background-color: #fafafa;
}
</style>
