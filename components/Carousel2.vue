<script setup lang="ts">
const carouselRef = ref()

onMounted(() => {
  setInterval(() => {
    if (!carouselRef.value) return

    if (carouselRef.value.page === carouselRef.value.pages) {
      return carouselRef.value.select(0)
    }

    carouselRef.value.next()
  }, 20000)
})

const props = defineProps({
  images: {
    type: Array,
    required: true,
  },
})

const images = props.images
</script>

<template>
  <UCarousel
    ref="carouselRef"
    v-slot="{ item }"
    :items="images"
    :ui="{ item: 'basis-full' }"
    class="carousel-container"
    indicators
  >
    <img :src="item.url" class="w-full h-full object-contain" draggable="false">
  </UCarousel>
</template>

<style scoped>
.carousel-container {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}
.carousel-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>
