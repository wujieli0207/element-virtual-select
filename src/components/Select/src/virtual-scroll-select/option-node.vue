<template>
  <el-option :key="value" :label="label" :value="value" />
</template>

<script setup>
import { computed, toRefs } from 'vue'
import { Option as ElOption } from 'element-ui'

const props = defineProps({
  source: {
    type: Object,
    default: () => {},
  },
  valueKey: {
    type: [String, Number],
    default: '',
  },
  labelKey: {
    type: String,
    default: '',
  },
})

const { source, valueKey, labelKey } = toRefs(props)

const value = computed(() => source.value[valueKey.value])
const label = computed(() => source.value[labelKey.value])
</script>
