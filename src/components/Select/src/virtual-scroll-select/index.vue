<template>
  <el-select
    v-model="value"
    filterable
    remote
    :remote-method="handleRemoteMethod"
    @visible-change="handleVisiableChange"
  >
    <virtual-scroll-list
      ref="virtualListRef"
      class="virtual-scroll-list"
      :data-key="dataKey"
      :data-sources="currentOptions"
      :data-component="OptionNode"
      :keeps="20"
      :extra-props="{
        labelKey,
        valueKey,
      }"
    ></virtual-scroll-list>
  </el-select>
</template>

<script setup>
import { toRefs, ref, nextTick, computed, watch, onMounted } from 'vue'
import VirtualScrollList from 'vue-virtual-scroll-list'
import { Select as ElSelect } from 'element-ui'

import OptionNode from './option-node.vue'
import { cloneDeep, isNil } from 'lodash-es'

const props = defineProps({
  value: {
    type: [String, Number],
    default: '',
  },
  options: {
    type: Array,
    default: () => [],
  },
  labelKey: {
    type: String,
    default: 'label',
  },
  valueKey: {
    type: String,
    default: 'value',
  },
})

const { value, options, labelKey, valueKey } = toRefs(props)

const virtualListRef = ref(null)

const dataKey = ref(valueKey)

// 当前筛选的 options
const currentOptions = ref([])
// 全量 options
const allOptions = computed(() => cloneDeep(options.value))

onMounted(() => {
  handleInitOptions(allOptions.value, value.value)
})

watch([value, options], ([newVal, newOptions], [_oldVal, oldOptions]) => {
  // 异步加载 options 时，如果 value 有值，需要将 value 对应的 option 放在第一位
  if (
    (!isNil(newVal) || newVal !== '') &&
    newOptions.length > 0 &&
    oldOptions.length === 0
  ) {
    handleInitOptions(newOptions, newVal)
  }
})

/**
 * @description 因为 DOM 不是全量加载，所以需要手动处理
 */
function handleRemoteMethod(query) {
  if (query !== '') {
    currentOptions.value = allOptions.value.filter((item) => {
      return item[labelKey.value].includes(query)
    })
  } else {
    currentOptions.value = allOptions.value
  }
}

function handleVisiableChange(val) {
  // 隐藏下拉框时，重置数据
  if (!val) {
    virtualListRef.value && virtualListRef.value.reset()
    nextTick(() => {
      currentOptions.value = allOptions.value
    })
  }
}

/**
 * @description 异步加载 options 时，如果 value 有值，需要将 value 对应的 option 放在第一位
 */
function handleInitOptions(allOptions, value) {
  const existOption = allOptions.find((item) => {
    return item[valueKey.value] === value
  })
  if (existOption) {
    currentOptions.value.push(existOption)
  }

  currentOptions.value.push(
    ...allOptions.filter((item) => {
      return item[valueKey.value] !== value
    })
  )
}
</script>

<style scoped>
.virtual-scroll-list {
  height: 200px;
  overflow: auto;
}
</style>
