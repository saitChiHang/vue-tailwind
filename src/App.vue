<script setup>
import { ref, onMounted } from 'vue'
import MapComponent from './components/MapComponent.vue'
import Navbar from './components/Navbar.vue'

const isDark = ref(false)

onMounted(() => {
  if (localStorage.theme === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }
})

const toggleDark = () => {
  isDark.value = !isDark.value
  if (isDark.value) {
    document.documentElement.classList.add('dark')
    localStorage.theme = 'dark'
  } else {
    document.documentElement.classList.remove('dark')
    localStorage.theme = 'light'
  }
}
</script>

<template>
  <div class="min-h-screen bg-white dark:bg-gray-900 transition-colors duration-200">
    <Navbar :isDark="isDark" @toggle-dark="toggleDark" />
    <div class="container mx-auto px-4 pt-20">
      <MapComponent />
    </div>
  </div>
</template>