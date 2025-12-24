<template>
  <div
    v-if="isDom"
    :class="['inline-block', fill || 'fill-primary']"
    :style="style"
    v-html="pureDom"
  />
  <img
    v-else
    class="inline-block"
    :style="style"
    :src="finalIcon"
    @error="handleImageError"
  />
</template>

<script setup lang="ts">
import DOMPurify from 'dompurify'
import { computed, ref } from 'vue'

const props = withDefaults(
  defineProps<{
    icon: string
    fill?: string
    size?: number
    margin?: number
  }>(),
  {
    size: 16,
    margin: 4,
  },
)

// 是否使用原始URL（当本地路径失败时）
const useOriginalUrl = ref<boolean>(false)

const style = computed(() => {
  return {
    width: `${props.size}px`,
    height: `${props.size}px`,
    marginRight: `${props.margin}px`,
  }
})
const DOM_STARTS_WITH = 'data:image/svg+xml,'
const isDom = computed(() => {
  return props.icon.startsWith(DOM_STARTS_WITH)
})

const pureDom = computed(() => {
  if (!isDom.value) return
  return DOMPurify.sanitize(props.icon.replace(DOM_STARTS_WITH, ''))
})

// 提取文件名的函数
const extractFileName = (url: string): string => {
  const lastSlashIndex = url.lastIndexOf('/')
  return lastSlashIndex !== -1 ? url.substring(lastSlashIndex + 1) : url
}

// 使用 computed 计算最终的图片URL
const finalIcon = computed(() => {
  if (isDom.value) return props.icon

  // 如果已经标记使用原始URL，直接返回原始URL
  if (useOriginalUrl.value) {
    return props.icon
  }

  // 提取文件名并生成本地路径
  const fileName = extractFileName(props.icon)
  const localPath = `/ui/icons/${fileName}`

  return localPath
})

// 处理图片加载错误
const handleImageError = () => {
  // 直接切换到原始URL
  useOriginalUrl.value = true
}
</script>
