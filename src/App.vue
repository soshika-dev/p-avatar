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
          preload="auto"
        ></video>

        <div v-if="autoplayBlocked" class="overlay-card">
          <div class="overlay-content">
            <p class="overlay-eyebrow">پخش خودکار با صدا فعال نشد</p>
            <h2 class="overlay-title">برای شنیدن صدا، پخش را شروع کنید</h2>
            <p class="overlay-body">
              مرورگر برای حفظ حریم خصوصی، پخش خودکار ویدیو با صدا را مسدود کرده است.
              با زدن دکمه زیر، ویدیو بلافاصله با صدا پخش می‌شود.
            </p>
            <button class="primary-btn" @click="playWithSound">پخش با صدا</button>
          </div>
        </div>

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
const autoplayBlocked = ref(false)
const video = ref<HTMLVideoElement | null>(null)

const playWithSound = async () => {
  if (!video.value) return

  video.value.muted = false
  video.value.volume = 1

  try {
    await video.value.play()
    autoplayBlocked.value = false
  } catch (error) {
    autoplayBlocked.value = true
  }
}

onMounted(() => {
  setTimeout(() => {
    showWarning.value = true
  }, 800)

  const onUserInteract = () => {
    void playWithSound()
  }

  // تلاش اولیه برای autoplay با صدا
  void playWithSound()

  // اگر بلاک شد، با اولین تعامل کاربر پلی و با صدا اجرا می‌شود
  window.addEventListener('pointerdown', onUserInteract, { once: true })
  window.addEventListener('keydown', onUserInteract, { once: true })
})

const restart = () => {
  if (video.value) {
    video.value.currentTime = 0
    void playWithSound()
  }
}
</script>


