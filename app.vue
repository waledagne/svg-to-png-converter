<template>
  <div class="min-h-screen bg-gradient-to-b from-gray-50 to-gray-100 dark:from-gray-900 dark:to-gray-800 transition-colors duration-200">
    <header class="bg-white dark:bg-gray-800 shadow-sm transition-colors duration-200">
      <nav class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <div class="flex h-16 justify-between items-center">
          <div class="flex-shrink-0 flex items-center">
            <h1 class="text-2xl font-bold bg-gradient-to-r from-indigo-600 to-blue-500 bg-clip-text text-transparent">SVG to PNG Converter</h1>
          </div>
          <ThemeToggle />
        </div>
      </nav>
    </header>

    <main class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 py-12">
      <div class="bg-white dark:bg-gray-800 rounded-2xl shadow-xl p-6 md:p-8 backdrop-blur-sm bg-white/90 dark:bg-gray-800/90 transition-colors duration-200">
        <SvgConverter />
      </div>
    </main>
  </div>
</template>

<script setup>
// Prevent flash of wrong theme
useHead({
  htmlAttrs: {
    class: ''
  },
  script: [
    {
      children: `
        (function() {
          const theme = localStorage.getItem('theme') || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
          document.documentElement.classList.toggle('dark', theme === 'dark');
        })()
      `,
      tagPosition: 'bodyOpen'
    }
  ]
})
</script>