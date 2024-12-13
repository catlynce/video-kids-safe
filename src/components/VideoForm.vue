<script setup>
import { ref } from 'vue'

const url = ref('')
const emit = defineEmits(['add-video'])

function handleSubmit() {
  emit('add-video', url.value)
  url.value = ''
}
</script>

<template>
  <form @submit.prevent="handleSubmit" class="flex justify-between mb-4">
    <input
      type="text"
      id="url"
      v-model="url"
      class="flex-1 mr-4 p-2 bg-gray-100 border border-gray-700"
      placeholder="Enter YouTube URL"
    >
    <button>Guardar</button>
  </form>
</template>
