<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// ══════════════════════════════════════════════════════════════════════════
// 1. STATE & AUDIO CONTROLS
// ══════════════════════════════════════════════════════════════════════════
const isDoorOpened = ref(false)
const isDoorAnimating = ref(false)
const isOverlayVisible = ref(true)

const songUrl = '/audio/reel_wedding_song.mp3'
const isPlayingMusic = ref(false)
const audioElement = ref(null)

const toggleMusic = () => {
  if (!audioElement.value) return
  if (isPlayingMusic.value) {
    audioElement.value.pause()
    isPlayingMusic.value = false
  } else {
    audioElement.value.play().then(() => {
      isPlayingMusic.value = true
    }).catch(err => console.warn('Audio play error:', err))
  }
}

const openInvitation = () => {
  if (isDoorAnimating.value || isDoorOpened.value) return
  isDoorAnimating.value = true

  // Start background music automatically on user tap
  if (audioElement.value) {
    audioElement.value.volume = 0.85
    audioElement.value.play().then(() => {
      isPlayingMusic.value = true
    }).catch(err => {
      console.warn('Playback gesture required:', err)
    })
  }

  // Smooth door swing transition
  setTimeout(() => {
    isDoorOpened.value = true
  }, 1100)

  setTimeout(() => {
    isOverlayVisible.value = false
  }, 1800)
}

// ══════════════════════════════════════════════════════════════════════════
// 2. LIVE COUNTDOWN TIMER (TARGET: 25.10.2026 10:30 AM)
// ══════════════════════════════════════════════════════════════════════════
const targetDateStr = '2026-10-25T10:30:00'
const countdown = ref({
  days: '000',
  hours: '00',
  minutes: '00',
  seconds: '00'
})
let countdownTimer = null

const startCountdown = () => {
  const update = () => {
    const target = new Date(targetDateStr).getTime()
    const now = new Date().getTime()
    const diff = target - now

    if (diff <= 0) {
      countdown.value = { days: '000', hours: '00', minutes: '00', seconds: '00' }
      if (countdownTimer) clearInterval(countdownTimer)
      return
    }

    const d = Math.floor(diff / (1000 * 60 * 60 * 24))
    const h = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
    const m = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))
    const s = Math.floor((diff % (1000 * 60)) / 1000)

    countdown.value = {
      days: String(d).padStart(3, '0'),
      hours: String(h).padStart(2, '0'),
      minutes: String(m).padStart(2, '0'),
      seconds: String(s).padStart(2, '0')
    }
  }

  update()
  countdownTimer = setInterval(update, 1000)
}

// ══════════════════════════════════════════════════════════════════════════
// 3. INTERACTIVE "SCRATCH TO REVEAL" CANVAS COMPONENT
// ══════════════════════════════════════════════════════════════════════════
const scratchCanvas = ref(null)
const isScratched = ref(false)
const isScratching = ref(false)
let ctx = null
let canvasWidth = 320
let canvasHeight = 175

const initScratchCard = () => {
  const canvas = scratchCanvas.value
  if (!canvas) return
  ctx = canvas.getContext('2d', { willReadFrequently: true })
  if (!ctx) return

  // Set real pixel dimensions based on display
  const rect = canvas.getBoundingClientRect()
  canvasWidth = rect.width || 320
  canvasHeight = rect.height || 175
  canvas.width = canvasWidth
  canvas.height = canvasHeight

  // 1. Draw luxurious gold metallic gradient
  const grad = ctx.createLinearGradient(0, 0, canvasWidth, canvasHeight)
  grad.addColorStop(0, '#e5b842')
  grad.addColorStop(0.25, '#fff1a8')
  grad.addColorStop(0.5, '#c88c1b')
  grad.addColorStop(0.75, '#fde68a')
  grad.addColorStop(1, '#b47313')

  ctx.fillStyle = grad
  ctx.fillRect(0, 0, canvasWidth, canvasHeight)

  // 2. Add subtle gold shimmer pattern / border
  ctx.strokeStyle = 'rgba(255, 255, 255, 0.4)'
  ctx.lineWidth = 2
  ctx.strokeRect(6, 6, canvasWidth - 12, canvasHeight - 12)

  // 3. Draw "✦ Scratch here ✦" prompt on foil
  ctx.fillStyle = '#6b4305'
  ctx.font = 'bold 16px "Montserrat", sans-serif'
  ctx.textAlign = 'center'
  ctx.textBaseline = 'middle'
  ctx.shadowColor = 'rgba(255, 255, 255, 0.6)'
  ctx.shadowBlur = 4
  ctx.fillText('✦ Scratch here ✦', canvasWidth / 2, canvasHeight / 2)
  ctx.shadowBlur = 0
}

const getPointerPos = (e) => {
  const canvas = scratchCanvas.value
  if (!canvas) return { x: 0, y: 0 }
  const rect = canvas.getBoundingClientRect()
  const clientX = e.touches ? e.touches[0].clientX : e.clientX
  const clientY = e.touches ? e.touches[0].clientY : e.clientY
  return {
    x: clientX - rect.left,
    y: clientY - rect.top
  }
}

const scratchAt = (x, y) => {
  if (!ctx || isScratched.value) return
  ctx.globalCompositeOperation = 'destination-out'
  ctx.beginPath()
  ctx.arc(x, y, 24, 0, Math.PI * 2)
  ctx.fill()
}

