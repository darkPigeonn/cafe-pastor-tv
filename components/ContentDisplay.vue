<template>
  <div>
    <div class="d-flex align-items-center justify-content-center vh-100">
      <div v-if="loading" class="text-center">
        <span class="spinner-border" role="status" aria-hidden="true"></span>
        <span class="sr-only">Loading...</span>
      </div>
      <div v-else>
        <div v-if="content">
          <div v-if="!hasInteracted" class="text-center">
            <button class="btn btn-primary mb-4" @click="startContent">Start Content</button>
          </div>
          <div v-else>
            <Carousel2 :images="content.images" />
          </div>
        </div>
        <div v-else>
          <p>No content available.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, nextTick } from 'vue'
import { useNuxtApp } from 'nuxt/app'
import { collection, query, orderBy, limit, getDocs } from 'firebase/firestore'

const nuxtApp = useNuxtApp()
const firestore = nuxtApp.$firebaseFirestore

const content = ref(null)
const loading = ref(true)
const audioElements = ref([])
const hasInteracted = ref(false)

const fetchContent = async () => {
  try {
    const q = query(collection(firestore, 'contents'), orderBy('datePublish', 'desc'), limit(1))
    const querySnapshot = await getDocs(q)
    querySnapshot.forEach((doc) => {
      content.value = doc.data()
      console.log(content.value)
    })
    loading.value = false
  } catch (error) {
    console.error('Error fetching content:', error)
    loading.value = false
  }
}

const playNextAudio = (currentIndex) => {
  const nextIndex = currentIndex + 1
  if (nextIndex < audioElements.value.length) {
    audioElements.value[nextIndex].play()
  }
}

const startContent = () => {
  hasInteracted.value = true
  nextTick(() => {
    if (audioElements.value.length > 0) {
      audioElements.value[0].play()
    }
  })
}

onMounted(() => {
  fetchContent()
})
</script>

<style scoped>
.vh-100 {
  height: 100vh;
}
</style>
