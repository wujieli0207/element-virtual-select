<template>
  <VirtualScrollSelect
    v-model="value"
    :options="options"
    :label-key="labelKey"
    :value-key="valueKey"
  />
</template>

<script setup>
import { VirtualScrollSelect } from '@/components/Select'
import { computed, ref } from 'vue'

const value = ref('')
const labelKey = 'label'
const valueKey = 'value'

const options = computed(() => {
  const result = []
  for (let i = 0; i < 10000; i++) {
    result.push({
      label: `label-${i}`,
      value: `value-${i}`,
    })
  }
  return result
})
</script>
