<script setup>
import { ref, computed } from 'vue'

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

defineEmits(['select', 'toggle'])

const searchQuery = ref('')

const filteredLocations = computed(() => {
  if (!searchQuery.value) return props.locations
  const query = searchQuery.value.toLowerCase()
  return props.locations.filter(loc => 
    loc.name.toLowerCase().includes(query) || 
    loc.status.toLowerCase().includes(query) ||
    loc.coords.toLowerCase().includes(query)
  )
})
</script>

<template>
  <aside class="w-full h-full bg-white/90 backdrop-blur-xl border-r border-gray-200 flex flex-col relative z-50">
    <!-- Sidebar Header -->
    <div class="px-6 py-8 flex justify-between items-center">
      <h2 class="text-[28px] font-bold text-gray-900 tracking-tight">Locations</h2>
      <button @click="$emit('toggle')" class="text-gray-400 hover:text-gray-700 transition-colors p-2 hover:bg-gray-100 rounded-full">
        <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M11 19l-7-7 7-7m8 14l-7-7 7-7" />
        </svg>
      </button>
    </div>

    <!-- Navigation / Filters -->
    <div class="px-6 pb-4">
      <div class="relative group">
        <input 
          v-model="searchQuery"
          type="text" 
          placeholder="Search..." 
          class="w-full bg-gray-100/80 border border-transparent rounded-[14px] pl-10 pr-4 py-2.5 text-[15px] text-gray-900 focus:outline-none focus:bg-white focus:border-blue-500/30 focus:ring-4 focus:ring-blue-500/10 transition-all placeholder:text-gray-500"
        >
        <svg xmlns="http://www.w3.org/2000/svg" class="absolute left-3.5 top-3 w-4 h-4 text-gray-400 group-focus-within:text-blue-500 transition-colors" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
          <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
      </div>
    </div>

    <!-- Scrollable List -->
    <div class="flex-1 overflow-y-auto custom-scrollbar px-4 pb-6 pt-2">
      <TransitionGroup name="list" tag="div" class="space-y-2 relative">
        <div 
          v-for="loc in filteredLocations" 
          :key="loc.id"
          @click="$emit('select', loc)"
          class="p-4 rounded-[16px] transition-all duration-300 cursor-pointer border"
          :class="[
            activeLocation?.id === loc.id 
              ? 'bg-blue-50 border-blue-200 shadow-sm' 
              : 'bg-white border-transparent hover:bg-gray-50 active:bg-gray-100 hover:shadow-[0_2px_8px_rgba(0,0,0,0.04)]'
          ]"
        >
          <div class="flex justify-between items-start mb-1">
            <h4 
              class="font-semibold text-[16px] tracking-tight transition-colors"
              :class="activeLocation?.id === loc.id ? 'text-blue-600' : 'text-gray-900'"
            >
              {{ loc.name }}
            </h4>
            <span 
              class="text-[11px] px-2 py-0.5 rounded-full font-medium"
              :class="[
                loc.status === 'Victory' ? 'text-green-700 bg-green-100' : 
                loc.status === 'End' ? 'text-red-700 bg-red-100' : 
                loc.status === 'Start' ? 'text-blue-700 bg-blue-100' : 
                'text-indigo-700 bg-indigo-100'
              ]"
            >
              {{ loc.status }}
            </span>
          </div>
          <p class="text-[13px] text-gray-500 leading-snug">{{ loc.desc }}</p>
          <div class="mt-3 text-[12px] font-medium text-gray-400 flex items-center gap-1.5">
            <svg xmlns="http://www.w3.org/2000/svg" class="w-3.5 h-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
            </svg>
            {{ loc.coords }}
          </div>
        </div>
      </TransitionGroup>
      
      <div v-if="filteredLocations.length === 0" class="text-center py-10 text-gray-400 text-sm">
        No locations found.
      </div>
    </div>
  </aside>
</template>

<style scoped>
.list-enter-active,
.list-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(10px) scale(0.98);
}
</style>
