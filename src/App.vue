<template>
  <div class="page-root">
    <header class="topbar">
      <div class="brand">
        <div class="brand-mark">AVA</div>
        <div>
          <p class="eyebrow">Virtual Avatar</p>
          <h1 class="brand-title">Meet your on-demand companion</h1>
        </div>
      </div>
      <div class="status-pill">
        <span class="status-dot"></span>
        Ready to talk
      </div>
    </header>

    <main class="grid">
      <section class="intro-card">
        <p class="eyebrow">Live human presence</p>
        <h2 class="hero-title">
          A responsive avatar who greets, listens and guides visitors like a real person.
        </h2>
        <p class="hero-body">
          Launch conversations instantly with a friendly face, ambient motion, and quick replies
          for common questions. Press play to hear the welcome message, then start chatting to see
          how she responds.
        </p>

        <div class="cta-row">
          <button class="primary-btn" @click="restart">
            Start new chat
          </button>
          <button class="ghost-btn" @click="playWithSound">Play greeting</button>
        </div>

        <div class="chips">
          <span class="chip">Persian + English</span>
          <span class="chip">Warm tone</span>
          <span class="chip">Fast follow-ups</span>
          <span class="chip">Always available</span>
        </div>
      </section>

      <section class="video-panel">
        <div class="video-container">
          <div class="video-label">Live avatar feed</div>
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
                مرورگر برای حفظ حریم خصوصی، پخش خودکار ویدیو با صدا را مسدود کرده است. با زدن دکمه
                زیر، ویدیو بلافاصله با صدا پخش می‌شود.
              </p>
              <button class="primary-btn" @click="playWithSound">پخش با صدا</button>
            </div>
          </div>

          <div class="floating-badge">Listening for your question</div>
        </div>

        <div v-if="showWarning" class="warning-card">
          <p class="warning-text">
            اطلاعات ارائه شده صرفا جنبهٔ راهنمایی عمومی دارد و جایگزین مشاوره حقوقی، وکالت یا رای قضایی
            نیست.
          </p>
          <button class="primary-btn" @click="restart">شروع مجدد</button>
        </div>
      </section>
    </main>

    <section class="chat-panel">
      <header class="chat-header">
        <div>
          <p class="eyebrow">Conversation preview</p>
          <h3 class="chat-title">Talk to the avatar as if you are face to face.</h3>
        </div>
        <span class="chat-meta">Private sandbox</span>
      </header>

      <div class="chat-window">
        <div v-for="(message, index) in messages" :key="index" class="chat-row" :class="message.from">
          <div class="avatar" v-if="message.from === 'Ava'">A</div>
          <div class="bubble">
            <p class="bubble-from">{{ message.from === 'Ava' ? 'Avatar' : 'You' }}</p>
            <p class="bubble-text">{{ message.text }}</p>
          </div>
        </div>
      </div>

      <form class="chat-input" @submit.prevent="sendMessage">
        <input
          v-model="userInput"
          type="text"
          name="message"
          placeholder="Ask for directions, pricing, or say hi..."
          autocomplete="off"
        />
        <button class="primary-btn" type="submit" :disabled="isSending || !userInput.trim()">
          {{ isSending ? 'Thinking...' : 'Send' }}
        </button>
      </form>
    </section>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

type Message = {
  from: 'Ava' | 'User'
  text: string
}

const showWarning = ref(false)
const autoplayBlocked = ref(false)
const video = ref<HTMLVideoElement | null>(null)

const defaultMessages: Message[] = [
  { from: 'Ava', text: 'سلام! خوش آمدی. من اینجا هستم تا مثل یک همراه واقعی با تو گفتگو کنم.' },
  { from: 'User', text: 'سلام، می‌تونی به من درباره خدماتت بگی؟' },
  { from: 'Ava', text: 'حتما! من می‌تونم خوشامدگویی کنم، راهنمایی بدهم و سوالات پر تکرار را جواب بدهم.' },
]

const messages = ref<Message[]>([...defaultMessages])
const userInput = ref('')
const isSending = ref(false)

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

const restart = () => {
  messages.value = [...defaultMessages]
  userInput.value = ''

  if (video.value) {
    video.value.currentTime = 0
    void playWithSound()
  }
}

const sendMessage = async () => {
  const text = userInput.value.trim()
  if (!text) return

  messages.value.push({ from: 'User', text })
  userInput.value = ''
  isSending.value = true

  setTimeout(() => {
    messages.value.push({
      from: 'Ava',
      text: 'ممنون که گفتی! همین حالا بررسی می‌کنم و پاسخ می‌دهم. اگر سوال بیشتری داری بهم بگو.',
    })
    isSending.value = false
  }, 700)
}

onMounted(() => {
  setTimeout(() => {
    showWarning.value = true
  }, 800)

  const onUserInteract = () => {
    void playWithSound()
  }

  void playWithSound()

  window.addEventListener('pointerdown', onUserInteract, { once: true })
  window.addEventListener('keydown', onUserInteract, { once: true })
})
</script>
