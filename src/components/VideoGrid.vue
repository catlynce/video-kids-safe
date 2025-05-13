<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
  videos: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['remove-video', 'select-video', 'open-video'])

onMounted(() => {
  if (props.videos.length > 0) {
    fetchVideoTitles()
  }
})

// Watch for changes in the videos array
watch(() => props.videos, (newVideos) => {
  if (newVideos.length > 0) {
    fetchVideoTitles()
  }
}, { deep: true })

async function fetchVideoTitles() {
  // For each video in the array, fetch its title if we don't already have it
  props.videos.forEach(async (video) => {
    if (!videoTitles.value[video.id]) {
      try {
        // Simple approach to get title from oEmbed endpoint
        const response = await fetch(`https://www.youtube.com/oembed?url=https://www.youtube.com/watch?v=${video.id}&format=json`)
        const data = await response.json()

        video.title = data.title
        video.author_url = data.author_url
      } catch (error) {
        console.error('Error fetching video title:', error)
        video.title = `Video ${video.id}`  // Fallback title
      }
    }
  })
}

function openVideo(videoId) {
  window.open(`https://www.youtube.com/watch?v=${videoId}`, '_blank').focus();
}

function openChannel(author_url) {
  window.open(`${author_url}/videos`, '_blank').focus();
}

</script>

<template>
  <div class="grid grid-cols-2 md:grid-cols-4 gap-6 mt-4">
    <div v-for="(video, idx) in videos" :key="idx" class="relative flex flex-col">
      <button 
        @click="emit('remove-video', video.id)" 
        class="absolute right-2 top-8 bg-red-700 z-10"
      >
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" class="w-4 h-4">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
        </svg>
      </button>
      <button 
        @click="openVideo(video.id)" 
        class="absolute right-2 top-16 bg-blue-700 z-10"
      >
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" class="w-4 h-4">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
        </svg>
      </button>
      <button
        @click="openChannel(video.author_url)"
        class="absolute right-2 top-24 bg-green-700 z-10"
        :title="video.author_url"
      >
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" class="w-4 h-4">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
        </svg>
      </button>

      <!-- Video title display above thumbnail -->
      <div class="mb-1 text-sm font-medium truncate w-full">
        {{ video.title || 'Loading title...' }}
      </div>
      <img 
        :src="video.thumbnail" 
        @click="emit('select-video', video.id)" 
        class="border border-gray-500 shadow cursor-pointer"
      >
    </div>
  </div>
</template>
