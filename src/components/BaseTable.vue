<template>
  <a-table
    size="middle"
    class="ant-table-striped"
    :data-source="data"
    :columns="columns"
    row-key="id"
    :loading="loading"
    :pagination="pagination"
    @change="handleTableChange"
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
              {{ item.title + ":" }}
              <span style="color: blueviolet">{{ item.value }}</span>
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
      <slot :name="column.dataIndex" :data="{ text, record, index, column }">
        <div v-if="column.type === 'text'">
          <a-input
            v-if="editableData[record.id]"
            v-model:value="editableData[record.id][column.dataIndex]"
            style="margin: -5px 0"
          />
          <template v-else>
            {{ text }}
          </template>
        </div>
        <div v-if="column.dataIndex === 'action'">
          <div class="editable-row-operations">
            <span v-if="editableData[record.id]">
              <a-typography-link @click="save(record.id)"
                >保存</a-typography-link
              >
              <a-popconfirm title="确定取消?" @confirm="cancel(record.id)">
                <a>取消</a>
              </a-popconfirm>
            </span>
            <span v-else>
              <a @click="edit(record.id)">编辑</a>
            </span>
          </div>
        </div>
      </slot>
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
import axios from "axios";
let data = ref([]);
const state = reactive({
  searchText: "",
  searchedColumn: "",
  selectedRowKeys: [],
  searchParams: {},
});
defineProps({
  columns: Array,
  filterData: Array,
  columnData: Object,
});
const editableData = reactive({});

const edit = (key) => {
  editableData[key] = JSON.parse(
    JSON.stringify(data.value.filter((item) => key === item.id)[0])
  );
};

const save = (key) => {
  Object.assign(
    data.value.filter((item) => key === item.id)[0],
    editableData[key]
  );
  delete editableData[key];
  console.log(key);
};

const cancel = (key) => {
  delete editableData[key];
};

const pagination = reactive({
  total: 0,
  current: 1,
  pageSize: 10,
  showSizeChanger: true,
  showQuickJumper: true,
});
const loading = ref(true);
const emit = defineEmits(["changeSelection"]);
onMounted(async () => {
  await getTableData({
    current: 1,
    pageSize: 10,
  });
});
const searchInfo = reactive({});
const filterTypeIndex = ref(0);
const searchInput = ref();

const queryData = async (params) => {
  const tableData = await axios.post(
    "https://api.spacexdata.com/v4/crew/query",
    { query: params }
  );
  return tableData.data;
};

const getTableData = async (params = {}) => {
  loading.value = true;
  const { docs, totalDocs, page, limit } = await queryData(params);
  data.value = docs;
  pagination.current = page;
  pagination.total = totalDocs;
  pagination.pageSize = limit;
  loading.value = false;
};
const handleSearch = (selectedKeys, confirm, column, clearFilters) => {
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
  for (const item in searchInfo) {
    state.searchParams[item] = searchInfo[item].value;
  }
  getTableData(state.searchParams);
};

const handleReset = (clearFilters) => {
  clearFilters({
    confirm: false,
  });
  state.searchText = "";
};

const sortBy = (field, mode) => {
  // data.value.sort((x, y) => {
  //   // return mode === "up" ? x[field] - y[field] : y[field] - x[field];
  //   return mode === "up" ? x[field] - y[field] : y[field] - x[field];
  // });
  data.value.sort((a, b) =>
    mode === "up"
      ? a[field].localeCompare(b[field])
      : b[field].localeCompare(a[field])
  );
};

const onSelectChange = (selectedRowKeys) => {
  state.selectedRowKeys = selectedRowKeys;
  emit("changeSelection", selectedRowKeys);
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
  delete state.searchParams[item];
  getTableData(state.searchParams);
};

const handleTableChange = ({ current, pageSize, total }) => {
  pagination.current = current;
  pagination.pageSize = pageSize;
  pagination.total = total;
  let params = {
    page: current,
    limit: pageSize,
  };
  params = Object.assign(params, state.searchParams);
  getTableData(params);
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
  height: 30px;
}
.header-left {
  width: 8%;
}
.header {
  width: 69%;
}
.header-right {
  width: 23%;
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
  height: 30px;
}
.header-right-selected {
  position: absolute;
  right: -10px;
  top: 4px;
}
.ant-table-striped :deep(.table-striped) td {
  background-color: #fafafa;
}
.editable-row-operations a {
  margin-right: 8px;
}
</style>
