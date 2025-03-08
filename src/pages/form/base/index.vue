<template>
  <t-card title="基本信息" :bordered="false">
    <t-form ref="formRef" :model="formData" :rules="rules">
      <div class="scrollable-table-container">
        <t-table
          :data="paginatedData"
          row-key="id"
          :columns="columns"
          resizable
          lazy-load
          label-width="0"
          size="large"
          bordered
          table-layout
          :hover="true"
          :stripe="true"
        >
          <!-- 序号列 -->
          <template #index="{ rowIndex }">
            <span :class="['row-index', `row-${getGlobalIndex(rowIndex)}`]">
              {{ getGlobalIndex(rowIndex) + 1 }}
            </span>
          </template>

          <!-- 文本输入列 -->
          <template #textInput="{ row, rowIndex }">
            <t-form-item :name="`accountTable[${getGlobalIndex(rowIndex)}].textInput`">
              <t-input
                v-if="isEdit"
                v-model="row.textInput"
                placeholder="请输入文本"
                @change="handleFieldChange(row, 'textInput')"
              />
              <span v-else>{{ row.textInput }}</span>
            </t-form-item>
          </template>

          <!-- 下拉选择列 -->
          <template #selectOption="{ row, rowIndex }">
            <t-form-item :name="`accountTable[${getGlobalIndex(rowIndex)}].selectOption`">
              <t-select
                v-if="isEdit"
                v-model="row.selectOption"
                :options="selectOptions"
                placeholder="请选择"
                @change="handleFieldChange(row, 'selectOption')"
              />
              <span v-else>{{ getSelectLabel(row.selectOption) }}</span>
            </t-form-item>
          </template>

          <!-- 单选框列 -->
          <template #radioChoice="{ row, rowIndex }">
            <t-form-item :name="`accountTable[${getGlobalIndex(rowIndex)}].radioChoice`">
              <t-radio-group
                v-if="isEdit"
                v-model="row.radioChoice"
                :options="radioOptions"
                @change="handleFieldChange(row, 'radioChoice')"
              />
              <span v-else>{{ getRadioLabel(row.radioChoice) }}</span>
            </t-form-item>
          </template>

          <!-- 操作列 -->
          <template #operation="{ row }">
            <t-link class="link-style" @click="handleDelete(row)">删除</t-link>
          </template>
        </t-table>

        <!-- 分页器 -->
        <t-pagination
          v-model="currentPage"
          :total="formData.accountTable.length"
          :page-size="pageSize"
          @change="handlePageChange"
        />

        <!-- 提交按钮 -->
        <t-button theme="primary" style="margin-top: 20px" @click="handleSubmit"> 提交所有数据 </t-button>
      </div>
    </t-form>
  </t-card>
</template>

<script setup lang="ts">
import type { FormInstanceFunctions, FormRule } from 'tdesign-vue-next';
import { computed, reactive, ref } from 'vue';

defineProps({
  isEdit: {
    type: Boolean,
    default: true,
  },
});

// 类型声明
interface TableItem {
  id: number;
  textInput: string;
  selectOption: number | null;
  radioChoice: number | null;
  blacklistProcess: string;
}

type ValidationError = {
  page: number;
  rowIndex: number;
  field: keyof TableItem;
};
// 表格列配置（包含序号列）
const columns = [
  { width: 80, colKey: 'index', title: '序号', align: 'center' },
  { width: 200, colKey: 'textInput', title: '文本输入', align: 'center' },
  { width: 200, colKey: 'selectOption', title: '下拉选择', align: 'center' },
  { width: 200, colKey: 'radioChoice', title: '单选选项', align: 'center' },
  { width: 200, colKey: 'blacklistProcess', title: '拉黑名单', align: 'center' },
  { width: 120, colKey: 'operation', title: '操作', align: 'center' },
];

// 响应式数据
const formData = reactive<{ accountTable: TableItem[] }>({
  accountTable: Array.from({ length: 3 }, (_, index) => ({
    id: index + 1,
    textInput: '',
    selectOption: 1,
    radioChoice: 1,
    blacklistProcess: `处理${index + 1}`,
  })),
});

