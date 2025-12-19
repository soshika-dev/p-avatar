<template>
  <div class="page-root">
    <div class="page-overlay">
      <div class="video-container">
        <video
          ref="video"
          class="video-element"
          src="/sarhang--sarhang.mp4"
          controls
          autoplay
          playsinline
        ></video>

        <div v-if="showWarning" class="warning-card">
          <p class="warning-text">
            اطلاعات ارائه شده صرفا جنبهٔ راهنمایی عمومی دارد و جایگزین
            مشاوره حقوقی، وکالت یا رای قضایی نیست.
          </p>
          <button class="primary-btn" @click="restart">
            شروع مجدد
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const showWarning = ref(false)
const video = ref<HTMLVideoElement | null>(null)

onMounted(() => {
  setTimeout(() => {
    showWarning.value = true
  }, 800)

  const tryPlay = () => {
    if (!video.value) return

    video.value.muted = false
    void video.value
      .play()
      .then(() => {
        window.removeEventListener('click', onUserInteract)
        window.removeEventListener('keydown', onUserInteract)
      })
      .catch(() => {
        // browser blocked autoplay with sound
      })
  }

  const onUserInteract = () => {
    tryPlay()
  }

  // تلاش اولیه برای autoplay با صدا
  tryPlay()

  // اگر بلاک شد، با اولین کلیک/کیبورد کاربر پلی و با صدا اجرا می‌شود
  window.addEventListener('click', onUserInteract, { once: true })
  window.addEventListener('keydown', onUserInteract, { once: true })
})

const restart = () => {
  if (video.value) {
    video.value.currentTime = 0
    void video.value.play()
  }
}
</script>