const checkScratchPercentage = () => {
  if (!ctx || isScratched.value) return
  try {
    const imgData = ctx.getImageData(0, 0, canvasWidth, canvasHeight)
    const pixels = imgData.data
    let transparentCount = 0
    const totalPixels = pixels.length / 4

    // Sample every 8th pixel for smooth performance
    for (let i = 3; i < pixels.length; i += 32) {
      if (pixels[i] === 0) {
        transparentCount++
      }
    }

    const ratio = transparentCount / (totalPixels / 8)
    if (ratio > 0.38) {
      isScratched.value = true
    }
  } catch (err) {
    console.warn('Canvas sampling notice:', err)
  }
}

const onPointerDown = (e) => {
  if (isScratched.value) return
  isScratching.value = true
  const pos = getPointerPos(e)
  scratchAt(pos.x, pos.y)
}

const onPointerMove = (e) => {
  if (!isScratching.value || isScratched.value) return
  if (e.touches) e.preventDefault()
  const pos = getPointerPos(e)
  scratchAt(pos.x, pos.y)
}

const onPointerUp = () => {
  if (isScratching.value) {
    isScratching.value = false
    checkScratchPercentage()
  }
}

const revealCardInstantly = () => {
  isScratched.value = true
}

// ══════════════════════════════════════════════════════════════════════════
// 4. ACTION HANDLERS (LOCATION & CALENDAR)
// ══════════════════════════════════════════════════════════════════════════
const mapsUrl = 'https://maps.app.goo.gl/8r8R7PVDQ1oatWbVA'

const openMaps = () => {
  window.open(mapsUrl, '_blank', 'noopener,noreferrer')
}

const addToCalendar = () => {
  // Generate downloadable ICS file for iOS / Android / Outlook
  const icsData = [
    'BEGIN:VCALENDAR',
    'VERSION:2.0',
    'PRODID:-//InviteSend//Engagement Ceremony//EN',
    'CALSCALE:GREGORIAN',
    'METHOD:PUBLISH',
    'BEGIN:VEVENT',
    'SUMMARY:M. Kirubakaran & N. Bhavya - Engagement Ceremony',
    'DESCRIPTION:With hearts full of happiness\\, you are cordially invited to celebrate the auspicious Engagement Ceremony of M. Kirubakaran and N. Bhavya.',
    'LOCATION:Sornam Arumugam Marriage Hall\\, Tittagudi\\, Cuddalore District\\, Tamil Nadu',
    'DTSTART:20261025T050000Z',
    'DTEND:20261025T063000Z',
    'STATUS:CONFIRMED',
    'SEQUENCE:0',
    'END:VEVENT',
    'END:VCALENDAR'
  ].join('\r\n')

  const blob = new Blob([icsData], { type: 'text/calendar;charset=utf-8' })
  const url = window.URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.setAttribute('download', 'Kirubakaran-Bhavya-Engagement.ics')
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
}

onMounted(() => {
  startCountdown()
  // Allow DOM to render before binding canvas
  setTimeout(() => {
    initScratchCard()
  }, 400)
  window.addEventListener('resize', initScratchCard)
})

onUnmounted(() => {
  if (countdownTimer) clearInterval(countdownTimer)
  window.removeEventListener('resize', initScratchCard)
  if (audioElement.value) audioElement.value.pause()
})
</script>

