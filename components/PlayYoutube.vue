<template>
    <div>
      <button v-if="isStop" ref="playButton" @click="playVideo">Play Video</button>
      <div v-else ref="videoContainer" class="hidden">
        Hallo
      </div>
    </div>
  </template>
  
  <script lang="ts" setup>
  import { onMounted, ref } from 'vue';
  
  // Refs
  const videoContainer = ref<HTMLDivElement | null>(null);
  const player = ref<YT.Player | null>(null);
  const isStop = ref(true);
  const props = defineProps({
    videoId: {
    type: Array,
    required: true,
  },
})
  const musics = props.videoId[0]
const extractVideoId = (url: string): string | null => {
  const regex = /youtube\.com\/embed\/([^?&"'>]+)/;
  const match = url.match(regex);
  return match ? match[1] : null;
};


const url = extractVideoId(musics.url);  
// Function to initialize YouTube player
const initializePlayer = () => {
if (videoContainer.value && !player.value) {
    player.value = new YT.Player(videoContainer.value, {
    height: '315',
    width: '560',
    videoId: url, // Ganti dengan ID video YouTube Anda
    playerVars: {
        autoplay: 1,
        loop: 1,
        mute: 1, // Mulai dengan mute
    },
    events: {
        onReady: onPlayerReady,
    },
    });
}
};

// Function to handle player readiness
const onPlayerReady = (event: YT.PlayerEvent) => {
// Optional: lakukan sesuatu saat player siap
};

// Play video and unmute after 3 seconds
const playVideo = () => {
isStop.value = false;

// Load YouTube Iframe API
const tag = document.createElement('script');
tag.src = 'https://www.youtube.com/iframe_api';

tag.onload = () => {
    // Check if API is already loaded
    if (window.YT && window.YT.Player) {
    initializePlayer();
    } else {
    window.onYouTubeIframeAPIReady = initializePlayer;
    }
};

document.head.appendChild(tag);

// Unmute video after 3 seconds
setTimeout(() => {
    if (player.value && player.value.isMuted()) {
    player.value.unMute();
    }
}, 3000);
};
const playButton = ref<HTMLButtonElement | null>(null);  
  // Load the YouTube Iframe API when the component is mounted
onMounted(() => {
     // Trigger the play button click on mount

const tag = document.createElement('script');
tag.src = 'https://www.youtube.com/iframe_api';

tag.onload = () => {
    // Check if API is already loaded
    if (window.YT && window.YT.Player) {
    initializePlayer();
    } else {
    window.onYouTubeIframeAPIReady = initializePlayer;
    }
};

document.head.appendChild(tag);
});
</script>

<style scoped>
/* Tambahkan gaya khusus jika diperlukan */
.hidden{
    display:none;
}
</style>
  