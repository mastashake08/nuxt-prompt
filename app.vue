<template>
  <div>
    <h1>Prompt API Example with Progress Bar</h1>
    <input v-model="prompt" placeholder="Enter a prompt" />
    <button @click="sendPrompt">Send Prompt</button>
    <p>Response: {{ response }}</p>
    <div v-if="downloading">
      <p>Downloading model: {{ downloadProgress }}%</p>
      <progress :value="downloadProgress" max="100"></progress>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const prompt = ref('')
const response = ref('')
const downloading = ref(false)
const downloadProgress = ref(0)

async function sendPrompt() {
  try {
    downloading.value = true

    const session = await ai.assistant.create({
      monitor: (m) => {
        console.log(m)
        m.addEventListener('downloadprogress', (e) => {
          console.log(e)
          downloadProgress.value = Math.round((e.loaded / e.total) * 100)
          if (downloadProgress.value === 100) {
            downloading.value = false
          }
        })
      }
    })

    response.value = await session.prompt(prompt.value)
  } catch (error) {
    console.error('Error:', error)
    downloading.value = false
  }
}
</script>