<template>
  <div class="video-theme-wrapper">

    <!-- ══════════════════════════════════════════════════════════════════════
         AUDIO ELEMENT & FLOATING MUSIC TOGGLE BUTTON
         ══════════════════════════════════════════════════════════════════════ -->
    <audio 
      ref="audioElement" 
      :src="songUrl" 
      loop 
      preload="auto" 
      playsinline 
      webkit-playsinline
      style="display: none;"
    ></audio>

    <div v-if="!isOverlayVisible" class="floating-music-btn-wrap">
      <button 
        @click="toggleMusic" 
        class="floating-music-btn" 
        :class="{ 'is-spinning': isPlayingMusic }"
        :title="isPlayingMusic ? 'Mute Music' : 'Play Music'"
        aria-label="Toggle Music"
      >
        <span class="music-note-icon">{{ isPlayingMusic ? '🎵' : '🔇' }}</span>
      </button>
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         1. TEMPLE DOOR ENTRANCE LANDING OVERLAY (EXACTLY AS IN VIDEO)
         ══════════════════════════════════════════════════════════════════════ -->
    <div 
      v-if="isOverlayVisible" 
      class="door-landing-overlay"
      :class="{ 'doors-swinging': isDoorAnimating, 'doors-finished': isDoorOpened }"
    >
      <!-- Background Temple Architecture -->
      <div class="door-bg-artwork">
        <img src="/images/reel-theme/door_bg.jpg" alt="Temple Door Architecture" class="door-bg-img" />
      </div>

      <!-- 3D Perspective Stage Doors -->
      <div class="doors-perspective-stage">
        <!-- Left Door Leaf -->
        <div class="door-leaf-panel leaf-left">
          <div class="door-surface-texture">
            <div class="door-brass-ring ring-left"></div>
          </div>
        </div>

        <!-- Right Door Leaf -->
        <div class="door-leaf-panel leaf-right">
          <div class="door-surface-texture">
            <div class="door-brass-ring ring-right"></div>
          </div>
        </div>
      </div>

      <!-- Center Ornate Invitation Card & Open Button -->
      <div class="door-center-card">
        <div class="door-card-ornament">❦</div>
        <p class="door-card-sub">With all our hearts</p>
        <h1 class="door-card-title">You are invited</h1>
        <p class="door-card-sub-bottom">to celebrate with us</p>

        <!-- Tap to Open Button -->
        <div class="door-action-wrapper">
          <button 
            type="button" 
            @click="openInvitation" 
            class="btn-open-invitation"
            aria-label="Open Invitation"
          >
            <span>OPEN INVITATION</span>
          </button>
          <p class="tap-hint-text">Tap to open the doors</p>
        </div>
      </div>

      <!-- Bottom Floating Urli Diyas & Lotus Petals -->
      <div class="door-bottom-rangoli">
        <div class="urli-diya-glow">🪔</div>
      </div>
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         MAIN INVITATION BODY (VISIBLE AFTER DOOR OPENS)
         ══════════════════════════════════════════════════════════════════════ -->
    <div class="main-invitation-container" :class="{ 'invitation-revealed': isDoorOpened }">

      <!-- ══════════════════════════════════════════════════════════════════
           2. HERO / COUPLE DETAILS SECTION
           ══════════════════════════════════════════════════════════════════ -->
      <section class="section-hero-card">
        <div class="hero-card-inner">

          <!-- Top Garland Floral Toran Accent -->
          <div class="hero-toran-crest">
            <span class="crest-leaf">🌿</span>
            <span class="crest-flower">🌼</span>
            <span class="crest-om">🕉️</span>
            <span class="crest-flower">🌼</span>
            <span class="crest-leaf">🌿</span>
          </div>

          <h2 class="hero-top-tag">TOGETHER WITH OUR FAMILIES</h2>
          <p class="hero-sub-invite">
            We joyfully invite you to celebrate the engagement and wedding of
          </p>

          <!-- Groom Details -->
          <div class="couple-name-wrap groom-wrap">
            <h1 class="calligraphy-name">M. Kirubakaran</h1>
            <p class="parents-line">S/o Mr. R. Nakkeeran & Mrs. N. Selvarani</p>
          </div>

          <!-- WEDS Connector -->
          <div class="weds-connector-wrap">
            <span class="weds-line"></span>
            <span class="weds-badge">WEDS</span>
            <span class="weds-line"></span>
          </div>

          <!-- Bride Details -->
          <div class="couple-name-wrap bride-wrap">
            <h1 class="calligraphy-name">N. Bhavya</h1>
            <p class="parents-line">D/o Mr. K.M. Mohan & Mrs. M. Akila Priya</p>
          </div>

          <!-- Auspicious Mandapam & Mangala Vadhyam Artwork Backdrop -->
          <div class="hero-traditional-illustration">
            <img src="/images/reel-theme/hero_bg.jpg" alt="South Indian Traditional Wedding Artwork" class="traditional-scene-img" />
          </div>

          <!-- Bouncing Scroll Down Prompt -->
          <div class="scroll-story-indicator">
            <span class="story-pill">
              ↓ SCROLL TO DISCOVER OUR STORY ↓
            </span>
          </div>

        </div>
      </section>

      <!-- ══════════════════════════════════════════════════════════════════
           3. INTERACTIVE "SCRATCH TO REVEAL" DATE CARD
           ══════════════════════════════════════════════════════════════════ -->
      <section class="section-scratch-card">
        <div class="scratch-card-container">

          <!-- Gold Ganesha Idol Crest -->
          <div class="ganesha-arch-wrap">
            <div class="ganesha-symbol">
              <svg viewBox="0 0 100 100" class="ganesha-svg" fill="none" stroke="currentColor">
                <!-- Ornate Ganesha Silhouette -->
                <path d="M50 15 C45 15 42 20 42 25 C42 32 46 38 48 45 C49 50 49 55 45 60 C40 66 32 70 35 78 C37 83 44 85 50 85 C56 85 63 83 65 78 C68 70 60 66 55 60 C51 55 51 50 52 45 C54 38 58 32 58 25 C58 20 55 15 50 15 Z" fill="#d97706" opacity="0.9" />
                <circle cx="50" cy="22" r="3" fill="#fbbf24" />
                <path d="M38 32 C30 35 25 42 25 50 C25 58 32 65 40 68" stroke="#d97706" stroke-width="2.5" stroke-linecap="round" />
                <path d="M62 32 C70 35 75 42 75 50 C75 58 68 65 60 68" stroke="#d97706" stroke-width="2.5" stroke-linecap="round" />
                <circle cx="45" cy="30" r="1.5" fill="#fff" />
                <circle cx="55" cy="30" r="1.5" fill="#fff" />
              </svg>
            </div>
            <div class="hanging-brass-deepam deepam-left">🪔</div>
            <div class="hanging-brass-deepam deepam-right">🪔</div>
          </div>

          <h3 class="scratch-section-title">Our Special Date</h3>
          <p class="scratch-section-sub">Scratch to reveal the date</p>

          <!-- Interactive Scratch Card Box -->
          <div class="scratch-canvas-box" :class="{ 'is-revealed': isScratched }">

            <!-- Revealed Content Layer Underneath -->
            <div class="scratch-revealed-layer">
              <div class="revealed-day">SUNDAY</div>
              <div class="revealed-date">25 - 10 - 2026</div>
              <div class="revealed-muhurtham">✦ Auspicious Muhurtham: 10:30 AM - 12:00 PM ✦</div>
            </div>

            <!-- Golden Scratch Foil Canvas Layer On Top -->
            <canvas 
              ref="scratchCanvas" 
              class="scratch-foil-canvas"
              @mousedown="onPointerDown"
              @mousemove="onPointerMove"
              @mouseup="onPointerUp"
              @mouseleave="onPointerUp"
              @touchstart.passive="onPointerDown"
              @touchmove="onPointerMove"
              @touchend="onPointerUp"
            ></canvas>

          </div>

          <p class="scratch-bottom-hint" @click="revealCardInstantly">
            ✦ Gently reveal our special date ✦
          </p>

          <!-- Lotus Corner Florals -->
          <div class="lotus-corner-wrap">
            <span class="lotus-flower">🪷</span>
            <span class="lotus-flower">🪷</span>
          </div>

        </div>
      </section>

      <!-- ══════════════════════════════════════════════════════════════════
           4. WEDDING TIMELINE (TWO MOMENTS • ONE FOREVER)
           ══════════════════════════════════════════════════════════════════ -->
      <section class="section-timeline">
        <div class="timeline-header-wrap">
          <h2 class="timeline-title">WEDDING TIMELINE</h2>
          <p class="timeline-subtitle">Two beautiful moments • One forever</p>
        </div>

        <div class="timeline-cards-stack">

          <!-- Event 1: Engagement Card -->
          <div class="timeline-event-card">
            <div class="event-arch-cutout">
              <img src="/images/reel-theme/stage_mandapam.jpg" alt="Engagement Stage Decor" class="event-arch-img" />
              <div class="arch-heart-badge">♥</div>
            </div>

            <div class="event-card-body">
              <h3 class="event-type-title">Engagement</h3>
              <div class="event-date-row">Sunday • 25th October 2026</div>
              <div class="event-time-row">10:30 AM – 12:00 PM</div>
              <div class="event-venue-name">Sornam Arumugam Marriage Hall</div>
              <div class="event-venue-address">Tittagudi, Cuddalore District, Tamil Nadu - 606106</div>

              <div class="event-actions-grid">
                <button type="button" @click="openMaps" class="btn-event-action btn-location">
                  <span class="action-icon">📍</span>
                  <span>View Location</span>
                </button>
                <button type="button" @click="addToCalendar" class="btn-event-action btn-calendar">
                  <span class="action-icon">📅</span>
                  <span>Add to Calendar</span>
                </button>
              </div>
            </div>
          </div>

          <!-- Event 2: Wedding / Muhurtham Card -->
          <div class="timeline-event-card">
            <div class="event-arch-cutout">
              <img src="/images/reel-theme/stage_gopuram.jpg" alt="Wedding Mandapam Architecture" class="event-arch-img" />
              <div class="arch-heart-badge">♥</div>
            </div>

            <div class="event-card-body">
              <h3 class="event-type-title">Wedding</h3>
              <div class="event-date-row">Sunday • 25th October 2026</div>
              <div class="event-time-row">Auspicious Muhurtham</div>
              <div class="event-venue-name">Sornam Arumugam Marriage Hall</div>
              <div class="event-venue-address">Tittagudi, Cuddalore District, Tamil Nadu</div>

              <div class="event-actions-grid">
                <button type="button" @click="openMaps" class="btn-event-action btn-location">
                  <span class="action-icon">📍</span>
                  <span>View Location</span>
                </button>
                <button type="button" @click="addToCalendar" class="btn-event-action btn-calendar">
                  <span class="action-icon">📅</span>
                  <span>Add to Calendar</span>
                </button>
              </div>
            </div>
          </div>

        </div>
      </section>

      <!-- ══════════════════════════════════════════════════════════════════
           5. COUNTING DOWN TO FOREVER
           ══════════════════════════════════════════════════════════════════ -->
      <section class="section-countdown-banner">
        <div class="countdown-bg-wrapper">
          <img src="/images/reel-theme/countdown_bg.jpg" alt="Couple Hands Rings Forever" class="countdown-bg-photo" />
          <div class="countdown-crimson-overlay"></div>
        </div>

        <div class="countdown-content-inner">
          <span class="countdown-badge-tag">BIG DAY IS GETTING CLOSER</span>
          <h2 class="countdown-heading">Counting Down to Forever</h2>
          <p class="countdown-lead">Every second brings us closer to celebrating with you.</p>

          <!-- 4 Crimson Glassmorphism Countdown Boxes -->
          <div class="countdown-blocks-grid">
            <div class="countdown-digit-box">
              <span class="digit-number">{{ countdown.days }}</span>
              <span class="digit-label">DAYS</span>
            </div>
            <div class="countdown-digit-box">
              <span class="digit-number">{{ countdown.hours }}</span>
              <span class="digit-label">HOURS</span>
            </div>
            <div class="countdown-digit-box">
              <span class="digit-number">{{ countdown.minutes }}</span>
              <span class="digit-label">MINUTES</span>
            </div>
            <div class="countdown-digit-box">
              <span class="digit-number">{{ countdown.seconds }}</span>
              <span class="digit-label">SECONDS</span>
            </div>
          </div>

          <div class="countdown-date-stamp">
            25 OCTOBER 2026 • 10:30 AM
          </div>
        </div>
      </section>

      <!-- ══════════════════════════════════════════════════════════════════
           6. PERSONAL NOTE TO GUESTS ("DEAR GUEST")
           ══════════════════════════════════════════════════════════════════ -->
      <section class="section-guest-note">
        <div class="guest-note-bg-wrap">
          <img src="/images/reel-theme/note_bg.jpg" alt="Couple Sunset Love" class="guest-note-bg-img" />
          <div class="guest-note-soft-overlay"></div>
        </div>

        <div class="guest-note-parchment-card">
          <span class="note-top-tag">A LITTLE NOTE FOR YOU</span>
          <h3 class="note-headline">Dear Guest</h3>

          <p class="note-letter-body">
            We found our moment, and now we're making it a lifetime. Come share the laughter, the love, and the beginning of our forever. As we step into this beautiful new chapter together, your presence, love, and blessings would make our special day even more meaningful and fill our hearts with joy.
          </p>

          <div class="note-signature-wrap">
            <p class="signature-script">With love,</p>
            <p class="signature-couple">Kirubakaran & Bhavya</p>
          </div>
        </div>
      </section>

      <!-- ══════════════════════════════════════════════════════════════════
           7. FOOTER: MADE WITH LOVE BY INVITESEND.COM
           ══════════════════════════════════════════════════════════════════ -->
      <footer class="video-theme-footer">
        <div class="footer-inner">
          <a 
            href="https://invitesend.com" 
            target="_blank" 
            rel="noopener noreferrer" 
            class="footer-brand-pill"
          >
            <span>Made with love by</span>
            <strong>InviteSend.com</strong>
          </a>
        </div>
      </footer>

    </div>

  </div>
