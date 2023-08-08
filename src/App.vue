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
  const videoId = url.value.split("v=")[1]
  // .substring(0, 11)
  console.log(videoId)
  url.value = ''
  videosIds.value.push(videoId)
  await localStorage.setItem('vids', videosIds.value)
  buildList()
}

function update(videoId){
  console.log(videoId)
  player.value.load(videoId, true)
}

async function buildList(){
  videos.value = []
  const vids = await localStorage.getItem('vids').split(',')
  vids.forEach(video => {
    const thumbnail = `https://img.youtube.com/vi/${video}/mqdefault.jpg`
    videos.value.push({ id: video, thumbnail })
  })

}
</script>

<template>
  <div>
      <form @submit.prevent="add" class="url">
        <input type="text" id="url" v-model="url">
        <button>Guardar</button>
      </form>
      <div class="player-wrapper">
        <div id="player"></div>
      </div>
      <div class="list">
        <div v-for="(video, idx) in videos" :key="idx">
          <img :src="video.thumbnail" @click="update(video.id)">
        </div>
      </div>
  </div>
</template>

<style scoped>
.url {
  display: flex;
  justify-content: space-between;
  margin: 0 0 20px 0;
}
.url input {
  flex: 1;
  margin-right: 20px;
}

.player-wrapper {
  height: 0;
  position: relative;
  padding-bottom: 56.25%;
}

.list {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-top: 20px;
}

.list img {
  width: 100%;
  height: auto;
  border: 1px solid;
}

.list img {
  cursor: pointer;
}
</style>
