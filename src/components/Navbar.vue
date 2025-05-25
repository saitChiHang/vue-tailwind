<script setup>
import { MoonIcon, SunIcon, MapIcon } from '@heroicons/vue/24/outline'

defineProps({
  isDark: Boolean
})

defineEmits(['toggleDark'])
</script>

<template>
  <nav class="fixed top-0 w-full bg-white dark:bg-gray-800 shadow-md transition-colors duration-200 z-50">
    <div class="container mx-auto px-4">
      <div class="flex items-center justify-between h-16">
        <div class="flex items-center space-x-2">
          <MapIcon class="h-6 w-6 text-blue-600 dark:text-blue-400" />
          <a href="/" class="text-xl font-bold text-gray-800 dark:text-white">
            Hazard Explorer
          </a>
        </div>
        
        <div class="flex items-center space-x-6">
          <div class="relative">
            <input 
              type="text" 
              placeholder="Search..." 
              class="w-64 px-4 py-2 rounded-lg bg-gray-100 dark:bg-gray-700 text-gray-800 dark:text-white border border-transparent focus:border-blue-500 dark:focus:border-blue-400 focus:outline-none"
            />
          </div>
          <a href="#" class="text-gray-600 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400 transition-colors duration-200 font-medium">
            Home
          </a>
          <a href="#" class="text-gray-600 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400 transition-colors duration-200 font-medium">
            About
          </a>
          <a href="#" class="text-gray-600 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400 transition-colors duration-200 font-medium">
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