</template>

<style scoped>
/* ══════════════════════════════════════════════════════════════════════════
   GLOBAL THEME WRAPPER & CONTAINER
   ══════════════════════════════════════════════════════════════════════════ */
.video-theme-wrapper {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background-color: #0b0202;
  background-image: radial-gradient(circle at 50% 0%, #2b0b0b 0%, #0d0303 60%, #050101 100%);
  color: #ffffff;
  font-family: 'Montserrat', sans-serif;
  overflow-x: hidden;
  display: flex;
  justify-content: center;
}

/* Center stage locked to authentic mobile dimensions with responsive fallback */
.main-invitation-container {
  width: 100%;
  max-width: 480px;
  min-height: 100vh;
  background: #fffdfa;
  box-shadow: 0 0 50px rgba(0, 0, 0, 0.8), 0 0 25px rgba(217, 119, 6, 0.2);
  display: flex;
  flex-direction: column;
  position: relative;
  overflow-x: hidden;
  opacity: 0;
  transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}

.main-invitation-container.invitation-revealed {
  opacity: 1;
}

/* ══════════════════════════════════════════════════════════════════════════
   FLOATING AUDIO BUTTON (TOP-RIGHT)
   ══════════════════════════════════════════════════════════════════════ */
.floating-music-btn-wrap {
  position: fixed;
  top: 1.25rem;
  right: calc(50% - 225px);
  z-index: 999;
}

@media (max-width: 480px) {
  .floating-music-btn-wrap {
    right: 1.25rem;
  }
}

.floating-music-btn {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  background: rgba(30, 8, 8, 0.88);
  border: 1.5px solid #fbbf24;
  color: #fbbf24;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 4px 18px rgba(0, 0, 0, 0.6), 0 0 12px rgba(251, 191, 36, 0.35);
  transition: transform 0.25s ease, background 0.25s ease;
}

.floating-music-btn:hover {
  transform: scale(1.08);
  background: rgba(50, 10, 10, 0.95);
}

.floating-music-btn.is-spinning .music-note-icon {
  animation: pulseMusic 2s ease-in-out infinite;
}

@keyframes pulseMusic {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.2); }
}

