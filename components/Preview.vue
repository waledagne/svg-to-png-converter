<template>
  <div class="space-y-6">
    <div class="flex items-center">
      <h3 class="text-lg font-medium text-gray-900 dark:text-gray-100">Preview</h3>
      <div class="ml-2 px-2 py-1 text-xs font-medium text-indigo-700 dark:text-indigo-300 bg-indigo-100 dark:bg-indigo-900 rounded-full">
        {{ dimensions.width }}x{{ dimensions.height }}
      </div>
    </div>
    <div class="border rounded-xl p-6 bg-gray-50/50 dark:bg-gray-900/50 backdrop-blur-sm transition-colors duration-200">
      <div class="flex justify-center" v-html="svgContent"></div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  svgContent: {
    type: String,
    required: true
  },
  dimensions: {
    type: Object,
    required: true
  }
})
</script>