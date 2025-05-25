<script setup>
import { MoonIcon, SunIcon } from '@heroicons/vue/24/outline'

defineProps({
  isDark: Boolean
})

defineEmits(['toggleDark'])
</script>

<template>
  <nav class="fixed top-0 w-full bg-white dark:bg-gray-800 shadow-md transition-colors duration-200 z-50">
    <div class="container mx-auto px-4">
      <div class="flex items-center justify-between h-16">
        <div class="flex items-center">
          <a href="/" class="text-xl font-bold text-gray-800 dark:text-white">
            Hazard Explorer
          </a>
        </div>
        
        <div class="flex items-center space-x-4">
          <a href="#" class="text-gray-600 dark:text-gray-300 hover:text-gray-800 dark:hover:text-white">
            Home
          </a>
          <a href="#" class="text-gray-600 dark:text-gray-300 hover:text-gray-800 dark:hover:text-white">
            About
          </a>
          <a href="#" class="text-gray-600 dark:text-gray-300 hover:text-gray-800 dark:hover:text-white">
            Contact
          </a>
          <button
            @click="$emit('toggleDark')"
            class="p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-gray-700 transition-colors duration-200"
          >
            <SunIcon v-if="isDark" class="h-5 w-5 text-gray-300" />
            <MoonIcon v-else class="h-5 w-5 text-gray-600" />
          </button>
        </div>
      </div>
    </div>
  </nav>
</template>