// 分页相关
const pageSize = 5;
const currentPage = ref(1);
const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * pageSize;
  return formData.accountTable.slice(start, start + pageSize);
});

// 校验规则
const rules: Record<string, FormRule[]> = {
  textInput: [
    {
      required: true,
      validator: (val: string) => !!val?.trim(),
      message: '文本不能为空',
      type: 'error',
      trigger: 'change',
    },
  ],
  selectOption: [
    {
      required: true,
      validator: (val: number | null) => val !== null && val !== undefined,
      message: '请选择下拉选项',
      type: 'error',
      trigger: 'change',
    },
  ],
  radioChoice: [
    {
      required: true,
      validator: (val: number | null) => val !== null && val !== undefined,
      message: '请选择单选选项',
      type: 'error',
      trigger: 'change',
    },
  ],
};

// 错误定位相关
const firstError = ref<ValidationError | null>(null);

// 全局校验方法
const validateAll = (): boolean => {
  firstError.value = null;

  for (let i = 0; i < formData.accountTable.length; i++) {
    const item = formData.accountTable[i];
    const fields: (keyof TableItem)[] = ['textInput', 'selectOption', 'radioChoice'];

    for (const field of fields) {
      const isValid = rules[field][0].validator(item[field]);
      if (!isValid) {
        firstError.value = {
          page: Math.floor(i / pageSize) + 1,
          rowIndex: i % pageSize,
          field,
        };
        return false;
      }
    }
  }
  return true;
};

// 跳转到错误位置
const jumpToError = async () => {
  if (!firstError.value) return;

  // 跳转页面
  if (currentPage.value !== firstError.value.page) {
    currentPage.value = firstError.value.page;
  }
};

// 提交处理
const formRef = ref<FormInstanceFunctions>();
const handleSubmit = async () => {
  try {
    // 执行全局校验
    // formRef.value.validate();
    console.log('提交数据：', formData.accountTable);
    formRef.value.setValidateMessage(rules as any);
    if (!validateAll()) {
      await jumpToError();
      throw new Error(`第 ${firstError.value!.page} 页存在未填写的必填字段`);
    }

    console.log('提交数据：', formData.accountTable);
    alert('提交成功！');
  } catch (error) {
    console.error('校验失败：', error);
    alert(error.message);
  }
};

// 全局索引计算
const getGlobalIndex = (rowIndex: number) => (currentPage.value - 1) * pageSize + rowIndex;
// 分页切换
const handlePageChange = (pageInfo: any) => {
  console.log('pageInfo', pageInfo);
  currentPage.value = pageInfo.current;
};

// 下拉选项
const selectOptions = [
  { label: '选项1', value: 1 },
  { label: '选项2', value: 2 },
  { label: '选项3', value: 3 },
];

// 单选项
const radioOptions = [
  { label: '是', value: 1 },
  { label: '否', value: 0 },
];

// 获取选项标签
const getSelectLabel = (value: number) => selectOptions.find((opt) => opt.value === value)?.label || '';

const getRadioLabel = (value: number) => radioOptions.find((opt) => opt.value === value)?.label || '';

// 删除行
const handleDelete = (row: any) => {
  const index = formData.accountTable.findIndex((item) => item.id === row.id);
  if (index !== -1) {
    formData.accountTable.splice(index, 1);
  }
};
// 字段变更处理
const handleFieldChange = (row: any, field: string) => {
  console.log(row, field);
  // const { validator } = rules[field][0];
  // validator(row[field]);
  // // eslint-disable-next-line no-underscore-dangle
  // row._validated =
};
</script>

<style lang="less" scoped>
.error-highlight {
  animation: error-flash 1.5s ease-in-out;
  background-color: #fff3f3;
  border: 1px solid #ff5c5c !important;
}

@keyframes error-flash {
  0% {
    background-color: #fff3f3;
  }
  50% {
    background-color: #ffe0e0;
  }
  100% {
    background-color: #fff3f3;
  }
}

.row-index {
  display: inline-block;
  padding: 0 8px;
  border-radius: 4px;
}
</style>
