<script setup>
import { ref, onMounted } from 'vue'
import MapComponent from './components/MapComponent.vue'
import Navbar from './components/Navbar.vue'
import BottomPanel from './components/BottomPanel.vue'

const isDark = ref(false)
const mapRef = ref(null)

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

const handlePanelToggle = () => {
  setTimeout(() => {
    mapRef.value?.invalidateMapSize();
  }, 300); // Delay to allow panel animation to complete
}
</script>

<template>
  <div class="h-screen flex flex-col bg-white dark:bg-gray-900 transition-colors duration-200">
    <Navbar :isDark="isDark" @toggle-dark="toggleDark" class="z-50" />
    <div class="flex-1 relative mt-16">
      <MapComponent ref="mapRef" />
    </div>
    <BottomPanel class="z-40" @panel-toggle="handlePanelToggle" />
  </div>
</template>