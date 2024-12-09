<template>
  <select
    v-if="!horizontal"
    class="join-item select select-bordered select-sm"
    v-model="sourceIPFilter"
  >
    <option
      value=""
      label="all"
    ></option>
    <option
      v-if="sourceIPOpts.every((i) => i.value !== sourceIPFilter) && sourceIPFilter !== ''"
      :value="sourceIPFilter"
      :label="getIPLabelFromMap(sourceIPFilter)"
    />
    <option
      v-for="opt in sourceIPOpts"
      :key="opt.value"
      :value="opt.value"
      :label="opt.label"
    />
  </select>
</template>

<script setup lang="ts">
import { getIPLabelFromMap } from '@/helper'
import { sourceIPFilter, sourceIPs } from '@/store/connections'
import { computed } from 'vue'
defineProps<{
  horizontal?: boolean
}>()

const sourceIPOpts = computed(() => {
  return sourceIPs.value.map((ip) => {
    return {
      label: getIPLabelFromMap(ip),
      value: ip,
    }
  })
})
</script>
