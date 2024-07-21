<template>
  <div> 
    <div v-if="loading"></div>
    <Carousel2 :images="content.images" v-else />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { doc, onSnapshot, collection, query, orderBy, limit, getDocs  } from 'firebase/firestore';

const nuxtApp = useNuxtApp()
const db = nuxtApp.$firebaseFirestore
const firestore = nuxtApp.$firebaseFirestore
const currentImage = ref('');
const currentMusic = ref('');
const hasInteracted = ref(false);


const loading = ref(true)
const content = ref(null)
const fetchContent = async () => {
  try {
    const q = query(collection(firestore, 'contents'), orderBy('datePublish', 'desc'), limit(1))
    const querySnapshot = await getDocs(q)
    querySnapshot.forEach((doc) => {
      content.value = doc.data()
    })
    loading.value = false
  } catch (error) {
    console.error('Error fetching content:', error)
    loading.value = false
  }
}

onMounted(() => {
  fetchContent()
  const controlDocRef = doc(db, 'controls', 'playback');
  onSnapshot(controlDocRef, (doc) => {
    const data = doc.data();
    if (data) {
      console.log(data.command);
      loading.value = true
      fetchContent()
    }
  });
});
</script>
