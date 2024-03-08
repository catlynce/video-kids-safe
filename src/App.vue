<script setup>
import { ref, onMounted } from 'vue';
import YouTubePlayerPlus from 'youtube-player-plus'

const url = ref()
const player = ref()
const videosIds = ref(['wJZm7WPctK8', 'pZ5NxMN88Jg'])
const videos = ref([])

onMounted(() => {
  // create localStorage
  const Db = localStorage.getItem('vids')
  if ( ! Db ){
    localStorage.setItem('vids', videosIds.value)
  }

  buildList()

  const options = {
    autoplay: 1,
    controls: false,
    annotations: false,
    modestBranding: true,
    relatedVideos: false,
  }
  player.value = new YouTubePlayerPlus('#player', options)
  player.value.load('wJZm7WPctK8', true)
})

async function add(){
  console.log(url.value)
  let videoId

  if ( url.value.includes("youtu.be") ){
    videoId = url.value.split("youtu.be/")[1]
  }

  if ( url.value.includes("youtube.com") ){
    videoId = url.value.split("v=")[1]
  }
  console.log(videoId)
  url.value = ''
  videosIds.value.push(videoId)
  await localStorage.setItem('vids', videosIds.value)
  buildList()
}

async function remove(videoId){
  let videos = await localStorage.getItem('vids')
  videos = videos.split(',')
  videos = videos.filter(item => item !== videoId)
  await localStorage.setItem('vids', videos)
  buildList()
}

function update(videoId){
  console.log(videoId)
  player.value.load(videoId, true)
}

async function buildList(){
  videos.value = []
  const ids = await localStorage.getItem('vids')
  if ( ids.trim().length !== 0 ){
    let vids = ids.split(',').reverse()
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
      </div>
      <div class="grid grid-cols-2 md:grid-cols-4 gap-6 mt-4">
        <div v-for="(video, idx) in videos" :key="idx" class="relative">
          <button @click="remove(video.id)" class="absolute right-2 top-2 bg-red-700">X</button>
          <img :src="video.thumbnail" @click="update(video.id)" class="border border-gray-500 shadow cursor-pointer">
        </div>
      </div>
  </div>
</template>