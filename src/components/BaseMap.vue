<script setup>
import { ref, watch } from 'vue'
import { LMap, LTileLayer } from '@vue-leaflet/vue-leaflet'
import MapMarker from './MapMarker.vue'
import 'leaflet/dist/leaflet.css'

const props = defineProps({
  locations: {
    type: Array,
    required: true
  },
  activeLocation: {
    type: Object,
    default: null
  }
})

const zoom = ref(5)
const center = ref([-2.5489, 118.0149]) // Center of Indonesia
const mapRef = ref(null)

// Watch activeLocation prop and flyTo coordinate
watch(() => props.activeLocation, (newLoc) => {
  if (newLoc && mapRef.value) {
    const map = mapRef.value.leafletObject
    if (map) {
      map.flyTo([newLoc.lat, newLoc.lng], 13, {
        duration: 1.5,
        easeLinearity: 0.25
      })
    }
  }
})
</script>

<template>
  <div class="h-full w-full relative bg-[#f8fafc]">
    <!-- Map Container -->
    <l-map 
      ref="mapRef"
      v-model:zoom="zoom"
      v-model:center="center"
      :useGlobalLeaflet="false" 
      :min-zoom="3"
      :max-bounds="[[-90, -180], [90, 180]]"
      :max-bounds-viscosity="1.0"
      class="h-full w-full z-0"
      :options="{ zoomControl: false }"
    >
      <!-- Clean Light Theme Basemap (CartoDB Voyager) -->
      <l-tile-layer
        url="https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png"
        layer-type="base"
        name="Voyager Light"
        :no-wrap="true"
        attribution="&copy; <a href='https://www.openstreetmap.org/copyright'>OpenStreetMap</a> &copy; <a href='https://carto.com/attributions'>CARTO</a>"
      />
      
      <!-- Custom Markers Component -->
      <MapMarker 
        v-for="loc in locations" 
        :key="loc.id" 
        :location="loc" 
        :is-active="activeLocation?.id === loc.id"
      />
    </l-map>
  </div>
</template>

<style>
/* Leaflet Container Minimal Overrides */
.leaflet-container {
  background: #f8fafc !important;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif !important;
}

/* Customizing Leaflet Popup for iOS style */
.leaflet-popup-content-wrapper {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  color: #111827;
  border-radius: 16px;
  box-shadow: 0 10px 40px -10px rgba(0, 0, 0, 0.15), 0 0 0 1px rgba(0,0,0,0.05);
  padding: 0;
}
.leaflet-popup-content {
  margin: 14px 16px;
}
.leaflet-popup-tip {
  background: rgba(255, 255, 255, 0.95);
  box-shadow: 0 10px 40px -10px rgba(0, 0, 0, 0.15);
}
.leaflet-container a.leaflet-popup-close-button {
  color: #9ca3af;
  padding: 10px;
}
.leaflet-container a.leaflet-popup-close-button:hover {
  color: #4b5563;
}
</style>
