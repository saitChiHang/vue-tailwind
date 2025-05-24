<script setup>
import { onMounted, onUnmounted } from 'vue';
import 'leaflet/dist/leaflet.css';
import L from 'leaflet';

let map = null;

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
});

onUnmounted(() => {
  if (map) {
    map.remove();
  }
});
</script>

<template>
  <div id="map" class="absolute inset-0"></div>
</template>