<script setup>
import { ref, onMounted } from 'vue';
import YouTubePlayerPlus from 'youtube-player-plus'

const url = ref()
const player = ref()
// const isPlaying = ref(false)
const videosIds = ref([])
const videos = ref([])
const timer = ref()
const percent = ref(0)
const seek = ref()

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
  // isPlaying.value = true
  player.value.on('timeupdate', (e) => {
    percent.value = parseInt(Math.ceil(player.value.percentageWatched*100))
    timer.value = parseInt(player.value.getCurrentTime())
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

function videoSeek(){
  player.value.seek(seek.value)
  seek.value = ''
}

function backward(){
  const time = player.value.getCurrentTime()
  player.value.seek(time - 10)
}
function forward(){
  const time = player.value.getCurrentTime()
  player.value.seek(time + 10)
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
      <div class="relative aspect-video" v-show="player?.getState() !== 'unstarted'">
        <div id="player"></div>
        <span class="absolute left-0 right-0 bottom-0 h-1 bg-slate-500 overflow-hidden">
          <span :style="percent ? `width: ${percent}%;`:'width: 0;'" class="relative block h-2 bg-red-700"></span>
        </span>
      </div>
      <div class="relative flex gap-4 justify-center py-4" v-show="player?.getState() !== 'unstarted'">
        <button @click="backward" class="px-4 w-20"> &lt;&lt; </button>
        <input type="number" v-model="seek" @keypress.enter="videoSeek" class="w-24 border border-slate-400 shadow-xs">
        <button @click="forward" class="px-4 w-20"> &gt;&gt; </button>
        <span class="absolute right-24 flex items-center justify-center w-20 h-10 text-xs text-white bg-slate-700 border border-slate-900 rounded shadow-xs">{{ timer }}</span>
        <span class="absolute right-0 flex items-center justify-center w-20 h-10 text-xs text-white bg-slate-700 border border-slate-900 rounded shadow-xs">{{ Math.floor(player?.getDuration()) }}</span>
      </div>
      <div class="grid grid-cols-2 md:grid-cols-4 gap-6 mt-4">
        <div v-for="(video, idx) in videos" :key="idx" class="relative">
          <button @click="remove(video.id)" class="absolute right-2 top-2 bg-red-700">X</button>
          <img :src="video.thumbnail" @click="update(video.id)" class="border border-gray-500 shadow cursor-pointer">
        </div>
      </div>
  </div>
</template>