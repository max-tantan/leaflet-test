<script setup>
import { ref } from 'vue'
import SidePanel from './components/SidePanel.vue'
import BaseMap from './components/BaseMap.vue'
import { locations } from './data/locations.js'
import '@fontsource/inter'

const activeLocation = ref(null)
const isSidebarOpen = ref(true)

const handleLocationSelect = (loc) => {
  activeLocation.value = loc
}

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value
}
</script>

<template>
  <div class="flex h-screen w-full bg-[#f8fafc] text-gray-900 font-sans antialiased overflow-hidden relative">
    
    <!-- Sidebar Container -->
    <div 
      class="h-full z-50 shrink-0 transition-all duration-500 ease-in-out absolute md:relative shadow-[5px_0_25px_rgba(0,0,0,0.05)]"
      :class="isSidebarOpen ? 'w-[320px] translate-x-0' : 'w-[320px] -translate-x-full'"
    >
      <SidePanel 
        :locations="locations" 
        :active-location="activeLocation"
        @select="handleLocationSelect" 
        @toggle="toggleSidebar"
      />
    </div>

    <!-- Toggle Button (Visible when sidebar is closed) -->
    <button 
      v-if="!isSidebarOpen"
      @click="toggleSidebar"
      class="absolute top-6 left-6 z-[1002] bg-white/90 backdrop-blur-md border border-gray-200 p-3 rounded-xl shadow-sm hover:bg-gray-50 transition-all text-gray-600 hover:text-gray-900 hover:scale-105"
    >
      <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
        <path stroke-linecap="round" stroke-linejoin="round" d="M13 5l7 7-7 7M5 5l7 7-7 7" />
      </svg>
    </button>

    <!-- Main Content Area -->
    <main class="flex-1 relative h-full bg-[#f8fafc] transition-all duration-500">
      <BaseMap :locations="locations" :active-location="activeLocation" />
    </main>
  </div>
</template>

<style>
body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  margin: 0;
  padding: 0;
}

/* Custom Scrollbar - iOS style invisible until scroll */
.custom-scrollbar::-webkit-scrollbar {
  width: 6px;
}
.custom-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}
.custom-scrollbar::-webkit-scrollbar-thumb {
  background: rgba(0, 0, 0, 0.15);
  border-radius: 10px;
}
.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background: rgba(0, 0, 0, 0.3);
}
</style>
