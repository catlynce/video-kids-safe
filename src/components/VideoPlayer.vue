<script setup>
import { ref, onMounted, watch } from 'vue'
import YouTubePlayerPlus from 'youtube-player-plus'

const props = defineProps({
  videoId: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['time-update'])
const player = ref(null)
const percent = ref(0)
const timer = ref(0)
const seek = ref('')
const playerContainer = ref(null)

onMounted(() => {
  initializePlayer()
})

function initializePlayer() {
  if (!playerContainer.value) return

  const options = {
    autoplay: 0,
    controls: false,
    annotations: false,
    modestBranding: true,
    relatedVideos: false,
  }

  player.value = new YouTubePlayerPlus(playerContainer.value, options)
  
  if (props.videoId) {
    loadVideo(props.videoId)
  }
}

function videoSeek() {
  if (!player.value) return
  player.value.seek(seek.value)
  seek.value = ''
}

function backward() {
  if (!player.value) return
  const time = player.value.getCurrentTime()
  player.value.seek(time - 10)
}

function forward() {
  if (!player.value) return
  const time = player.value.getCurrentTime()
  player.value.seek(time + 10)
}

function loadVideo(videoId) {
  if (videoId && player.value) {
    player.value.load(videoId, true)
    player.value.on('timeupdate', (e) => {
      percent.value = parseInt(Math.ceil(player.value.percentageWatched * 100))
      timer.value = parseInt(player.value.getCurrentTime())
      emit('time-update', timer.value)
    })
  }
}

// Watch for videoId changes
watch(() => props.videoId, (newId) => {
  if (newId) {
    loadVideo(newId)
  }
})
</script>

<template>
  <div>
    <div class="relative aspect-video" v-show="player?.getState() !== 'unstarted'">
      <div ref="playerContainer" class="w-full h-full"></div>
      <span class="absolute left-0 right-0 bottom-0 h-1 bg-slate-500 overflow-hidden">
        <span :style="percent ? `width: ${percent}%;` : 'width: 0;'" class="relative block h-2 bg-red-700"></span>
      </span>
    </div>
    <div class="relative flex gap-4 justify-center py-4" v-show="player?.getState() !== 'unstarted'">
      <button @click="backward" class="px-4 w-20">&lt;&lt;</button>
      <input type="number" v-model="seek" @keypress.enter="videoSeek" class="w-24 border border-slate-400 shadow-xs">
      <button @click="forward" class="px-4 w-20">&gt;&gt;</button>
      <span class="absolute right-24 flex items-center justify-center w-20 h-10 text-xs text-white bg-slate-700 border border-slate-900 rounded shadow-xs">
        {{ timer }}
      </span>
      <span class="absolute right-0 flex items-center justify-center w-20 h-10 text-xs text-white bg-slate-700 border border-slate-900 rounded shadow-xs">
        {{ Math.floor(player?.getDuration()) }}
      </span>
    </div>
  </div>
</template>
