<script setup>
import { ref, onMounted } from 'vue'
import VideoForm from './components/VideoForm.vue'
import VideoPlayer from './components/VideoPlayer.vue'
import VideoGrid from './components/VideoGrid.vue'

const videosIds = ref([])
const videos = ref([])
const currentVideoId = ref(null)

onMounted(() => {
  // create localStorage
  const Db = localStorage.getItem('vids')
  if (!Db) {
    Promise.resolve(setLocalStorage())
  }
  buildList()
})

async function handleAddVideo(url) {
  try {
    // Handle different YouTube URL formats
    const videoId = extractYouTubeId(url)
    if (!videoId) {
      console.error('Invalid YouTube URL')
      return
    }

    videosIds.value.push(videoId)
    await setLocalStorage()
    buildList()
  } catch (error) {
    console.error('Error adding video:', error)
  }
}

function extractYouTubeId(url) {
  // Handle different YouTube URL formats
  const patterns = [
    /(?:https?:\/\/)?(?:www\.)?youtube\.com\/watch\?v=([^&]+)/,
    /(?:https?:\/\/)?(?:www\.)?youtube\.com\/embed\/([^?]+)/,
    /(?:https?:\/\/)?(?:www\.)?youtu\.be\/([^?]+)/
  ]

  for (const pattern of patterns) {
    const match = url.match(pattern)
    if (match) return match[1]
  }

  return null
}

async function setLocalStorage() {
  return localStorage.setItem('vids', JSON.stringify(videosIds.value))
}

async function handleRemoveVideo(videoId) {
  videosIds.value = videosIds.value.filter(item => item !== videoId)
  await setLocalStorage()
  buildList()
}

function handleSelectVideo(videoId) {
  currentVideoId.value = videoId
}

async function buildList() {
  videos.value = []
  const ids = await JSON.parse(localStorage.getItem('vids'))
  if (ids.length !== 0) {
    let vids = ids.reverse()
    vids.forEach(video => {
      const thumbnail = `https://img.youtube.com/vi/${video}/mqdefault.jpg`
      videos.value.push({ id: video, thumbnail })
    })
  }
}
</script>

<template>
  <main>
    <VideoForm @add-video="handleAddVideo" />
    <VideoPlayer :video-id="currentVideoId" />
    <VideoGrid 
      :videos="videos" 
      @remove-video="handleRemoveVideo"
      @select-video="handleSelectVideo"
    />
  </main>
</template>