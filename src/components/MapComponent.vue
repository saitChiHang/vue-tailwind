<script setup>
import { onMounted, onUnmounted, watch } from 'vue';
import 'leaflet/dist/leaflet.css';
import L from 'leaflet';

let map = null;

const invalidateMapSize = () => {
  if (map) {
    setTimeout(() => {
      map.invalidateSize();
    }, 200); // Small delay to ensure DOM has updated
  }
};

onMounted(() => {
  // Create map instance
  map = L.map('map').setView([51.505, -0.09], 13);

  // Add tile layer
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
  }).addTo(map);

  // Add a marker
  L.marker([51.505, -0.09])
    .addTo(map)
    .bindPopup('A sample marker!')
    .openPopup();

  // Initial size check
  invalidateMapSize();
});

onUnmounted(() => {
  if (map) {
    map.remove();
  }
});

// Export method to be called from parent
defineExpose({ invalidateMapSize });
</script>

<template>
  <div id="map" class="absolute inset-0"></div>
</template>