/* ══════════════════════════════════════════════════════════════════════════
   1. DOOR OPENING LANDING OVERLAY
   ══════════════════════════════════════════════════════════════════════ */
.door-landing-overlay {
  position: fixed;
  inset: 0;
  z-index: 10000;
  background: #120303;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  transition: opacity 0.8s ease;
}

.door-landing-overlay.doors-finished {
  opacity: 0;
  pointer-events: none;
}

.door-bg-artwork {
  position: absolute;
  inset: 0;
  z-index: 1;
}

.door-bg-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
  filter: brightness(0.92) contrast(1.05);
}

/* 3D Doors Stage */
.doors-perspective-stage {
  position: absolute;
  inset: 0;
  z-index: 2;
  display: flex;
  perspective: 1200px;
  perspective-origin: center center;
  pointer-events: none;
}

.door-leaf-panel {
  flex: 1;
  height: 100%;
  background: linear-gradient(90deg, rgba(30, 8, 8, 0.3), rgba(60, 15, 15, 0.1));
  box-shadow: inset 0 0 40px rgba(0, 0, 0, 0.7);
  transition: transform 1.5s cubic-bezier(0.25, 1, 0.5, 1);
  will-change: transform;
}

.door-leaf-panel.leaf-left {
  transform-origin: left center;
}

.door-leaf-panel.leaf-right {
  transform-origin: right center;
}

.doors-swinging .door-leaf-panel.leaf-left {
  transform: rotateY(-110deg);
}

.doors-swinging .door-leaf-panel.leaf-right {
  transform: rotateY(110deg);
}

/* Center Invitation Crest */
.door-center-card {
  position: relative;
  z-index: 10;
  width: 90%;
  max-width: 360px;
  background: rgba(28, 6, 6, 0.82);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border: 1.5px solid rgba(251, 191, 36, 0.55);
  border-radius: 1.5rem;
  padding: 2.25rem 1.5rem;
  text-align: center;
  box-shadow: 0 15px 45px rgba(0, 0, 0, 0.85), 0 0 25px rgba(251, 191, 36, 0.25);
  transition: opacity 0.6s ease, transform 0.6s ease;
}

