<script setup>
import { ref, watch, computed } from 'vue'
import { LMap, LTileLayer, LPolyline } from '@vue-leaflet/vue-leaflet'
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

// Focus map on Mediterranean / Middle East for Alexander's Route
const zoom = ref(5)
const center = ref([35.0, 33.0]) 
const mapRef = ref(null)

// Map Styles Configuration
const mapStyles = {
  light: {
    name: 'Light',
    url: 'https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png',
    attribution: '&copy; OpenStreetMap &copy; CARTO'
  },
  dark: {
    name: 'Dark',
    url: 'https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png',
    attribution: '&copy; OpenStreetMap &copy; CARTO'
  },
  satellite: {
    name: 'Satellite',
    url: 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',
    attribution: 'Tiles &copy; Esri'
  }
}
const activeStyle = ref('light')

// Extract coordinates in chronological order for the route line
const routeCoordinates = computed(() => {
  return props.locations.map(loc => [loc.lat, loc.lng])
})

// Watch activeLocation prop and flyTo coordinate
watch(() => props.activeLocation, (newLoc) => {
  if (newLoc && mapRef.value) {
    const map = mapRef.value.leafletObject
    if (map) {
      map.flyTo([newLoc.lat, newLoc.lng], 7, { // zoom adjusted to 7 for better historical view
        duration: 1.5,
        easeLinearity: 0.25
      })
    }
  }
})
</script>

<template>
  <div class="h-full w-full relative bg-[#f8fafc]">
    
    <!-- Map Style Selector (Floating Top Right) -->
    <div class="absolute top-6 right-6 z-[1001]">
      <div class="bg-white/90 backdrop-blur-md rounded-2xl shadow-sm border border-gray-200 overflow-hidden flex flex-col p-1.5 gap-1">
        <button 
          v-for="(style, key) in mapStyles" 
          :key="key"
          @click="activeStyle = key"
          class="px-4 py-2 text-[13px] font-medium rounded-xl transition-all text-left flex items-center gap-2"
          :class="activeStyle === key ? 'bg-blue-50 text-blue-600 shadow-[0_1px_3px_rgba(0,0,0,0.05)]' : 'text-gray-600 hover:bg-gray-100 hover:text-gray-900'"
        >
          <div class="w-2 h-2 rounded-full" :class="key === 'light' ? 'bg-gray-300' : key === 'dark' ? 'bg-gray-800' : 'bg-green-500'"></div>
          {{ style.name }}
        </button>
      </div>
    </div>

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
      <!-- Dynamic Basemap Layer -->
      <l-tile-layer
        :url="mapStyles[activeStyle].url"
        layer-type="base"
        :name="mapStyles[activeStyle].name"
        :no-wrap="true"
        :attribution="mapStyles[activeStyle].attribution"
      />
      
      <!-- Routing Polyline -->
      <l-polyline 
        :lat-lngs="routeCoordinates" 
        color="#3b82f6" 
        :weight="3"
        :opacity="0.8"
        dash-array="8, 8"
        line-cap="round"
        line-join="round"
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
