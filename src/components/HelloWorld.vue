<script setup>
defineProps({
  msg: String,
})
</script>

<template>
  <h1 class="text-3xl font-bold text-gray-800 dark:text-white">
    {{ msg }}
  </h1>
</template>