.doors-swinging .door-center-card {
  opacity: 0;
  transform: scale(0.92);
}

.door-card-ornament {
  color: #fbbf24;
  font-size: 1.4rem;
  margin-bottom: 0.25rem;
}

.door-card-sub {
  font-family: 'Cinzel', serif;
  font-size: 0.95rem;
  color: #fef08a;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  margin-bottom: 0.5rem;
}

.door-card-title {
  font-family: 'Playfair Display', Georgia, serif;
  font-size: clamp(2.3rem, 7vw, 3rem);
  font-weight: 700;
  color: #ffffff;
  line-height: 1.15;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.9), 0 0 20px rgba(251, 191, 36, 0.4);
  margin: 0.35rem 0;
}

.door-card-sub-bottom {
  font-family: 'Playfair Display', serif;
  font-style: italic;
  font-size: 1.1rem;
  color: #fed7aa;
  margin-bottom: 1.75rem;
}

/* Open Button */
.btn-open-invitation {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.9rem 2.25rem;
  background: linear-gradient(135deg, #fef08a 0%, #fbbf24 50%, #d97706 100%);
  color: #2b0b0b;
  font-family: 'Cinzel', sans-serif;
  font-size: 1.05rem;
  font-weight: 800;
  letter-spacing: 0.12em;
  border: none;
  border-radius: 999px;
  cursor: pointer;
  box-shadow: 0 6px 25px rgba(217, 119, 6, 0.6), 0 0 15px rgba(254, 240, 138, 0.4);
  transition: transform 0.25s ease, box-shadow 0.25s ease;
  animation: pulseBtn 2.2s infinite;
}

@keyframes pulseBtn {
  0%, 100% { transform: scale(1); box-shadow: 0 6px 25px rgba(217, 119, 6, 0.6); }
  50% { transform: scale(1.04); box-shadow: 0 8px 32px rgba(251, 191, 36, 0.85); }
}

.tap-hint-text {
  font-size: 0.85rem;
  color: #fef08a;
  opacity: 0.9;
  margin-top: 0.75rem;
  letter-spacing: 0.05em;
}

.door-bottom-rangoli {
  position: absolute;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
}

.urli-diya-glow {
  font-size: 1.8rem;
  filter: drop-shadow(0 0 10px rgba(251, 191, 36, 0.8));
}

/* ══════════════════════════════════════════════════════════════════════════
   2. HERO / COUPLE DETAILS SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.section-hero-card {
  width: 100%;
  background: #fbf7f0;
  padding: 2.5rem 1.25rem 2rem;
  position: relative;
  text-align: center;
  color: #2e0808;
}

.hero-card-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}

.hero-toran-crest {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 1.25rem;
  font-size: 1.15rem;
}

.hero-top-tag {
  font-family: 'Cinzel', Georgia, serif;
  font-size: 0.95rem;
  font-weight: 700;
  letter-spacing: 0.16em;
  color: #78350f;
  margin-bottom: 0.45rem;
}

.hero-sub-invite {
  font-family: 'Playfair Display', Georgia, serif;
  font-size: 1.05rem;
  line-height: 1.45;
  color: #551c1c;
  max-width: 380px;
  margin-bottom: 1.75rem;
}

.couple-name-wrap {
  margin: 0.25rem 0;
}

.calligraphy-name {
  font-family: 'Alex Brush', 'Great Vibes', cursive;
  font-size: clamp(3rem, 10vw, 4rem);
  font-weight: 400;
  color: #4a0e0e;
  line-height: 1.2;
  margin: 0;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.15);
}

.parents-line {
  font-family: 'Playfair Display', serif;
  font-size: 0.88rem;
  color: #6b2626;
  margin-top: 0.2rem;
  letter-spacing: 0.02em;
}

.weds-connector-wrap {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  width: 70%;
  margin: 1rem 0;
}

.weds-line {
  flex: 1;
  height: 1px;
  background: linear-gradient(90deg, transparent, #d97706, transparent);
}

.weds-badge {
  font-family: 'Cinzel', serif;
  font-size: 0.92rem;
  font-weight: 700;
  letter-spacing: 0.2em;
  color: #b45309;
}

.hero-traditional-illustration {
  width: 100%;
  margin-top: 1.5rem;
  border-radius: 1rem;
  overflow: hidden;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.12);
}

.traditional-scene-img {
  width: 100%;
  height: auto;
  display: block;
  object-fit: cover;
}

.scroll-story-indicator {
  margin-top: 1.5rem;
}

.story-pill {
  display: inline-block;
  padding: 0.45rem 1.1rem;
  background: rgba(254, 243, 199, 0.85);
  border: 1px solid #d97706;
  border-radius: 999px;
  font-family: 'Cinzel', sans-serif;
  font-size: 0.76rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  color: #92400e;
  animation: bouncePill 2s infinite ease-in-out;
}

@keyframes bouncePill {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

/* ══════════════════════════════════════════════════════════════════════════
   3. INTERACTIVE SCRATCH TO REVEAL DATE CARD
   ══════════════════════════════════════════════════════════════════════════ */
.section-scratch-card {
  width: 100%;
  background: #fdfaf5;
  padding: 2.5rem 1.25rem;
  text-align: center;
  color: #2b0b0b;
  position: relative;
  border-top: 1px dashed rgba(217, 119, 6, 0.35);
}

.scratch-card-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.ganesha-arch-wrap {
  position: relative;
  width: 100%;
  max-width: 260px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 0.75rem;
}

.ganesha-symbol {
  width: 64px;
  height: 64px;
}

.ganesha-svg {
  width: 100%;
  height: 100%;
}

.hanging-brass-deepam {
  position: absolute;
  top: 0;
  font-size: 1.4rem;
  filter: drop-shadow(0 0 6px rgba(251, 191, 36, 0.7));
}

.hanging-brass-deepam.deepam-left { left: 0.5rem; }
.hanging-brass-deepam.deepam-right { right: 0.5rem; }

.scratch-section-title {
  font-family: 'Playfair Display', Georgia, serif;
  font-size: 1.85rem;
  font-weight: 700;
  color: #4a0e0e;
  margin: 0;
}

.scratch-section-sub {
  font-family: 'Playfair Display', serif;
  font-style: italic;
  font-size: 1rem;
  color: #78350f;
  margin-top: 0.25rem;
  margin-bottom: 1.25rem;
}

/* Canvas Scratch Box */
.scratch-canvas-box {
  position: relative;
  width: 100%;
  max-width: 320px;
  height: 175px;
  border-radius: 1rem;
  overflow: hidden;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.18), 0 0 15px rgba(217, 119, 6, 0.25);
  border: 2px solid #d97706;
  background: #fffefb;
  user-select: none;
  -webkit-user-select: none;
  touch-action: none;
}

