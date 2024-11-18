<template>
  <div>
    <div class="grid gap-6 md:grid-cols-2">
      <div class="space-y-4">
        <label class="block text-sm font-medium text-gray-900 dark:text-gray-100">Output Dimensions</label>
        <div class="grid grid-cols-2 gap-4">
          <div class="relative">
            <label class="absolute -top-2 left-2 px-1 text-xs font-medium text-gray-500 dark:text-gray-400 bg-white dark:bg-gray-800">Width</label>
            <input 
              type="number" 
              :value="width"
              @input="handleDimensionChange('width', $event.target.value)"
              class="block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-lg shadow-sm focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
              min="1"
              :max="maxDimension"
              :disabled="converting"
            >
            <span class="absolute right-3 top-2 text-gray-400 dark:text-gray-500 text-sm">px</span>
          </div>
          <div class="relative">
            <label class="absolute -top-2 left-2 px-1 text-xs font-medium text-gray-500 dark:text-gray-400 bg-white dark:bg-gray-800">Height</label>
            <input 
              type="number" 
              :value="height"
              @input="handleDimensionChange('height', $event.target.value)"
              class="block w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-lg shadow-sm focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100"
              min="1"
              :max="maxDimension"
              :disabled="converting"
            >
            <span class="absolute right-3 top-2 text-gray-400 dark:text-gray-500 text-sm">px</span>
          </div>
        </div>
        <p class="text-xs text-gray-500 dark:text-gray-400">
          Max dimension: {{ maxDimension }}px
        </p>
      </div>

      <div class="space-y-4">
        <label class="block text-sm font-medium text-gray-900 dark:text-gray-100">Scale Factor</label>
        <div class="grid grid-cols-4 gap-3">
          <button
            v-for="s in availableScaleOptions"
            :key="s"
            @click="handleScaleChange(s)"
            :class="[
              'px-3 py-2 text-sm font-medium rounded-lg focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition-colors duration-200',
              scale === s
                ? 'bg-indigo-600 dark:bg-indigo-500 text-white'
                : 'bg-white dark:bg-gray-700 border border-gray-300 dark:border-gray-600 text-gray-700 dark:text-gray-200 hover:bg-gray-50 dark:hover:bg-gray-600'
            ]"
            :disabled="converting || !isScaleAllowed(s)"
          >
            {{ s }}x
          </button>
        </div>
      </div>
    </div>

    <div class="mt-6">
      <div v-if="error" class="mb-4 p-4 bg-red-50 dark:bg-red-900/20 border border-red-200 dark:border-red-800 rounded-lg">
        <p class="text-sm text-red-600 dark:text-red-400">{{ error }}</p>
      </div>
      
      <div class="flex justify-center">
        <button
          @click="$emit('convert')"
          :disabled="converting "
          class="inline-flex items-center px-8 py-3 border border-transparent text-base font-medium rounded-xl text-white bg-gradient-to-r from-indigo-600 to-blue-500 hover:from-indigo-700 hover:to-blue-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 shadow-lg shadow-indigo-500/30 transition-all duration-200 ease-in-out hover:shadow-indigo-500/40 disabled:opacity-50 disabled:cursor-not-allowed dark:shadow-indigo-900/30 dark:hover:shadow-indigo-900/40"
        >
          <template v-if="converting">
            <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
            Converting...
          </template>
          <template v-else>
            Convert to PNG
          </template>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  width: Number,
  height: Number,
  scale: Number,
  converting: Boolean,
  error: String
})

const emit = defineEmits(['update:width', 'update:height', 'update:scale', 'convert'])

const maxDimension = 16384 // Maximum canvas dimension supported by most browsers
const scaleOptions = [1, 2, 4, 8, 16, 32, 64]

// Store base dimensions for scaling calculations
const baseDimensions = ref({
  width: props.width,
  height: props.height
})

const availableScaleOptions = computed(() => {
  return scaleOptions.filter(s => isScaleAllowed(s))
})

const isValidConfiguration = computed(() => {
  const finalWidth = props.width * props.scale
  const finalHeight = props.height * props.scale
  return finalWidth <= maxDimension && finalHeight <= maxDimension
})

function isScaleAllowed(scale) {
  const finalWidth = baseDimensions.value.width * scale
  const finalHeight = baseDimensions.value.height * scale
  return finalWidth <= maxDimension && finalHeight <= maxDimension
}

function handleDimensionChange(dimension, value) {
  const numValue = parseInt(value)
  if (isNaN(numValue) || numValue < 1) return
  
  // Update base dimensions when user manually changes values
  baseDimensions.value[dimension] = numValue / props.scale
  
  emit(`update:${dimension}`, Math.min(numValue, maxDimension))
}

function handleScaleChange(newScale) {
  if (isScaleAllowed(newScale)) {
    // Calculate new dimensions based on base dimensions and scale
    const newWidth = Math.round(baseDimensions.value.width * newScale)
    const newHeight = Math.round(baseDimensions.value.height * newScale)
    
    emit('update:width', newWidth)
    emit('update:height', newHeight)
    emit('update:scale', newScale)
  }
}

// Initialize base dimensions when component mounts
if (props.width && props.height && props.scale) {
  baseDimensions.value = {
    width: Math.round(props.width / props.scale),
    height: Math.round(props.height / props.scale)
  }
}
</script>