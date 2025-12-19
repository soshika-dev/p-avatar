<template>
  <div class="page">
    <div class="hero">
      <div class="hero-glow"></div>
      <div class="video-shell">
        <div class="video-header">
          <span class="pill">Avatar is speaking</span>
          <span class="live-dot"></span>
        </div>

        <video
          ref="video"
          class="video"
          src="/sarhang--sarhang.mp4"
          playsinline
          autoplay
          loop
          controlslist="nodownload noremoteplayback noplaybackrate"
        ></video>

        <div v-if="isAutoplayBlocked" class="overlay">
          <div class="overlay-card">
            <p class="overlay-label">پخش خودکار با صدا فعال نشد</p>
            <h2 class="overlay-title">برای شنیدن صدای آواتار، دکمه زیر را بزنید.</h2>
            <button class="primary" @click="playWithSound">پخش با صدا</button>
          </div>
        </div>
      </div>

      <div class="action-row">
        <button class="primary" @click="playWithSound">
          <span>ادامه</span>
          <span class="chevron">⌄</span>
        </button>
        <p class="hint">ویدیو به‌صورت خودکار پخش می‌شود تا آواتار با شما صحبت کند.</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const video = ref<HTMLVideoElement | null>(null)
const isAutoplayBlocked = ref(false)

const startPlayback = async (unmute = false) => {
  if (!video.value) return

  video.value.muted = !unmute
  video.value.volume = unmute ? 1 : 0

  try {
    await video.value.play()
    isAutoplayBlocked.value = false
  } catch (error) {
    if (unmute) {
      await startPlayback(false)
      isAutoplayBlocked.value = true
    }
  }
}

const playWithSound = async () => {
  await startPlayback(true)
}

onMounted(() => {
  void startPlayback(true)
})
</script>