/* Revealed Content Layer */
.scratch-revealed-layer {
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, #fffdf7 0%, #fef3c7 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 1rem;
  text-align: center;
  z-index: 1;
}

.revealed-day {
  font-family: 'Cinzel', serif;
  font-size: 1.2rem;
  font-weight: 800;
  letter-spacing: 0.16em;
  color: #92400e;
}

.revealed-date {
  font-family: 'Playfair Display', serif;
  font-size: 2.15rem;
  font-weight: 800;
  letter-spacing: 0.08em;
  color: #4a0e0e;
  margin: 0.2rem 0;
}

.revealed-muhurtham {
  font-size: 0.8rem;
  font-weight: 600;
  color: #b45309;
}

/* Foil Canvas On Top */
.scratch-foil-canvas {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  z-index: 2;
  cursor: crosshair;
  transition: opacity 0.5s ease;
}

.scratch-canvas-box.is-revealed .scratch-foil-canvas {
  opacity: 0;
  pointer-events: none;
}

.scratch-bottom-hint {
  font-size: 0.85rem;
  color: #92400e;
  margin-top: 1rem;
  cursor: pointer;
  letter-spacing: 0.04em;
}

.lotus-corner-wrap {
  display: flex;
  justify-content: space-between;
  width: 100%;
  max-width: 320px;
  margin-top: 0.5rem;
  font-size: 1.6rem;
}

