<script setup>
import { ref, watch, nextTick } from 'vue'
import { LMarker, LPopup } from '@vue-leaflet/vue-leaflet'

const props = defineProps({
  location: {
    type: Object,
    required: true
  },
  isActive: {
    type: Boolean,
    default: false
  }
})

const markerRef = ref(null)

watch(() => props.isActive, async (active) => {
  if (active && markerRef.value) {
    await nextTick()
    const marker = markerRef.value.leafletObject
    if (marker) {
      marker.openPopup()
    }
  }
})
</script>

<template>
  <l-marker ref="markerRef" :lat-lng="[location.lat, location.lng]">
    <!-- Default leaf icon is fine, but popup is styled -->
    <l-popup>
      <div class="min-w-[180px] p-1">
        <h3 class="text-gray-900 font-semibold text-[16px] mb-1 tracking-tight">{{ location.name }}</h3>
        <p class="text-gray-500 text-[13px] leading-snug mb-3">{{ location.desc }}</p>
        <div class="flex items-center gap-2 pt-3 border-t border-gray-100">
          <span 
            class="text-[11px] px-2.5 py-0.5 rounded-full font-medium"
            :class="[
              location.status === 'Active' ? 'text-green-700 bg-green-100' : 
              location.status === 'Monitoring' ? 'text-amber-700 bg-amber-100' : 
              'text-red-700 bg-red-100'
            ]"
          >
            {{ location.status }}
          </span>
          <span class="text-[12px] text-gray-400 font-medium ml-auto">{{ location.coords }}</span>
        </div>
      </div>
    </l-popup>
  </l-marker>
</template>
