<script setup>
import { ref, onMounted } from 'vue';
import YouTubePlayerPlus from 'youtube-player-plus'

const url = ref()
const player = ref()
const videosIds = ref(['wJZm7WPctK8', 'pZ5NxMN88Jg'])
const videos = ref([])
const percent = ref(0)

onMounted(() => {
  // create localStorage
  const Db = localStorage.getItem('vids')
  if ( ! Db ){
    Promise.resolve(setLocalStorage())
  }

  buildList()

  const options = {
    autoplay: 0,
    controls: false,
    annotations: false,
    modestBranding: true,
    relatedVideos: false,
  }
  player.value = new YouTubePlayerPlus('#player', options)
  // player.value.load('wJZm7WPctK8', true)
})

async function add(){
  console.log(url.value)
  let link = new URL(url.value)
  let params = link.searchParams
  let videoId = params.get('v') ?? null

  url.value = ''
  videosIds.value.push(videoId)

  await setLocalStorage()

  buildList()
}

async function setLocalStorage(){
  return localStorage.setItem('vids', JSON.stringify(videosIds.value))
}

async function remove(videoId){
  videosIds.value = videosIds.value.filter(item => item !== videoId)
  await setLocalStorage()
  buildList()
}

function update(videoId){
  console.log(videoId)
  player.value.load(videoId, true)
  player.value.on('timeupdate', (e) => {
    percent.value = parseInt(Math.ceil(player.value.percentageWatched*100))
  })
}

async function buildList(){
  videos.value = []
  const ids = await JSON.parse(localStorage.getItem('vids'))
  if ( ids.length !== 0 ){
    let vids = ids.reverse()
    vids.forEach(video => {
      const thumbnail = `https://img.youtube.com/vi/${video}/mqdefault.jpg`
      videos.value.push({ id: video, thumbnail })
    })
  }
}
</script>

<template>
  <div class="">
      <form @submit.prevent="add" class="flex justify-between mb-4">
        <input
          type="text"
          id="url"
          v-model="url"
          class="flex-1 mr-4 p-2 bg-gray-100 border border-gray-700"
        >
        <button>Guardar</button>
      </form>
      <div class="relative aspect-video">
        <div id="player"></div>
        <span class="absolute left-0 right-0 bottom-0 h-1 bg-slate-500 overflow-hidden">
          <span :style="percent ? `width: ${percent}%;`:'width: 0;'" class="relative block h-2 bg-red-700"></span>
        </span>
      </div>
      <div class="grid grid-cols-2 md:grid-cols-4 gap-6 mt-4">
        <div v-for="(video, idx) in videos" :key="idx" class="relative">
          <button @click="remove(video.id)" class="absolute right-2 top-2 bg-red-700">X</button>
          <img :src="video.thumbnail" @click="update(video.id)" class="border border-gray-500 shadow cursor-pointer">
        </div>
      </div>
  </div>
</template>