/* ══════════════════════════════════════════════════════════════════════════
   4. WEDDING TIMELINE SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.section-timeline {
  width: 100%;
  background: linear-gradient(180deg, #091224 0%, #102142 50%, #0c1833 100%);
  padding: 3rem 1.25rem;
  color: #ffffff;
  text-align: center;
}

.timeline-title {
  font-family: 'Cinzel', Georgia, serif;
  font-size: 1.55rem;
  font-weight: 700;
  letter-spacing: 0.14em;
  color: #fbbf24;
  margin: 0;
}

.timeline-subtitle {
  font-family: 'Playfair Display', serif;
  font-style: italic;
  font-size: 0.95rem;
  color: #fed7aa;
  margin-top: 0.35rem;
  margin-bottom: 2rem;
}

.timeline-cards-stack {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  width: 100%;
}

.timeline-event-card {
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(251, 191, 36, 0.35);
  border-radius: 1.25rem;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
}

.event-arch-cutout {
  position: relative;
  width: 100%;
  height: 140px;
  overflow: hidden;
  background: #000;
}

.event-arch-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
  filter: brightness(0.95);
}

.arch-heart-badge {
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #fbbf24;
  color: #5b1313;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
}

.event-card-body {
  padding: 1.5rem 1.25rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.event-type-title {
  font-family: 'Alex Brush', 'Great Vibes', cursive;
  font-size: 2.8rem;
  font-weight: 400;
  color: #ffffff;
  line-height: 1.1;
  margin: 0;
  text-shadow: 0 0 15px rgba(251, 191, 36, 0.4);
}

.event-date-row {
  font-family: 'Playfair Display', serif;
  font-size: 1.05rem;
  font-weight: 700;
  color: #fef08a;
  margin-top: 0.5rem;
}

.event-time-row {
  font-size: 0.95rem;
  font-weight: 700;
  color: #fed7aa;
  margin-top: 0.2rem;
  letter-spacing: 0.05em;
}

.event-venue-name {
  font-family: 'Playfair Display', serif;
  font-size: 1.1rem;
  font-weight: 700;
  color: #ffffff;
  margin-top: 0.85rem;
}

.event-venue-address {
  font-size: 0.85rem;
  color: #e2e8f0;
  opacity: 0.9;
  max-width: 320px;
  line-height: 1.4;
  margin-top: 0.25rem;
}

.event-actions-grid {
  display: flex;
  gap: 0.75rem;
  width: 100%;
  max-width: 340px;
  margin-top: 1.25rem;
}

.btn-event-action {
  flex: 1;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.4rem;
  padding: 0.65rem 0.5rem;
  background: rgba(255, 255, 255, 0.12);
  border: 1px solid rgba(251, 191, 36, 0.4);
  border-radius: 999px;
  color: #ffffff;
  font-size: 0.82rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  backdrop-filter: blur(6px);
}

.btn-event-action:hover {
  background: rgba(251, 191, 36, 0.25);
  border-color: #fbbf24;
  transform: translateY(-2px);
}

/* ══════════════════════════════════════════════════════════════════════════
   5. COUNTDOWN BANNER SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.section-countdown-banner {
  position: relative;
  width: 100%;
  min-height: 380px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  text-align: center;
  padding: 3rem 1.25rem;
}

.countdown-bg-wrapper {
  position: absolute;
  inset: 0;
  z-index: 1;
}

.countdown-bg-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}

.countdown-crimson-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(65, 12, 18, 0.88) 0%, rgba(35, 6, 10, 0.94) 100%);
}

.countdown-content-inner {
  position: relative;
  z-index: 2;
  width: 100%;
  max-width: 380px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.countdown-badge-tag {
  font-family: 'Cinzel', serif;
  font-size: 0.82rem;
  letter-spacing: 0.16em;
  color: #fef08a;
  margin-bottom: 0.45rem;
}

.countdown-heading {
  font-family: 'Playfair Display', Georgia, serif;
  font-size: clamp(1.8rem, 5.5vw, 2.3rem);
  font-weight: 700;
  color: #ffffff;
  margin: 0;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.8);
}

.countdown-lead {
  font-size: 0.88rem;
  color: #fed7aa;
  margin-top: 0.5rem;
  margin-bottom: 1.5rem;
  max-width: 300px;
  line-height: 1.4;
}

.countdown-blocks-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.5rem;
  width: 100%;
}

.countdown-digit-box {
  background: rgba(45, 10, 15, 0.65);
  border: 1px solid rgba(251, 191, 36, 0.45);
  border-radius: 0.75rem;
  padding: 0.75rem 0.25rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  backdrop-filter: blur(6px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4);
}

.digit-number {
  font-family: 'Playfair Display', serif;
  font-size: 1.6rem;
  font-weight: 800;
  color: #ffffff;
  line-height: 1.1;
}

.digit-label {
  font-family: 'Cinzel', sans-serif;
  font-size: 0.65rem;
  font-weight: 700;
  color: #fbbf24;
  letter-spacing: 0.08em;
  margin-top: 0.25rem;
}

.countdown-date-stamp {
  font-family: 'Cinzel', serif;
  font-size: 0.88rem;
  font-weight: 700;
  letter-spacing: 0.14em;
  color: #fef08a;
  margin-top: 1.5rem;
  border-top: 1px solid rgba(251, 191, 36, 0.35);
  padding-top: 0.75rem;
  width: 80%;
}

/* ══════════════════════════════════════════════════════════════════════════
   6. GUEST NOTE SECTION ("DEAR GUEST")
   ══════════════════════════════════════════════════════════════════════════ */
.section-guest-note {
  position: relative;
  width: 100%;
  padding: 3rem 1.25rem;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.guest-note-bg-wrap {
  position: absolute;
  inset: 0;
  z-index: 1;
}

.guest-note-bg-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}

.guest-note-soft-overlay {
  position: absolute;
  inset: 0;
  background: rgba(15, 23, 42, 0.55);
}

.guest-note-parchment-card {
  position: relative;
  z-index: 2;
  width: 100%;
  max-width: 380px;
  background: rgba(255, 255, 255, 0.88);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(251, 191, 36, 0.6);
  border-radius: 1.25rem;
  padding: 2.25rem 1.5rem;
  text-align: center;
  color: #2b0b0b;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.35);
}

.note-top-tag {
  font-family: 'Cinzel', serif;
  font-size: 0.82rem;
  font-weight: 700;
  letter-spacing: 0.16em;
  color: #92400e;
  margin-bottom: 0.25rem;
}

.note-headline {
  font-family: 'Playfair Display', Georgia, serif;
  font-size: 2rem;
  font-weight: 700;
  color: #4a0e0e;
  margin: 0.25rem 0 1rem;
}

.note-letter-body {
  font-family: 'Playfair Display', Georgia, serif;
  font-style: italic;
  font-size: 0.95rem;
  line-height: 1.65;
  color: #451a1a;
  margin-bottom: 1.5rem;
}

.signature-script {
  font-family: 'Alex Brush', 'Great Vibes', cursive;
  font-size: 2.2rem;
  color: #b45309;
  line-height: 1;
  margin: 0;
}

.signature-couple {
  font-family: 'Alex Brush', 'Great Vibes', cursive;
  font-size: 2.5rem;
  color: #4a0e0e;
  line-height: 1.1;
  margin: 0.25rem 0 0;
}

/* ══════════════════════════════════════════════════════════════════════════
   7. FOOTER
   ══════════════════════════════════════════════════════════════════════════ */
.video-theme-footer {
  width: 100%;
  background: #0f172a;
  padding: 1.5rem 1rem 3rem;
  text-align: center;
}

.footer-inner {
  display: flex;
  justify-content: center;
}

.footer-brand-pill {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  padding: 0.5rem 1.2rem;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(251, 191, 36, 0.35);
  border-radius: 999px;
  color: #fed7aa;
  text-decoration: none;
  font-size: 0.85rem;
  transition: all 0.25s ease;
}

.footer-brand-pill strong {
  color: #fbbf24;
}

.footer-brand-pill:hover {
  background: rgba(251, 191, 36, 0.15);
  border-color: #fbbf24;
  transform: translateY(-2px);
}
</style>
