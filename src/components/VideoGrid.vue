<script setup>
defineProps({
  videos: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['remove-video', 'select-video'])
</script>

<template>
  <div class="grid grid-cols-2 md:grid-cols-4 gap-6 mt-4">
    <div v-for="(video, idx) in videos" :key="idx" class="relative">
      <button 
        @click="emit('remove-video', video.id)" 
        class="absolute right-2 top-2 bg-red-700"
      >
        X
      </button>
      <img 
        :src="video.thumbnail" 
        @click="emit('select-video', video.id)" 
        class="border border-gray-500 shadow cursor-pointer"
      >
    </div>
  </div>
</template>
