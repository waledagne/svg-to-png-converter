<template>
  <div 
    class="border-2 border-dashed border-indigo-200 dark:border-indigo-800 rounded-xl p-8 text-center transition-all duration-200 ease-in-out hover:border-indigo-400 dark:hover:border-indigo-600 hover:bg-indigo-50/50 dark:hover:bg-indigo-900/20"
    @dragover.prevent
    @drop.prevent="handleDrop"
    @click="$refs.fileInput.click()"
  >
    <input
      ref="fileInput"
      type="file"
      accept=".svg"
      class="hidden"
      @change="handleSelect"
    >
    <div class="space-y-4">
      <div class="mx-auto h-16 w-16 flex items-center justify-center rounded-full bg-indigo-100 dark:bg-indigo-900">
        <svg class="h-8 w-8 text-indigo-600 dark:text-indigo-400" stroke="currentColor" fill="none" viewBox="0 0 48 48">
          <path d="M28 8H12a4 4 0 00-4 4v20m32-12v8m0 0v8a4 4 0 01-4 4H12a4 4 0 01-4-4v-4m32-4l-3.172-3.172a4 4 0 00-5.656 0L28 28M8 32l9.172-9.172a4 4 0 015.656 0L28 28m0 0l4 4m4-24h8m-4-4v8m-12 4h.02" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
        </svg>
      </div>
      <div class="text-sm text-gray-600 dark:text-gray-300">
        <label class="relative cursor-pointer rounded-md font-medium text-indigo-600 dark:text-indigo-400 hover:text-indigo-500 dark:hover:text-indigo-300">
          <span>Upload an SVG file</span>
        </label>
        <p class="pl-1">or drag and drop</p>
      </div>
      <p class="text-xs text-gray-500 dark:text-gray-400">SVG files only</p>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(['file-selected'])

const handleSelect = (event) => {
  const file = event.target.files[0]
  if (file && file.type === 'image/svg+xml') {
    emit('file-selected', file)
  }
}

const handleDrop = (event) => {
  const file = event.dataTransfer.files[0]
  if (file && file.type === 'image/svg+xml') {
    emit('file-selected', file)
  }
}
</script>