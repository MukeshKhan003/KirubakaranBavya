<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// ══════════════════════════════════════════════════════════════════════════
// 0. MULTILINGUAL TRANSLATIONS (ENGLISH & TAMIL)
// Default: Tamil. If URL contains /lang-en or ?lang=en, switches to English.
// ══════════════════════════════════════════════════════════════════════════
const currentLang = ref('tm')

const translations = {
  en: {
    // Entrance Doors
    entryKickerTop: 'With all our hearts',
    entryTitle: 'You are invited',
    entryKickerSub: 'to celebrate with us',
    openInvitationBtn: 'OPEN INVITATION',
    entryHint: 'Tap to open the doors',

    // Sacred Invocations Header Quote (Shown only after door opens)
    headerQuote: 'நமசிவாய எனும் மங்கள நாதத்துடன்… சிவனும் சக்தியும் சாட்சியாக, இரு உள்ளங்கள் இணையும் திருநாள்…',

    // Hero Section
    togetherWithFamilies: 'Together with our families',
    heroInviteCopy: 'We cordially invite you to celebrate the auspicious engagement of',
    groomParents: 'Mr. K.M. Mohan & Mrs. M. Akila Priya',
    groomRelation: 'Son of',
    groomName: 'Kirubakaran',
    weds: 'weds',
    brideParents: 'Mr. R. Nakkeeran & Mrs. N. Selvarani',
    brideRelation: 'Daughter of',
    brideName: 'Bhavya',
    heroDate: '25 • OCTOBER • 2026',
    scrollPrompt: '↓ SCROLL TO DISCOVER OUR STORY ↓',

    // Scratch Card Date Section
    dateSectionTitle: 'Our Special Date',
    scratchPromptSub: 'Scratch to reveal the date',
    scratchDay: 'SUNDAY',
    scratchDate: '25-10-2026',
    scratchCanvasLabel: 'Scratch to reveal Sunday, 25-10-2026',
    scratchCanvasFoil: '✦ Scratch here ✦',
    scratchTip: '✦ Gently reveal our special date ✦',

    // Engagement Event Section
    timelineTitle: 'Engagement Ceremony',
    timelineSub: 'A sacred beginning · Two souls united in love',
    eventTitle: 'Engagement Ceremony',
    eventDayDate: 'Sunday • 25th October 2026',
    eventTime: '10:30 AM – 12:00 PM • Auspicious Muhurtham',
    venueName: 'Sornam Arumugam Marriage Hall',
    venueAddress: 'Tittagudi, Cuddalore District, Tamil Nadu – 606106, India',
    viewLocationBtn: '📍 View on Google Maps',
    addToCalendarBtn: '📅 Add to Google Calendar',

    // Countdown Section
    countEyebrow: 'Big day is getting closer',
    countTitle: 'Counting Down to Forever',
    countSub: 'Every second brings us closer to celebrating with you.',
    daysLabel: 'Days',
    hoursLabel: 'Hours',
    minsLabel: 'Minutes',
    secsLabel: 'Seconds',
    countFooterDate: '25 OCTOBER 2026 · 10:30 AM',

    // Guest Note Section
    guestKicker: 'A little note for you',
    guestTitle: 'Dear Guest',
    guestSub: 'From our hearts to yours',
    guestMessage: 'We found our moment, and now we’re making it a lifetime. Come share the laughter, the love, and the beginning of our forever. As we step into this beautiful new chapter together, your presence, love, and blessings would make our special day even more meaningful and fill our hearts with joy.',
    signatureWithLove: 'With love,',
    signatureNames: 'Kirubakaran & Bhavya',

    // Footer
    madeWithLove: 'Made with love by',
    toggleMusicPlay: 'Play background music',
    toggleMusicMute: 'Mute background music'
  },
  tm: {
    // Entrance Doors
    entryKickerTop: 'இரு உள்ளங்கள் இணையும் திருநாள்',
    entryTitle: 'அன்புடன் அழைக்கிறோம்',
    entryKickerSub: 'எங்கள் நிச்சயதார்த்த விழாவிற்கு',
    openInvitationBtn: 'அழைப்பிதழை திறக்கவும்',
    entryHint: 'கதவைத் தட்டி திறக்கவும்',

    // Sacred Invocations Header Quote (Shown only after door opens)
    headerQuote: 'நமசிவாய எனும் மங்கள நாதத்துடன்… சிவனும் சக்தியும் சாட்சியாக, இரு உள்ளங்கள் இணையும் திருநாள்…',

    // Hero Section
    togetherWithFamilies: 'எங்கள் குடும்பத்தாருடன் இணைந்து',
    heroInviteCopy: 'அன்புடன் எங்களது சுப நிச்சயதார்த்த விழாவிற்கு தங்களை அழைக்கிறோம்',
    groomParents: 'திரு. K.M. மோகன் & திருமதி. M. அகில பிரியா',
    groomRelation: 'அவர்களின் புதல்வருமான',
    groomName: 'கிருபாகரன்',
    weds: 'இணையும்',
    brideParents: 'திரு. R. நக்கீரன் & திருமதி. N. செல்வராணி',
    brideRelation: 'அவர்களின் புதல்வியுமான',
    brideName: 'பவ்யா',
    heroDate: '25 • அக்டோபர் • 2026',
    scrollPrompt: '↓ எங்களின் கதையைக் காண கீழே செல்லவும் ↓',

    // Scratch Card Date Section
    dateSectionTitle: 'எங்களின் நன்னாள்',
    scratchPromptSub: 'தேதியைக் காண உரசவும்',
    scratchDay: 'ஞாயிற்றுக்கிழமை',
    scratchDate: '25-10-2026',
    scratchCanvasLabel: 'தேதியை பார்க்க உரசவும் 25-10-2026',
    scratchCanvasFoil: '✦ இங்கே உரசவும் ✦',
    scratchTip: '✦ எங்களின் நன்னாளைக் காண உரசவும் ✦',

    // Engagement Event Section
    timelineTitle: 'சுப நிச்சயதார்த்த விழா',
    timelineSub: 'புனிதமான தொடக்கம் · இரு உள்ளங்களின் சங்கமம்',
    eventTitle: 'சுப நிச்சயதார்த்த விழா',
    eventDayDate: 'ஞாயிற்றுக்கிழமை • 25 அக்டோபர் 2026',
    eventTime: 'காலை 10:30 – நண்பகல் 12:00 • சுப முகூர்த்தம்',
    venueName: 'சொர்ணம் ஆறுமுகம் திருமண மண்டபம்',
    venueAddress: 'திட்டக்குடி, கடலூர் மாவட்டம், தமிழ்நாடு – 606106',
    viewLocationBtn: '📍 வரைபடத்தில் பார்க்க (Maps)',
    addToCalendarBtn: '📅 காலண்டரில் சேர்க்க (Calendar)',

    // Countdown Section
    countEyebrow: 'மங்கள நன்னாள் நெருங்குகிறது',
    countTitle: 'நன்னாளை நோக்கிய தருணங்கள்',
    countSub: 'ஒவ்வொரு நொடியும் உங்களுடன் கொண்டாடும் தருணத்தை நோக்கி நகர்கிறது.',
    daysLabel: 'நாட்கள்',
    hoursLabel: 'மணி',
    minsLabel: 'நிமிடம்',
    secsLabel: 'நொடிகள்',
    countFooterDate: '25 அக்டோபர் 2026 · காலை 10:30',

    // Guest Note Section
    guestKicker: 'எங்கள் இதயத்திலிருந்து ஒரு மடல்',
    guestTitle: 'அன்பான விருந்தினரே',
    guestSub: 'எங்கள் இதயத்தின் வாழ்த்துக்கள்',
    guestMessage: 'இறைவன் அருளால் இரு குடும்பங்களின் சம்மதத்துடன் எங்கள் வாழ்வின் வசந்த காலத்தை துவங்குகிறோம். அன்பும் மகிழ்ச்சியும் நிறைந்த இந்த நன்னாளில் தங்களின் வருகையும் வாழ்த்துக்களும் எங்கள் புதிய வாழ்விற்கு உன்னத ஆசீர்வாதமாக அமையும் என அன்புடன் வேண்டுகிறோம்.',
    signatureWithLove: 'அன்புடன்,',
    signatureNames: 'கிருபாகரன் & பவ்யா',

    // Footer
    madeWithLove: 'அன்புடன் உருவாக்கியது',
    toggleMusicPlay: 'இசையை இயக்கவும்',
    toggleMusicMute: 'இசையை நிறுத்தவும்'
  }
}

// Active translation accessor
const t = computed(() => translations[currentLang.value] || translations.en)

// Synchronize Language from URL (/lang-en or /lang-tm)
// Defaults to Tamil on initial page load unless English (/lang-en or ?lang=en) is requested
const syncLangFromUrl = () => {
  if (typeof window === 'undefined') return
  const path = window.location.pathname.toLowerCase()
  const search = window.location.search.toLowerCase()
  const hash = window.location.hash.toLowerCase()
  if (path.includes('lang-en') || search.includes('lang=en') || hash.includes('lang-en')) {
    currentLang.value = 'en'
  } else {
    currentLang.value = 'tm'
  }
}

// Language Switcher Function
const setLanguage = (lang) => {
  currentLang.value = lang
  const targetPath = lang === 'tm' ? '/lang-tm' : '/lang-en'
  if (window.location.pathname !== targetPath) {
    window.history.pushState({ lang }, '', targetPath)
  }
}

// ══════════════════════════════════════════════════════════════════════════
// 1. STATE & AUDIO CONTROLS
// ══════════════════════════════════════════════════════════════════════════
const isEntryOpen = ref(false)
const isMainVisible = ref(false)
const isPlayingMusic = ref(false)
const bgm = ref(null)

// 52-second seamless invitation audio excerpt from original reel
const MUSIC_SEGMENT_END = 52

const openInvitation = () => {
  if (isEntryOpen.value) return
  isEntryOpen.value = true

  // Play background audio automatically upon user interaction
  if (bgm.value) {
    bgm.value.currentTime = 0
    bgm.value.play().then(() => {
      isPlayingMusic.value = true
    }).catch(err => {
      console.warn('Audio play notice:', err)
    })
  }

  // Smooth entrance transition
  setTimeout(() => {
    isMainVisible.value = true
  }, 450)
}

const toggleMusic = async () => {
  if (!bgm.value) return
  if (bgm.value.paused) {
    try {
      if (bgm.value.currentTime >= MUSIC_SEGMENT_END - 0.1) {
        bgm.value.currentTime = 0
      }
      await bgm.value.play()
      isPlayingMusic.value = true
    } catch (e) {
      console.warn(e)
    }
  } else {
    bgm.value.pause()
    isPlayingMusic.value = false
  }
}

const onAudioTimeUpdate = () => {
  if (!bgm.value) return
  if (bgm.value.currentTime >= MUSIC_SEGMENT_END - 0.08) {
    bgm.value.currentTime = 0
    if (isPlayingMusic.value) {
      bgm.value.play().catch(() => {})
    }
  }
}

// ══════════════════════════════════════════════════════════════════════════
// 2. LIVE COUNTDOWN TIMER (INDIA TIME TARGET: 25 OCT 2026, 10:30 AM)
// ══════════════════════════════════════════════════════════════════════════
const targetTime = new Date('2026-10-25T10:30:00+05:30').getTime()
const countdown = ref({
  days: '000',
  hours: '00',
  mins: '00',
  secs: '00'
})
let countdownInterval = null

const updateCountdown = () => {
  const diff = Math.max(0, targetTime - Date.now())
  const d = Math.floor(diff / 86400000)
  const h = Math.floor((diff % 86400000) / 3600000)
  const m = Math.floor((diff % 3600000) / 60000)
  const s = Math.floor((diff % 60000) / 1000)

  countdown.value = {
    days: String(d).padStart(3, '0'),
    hours: String(h).padStart(2, '0'),
    mins: String(m).padStart(2, '0'),
    secs: String(s).padStart(2, '0')
  }
}

// ══════════════════════════════════════════════════════════════════════════
// 3. RETINA HIGH-DPI SCRATCH CARD & CELEBRATION PETALS
// ══════════════════════════════════════════════════════════════════════════
const scratchCanvasRef = ref(null)
const isCardRevealed = ref(false)
const petalsLayer = ref(null)
let scratchCtx = null
let isDown = false
let lastX = 0
let lastY = 0
let totalDistance = 0

const showerPetals = () => {
  const layer = petalsLayer.value
  if (!layer) return

  for (let i = 0; i < 48; i++) {
    const p = document.createElement('div')
    p.className = 'petal'
    p.style.left = (Math.random() * 100) + 'vw'
    p.style.animationDuration = (2.5 + Math.random() * 2) + 's'
    p.style.animationDelay = (Math.random() * 0.65) + 's'
    p.style.setProperty('--drift', (Math.random() * 160 - 80) + 'px')
    p.style.transform = 'rotate(' + (Math.random() * 360) + 'deg)'
    layer.appendChild(p)
    setTimeout(() => p.remove(), 5200)
  }
}

const revealScratch = () => {
  if (isCardRevealed.value) return
  isCardRevealed.value = true
  showerPetals()
}

const initScratchCanvas = () => {
  const canvas = scratchCanvasRef.value
  if (!canvas) return
  scratchCtx = canvas.getContext('2d')
  if (!scratchCtx) return

  const r = canvas.getBoundingClientRect()
  const dpr = Math.min(2, window.devicePixelRatio || 1)
  canvas.width = Math.round(r.width * dpr)
  canvas.height = Math.round(r.height * dpr)

  scratchCtx.setTransform(dpr, 0, 0, dpr, 0, 0)
  scratchCtx.globalCompositeOperation = 'source-over'
  scratchCtx.clearRect(0, 0, r.width, r.height)

  // Pure metallic gold gradient exactly from original template
  const g = scratchCtx.createLinearGradient(0, 0, r.width, r.height)
  g.addColorStop(0, '#f4d77c')
  g.addColorStop(0.48, '#b77a24')
  g.addColorStop(1, '#e8bd58')
  scratchCtx.fillStyle = g
  scratchCtx.fillRect(0, 0, r.width, r.height)

  // Foil prompt typography
  scratchCtx.fillStyle = 'rgba(255, 246, 205, 0.55)'
  scratchCtx.font = '600 17px serif'
  scratchCtx.textAlign = 'center'
  scratchCtx.fillText(t.value.scratchCanvasFoil, r.width / 2, r.height / 2 + 6)
}

const onPointerDown = (e) => {
  if (isCardRevealed.value) return
  isDown = true
  const canvas = scratchCanvasRef.value
  if (!canvas) return
  if (canvas.setPointerCapture) {
    try { canvas.setPointerCapture(e.pointerId) } catch (err) {}
  }
  const r = canvas.getBoundingClientRect()
  lastX = e.clientX - r.left
  lastY = e.clientY - r.top
  wipe(e)
}

const wipe = (e) => {
  if (!isDown || isCardRevealed.value || !scratchCtx) return
  const canvas = scratchCanvasRef.value
  if (!canvas) return
  const r = canvas.getBoundingClientRect()
  const x = e.clientX - r.left
  const y = e.clientY - r.top
  const dx = x - lastX
  const dy = y - lastY
  totalDistance += Math.hypot(dx, dy)
  lastX = x
  lastY = y

  scratchCtx.globalCompositeOperation = 'destination-out'
  scratchCtx.lineCap = 'round'
  scratchCtx.lineJoin = 'round'
  scratchCtx.lineWidth = 48
  scratchCtx.beginPath()
  scratchCtx.moveTo(x - dx, y - dy)
  scratchCtx.lineTo(x, y)
  scratchCtx.stroke()
  scratchCtx.beginPath()
  scratchCtx.arc(x, y, 22, 0, Math.PI * 2)
  scratchCtx.fill()

  // After 2–3 gentle sweeps, smoothly reveal date
  if (totalDistance >= 140) {
    revealScratch()
  }
}

const onPointerUp = () => {
  isDown = false
}

// ══════════════════════════════════════════════════════════════════════════
// 4. VENUE LOCATION & ADD TO CALENDAR
// ══════════════════════════════════════════════════════════════════════════
const googleMapsLocationUrl = 'https://maps.app.goo.gl/8r8R7PVDQ1oatWbVA'

const googleCalendarUrl = computed(() => {
  const isTm = currentLang.value === 'tm'
  const text = isTm 
    ? 'சுப நிச்சயதார்த்த விழா – கிருபாகரன் & பவ்யா'
    : 'Engagement Ceremony – M. Kirubakaran & N. Bhavya'
  const details = isTm
    ? 'M. கிருபாகரன் மற்றும் N. பவ்யா ஆகியோரின் சுப நிச்சயதார்த்த விழாவிற்கு எங்கள் குடும்பத்தாருடன் இணைந்து தங்களை அன்போடு அழைக்கிறோம்.'
    : 'Auspicious Engagement Ceremony of M. Kirubakaran and N. Bhavya. Together with our families, we cordially invite you to celebrate with us.'
  const location = 'Sornam Arumugam Marriage Hall, Tittagudi, Cuddalore District, Tamil Nadu – 606106'

  return 'https://calendar.google.com/calendar/render?action=TEMPLATE' +
    '&text=' + encodeURIComponent(text) +
    '&dates=20261025T050000Z%2F20261025T063000Z' +
    '&details=' + encodeURIComponent(details) +
    '&location=' + encodeURIComponent(location)
})

// ══════════════════════════════════════════════════════════════════════════
// 5. SCROLL REVEAL OBSERVER & LIFECYCLE
// ══════════════════════════════════════════════════════════════════════════
let revealObserver = null

onMounted(() => {
  syncLangFromUrl()
  if (typeof window !== 'undefined') {
    window.addEventListener('popstate', syncLangFromUrl)
  }

  updateCountdown()
  countdownInterval = setInterval(updateCountdown, 1000)

  // Initialize scratch card after DOM layout
  setTimeout(() => {
    initScratchCanvas()
  }, 350)
  window.addEventListener('resize', initScratchCanvas)

  // Intersection observer for section rise animations
  revealObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('in-view')
        revealObserver.unobserve(entry.target)
      }
    })
  }, { threshold: 0.1, rootMargin: '0px 0px -6% 0px' })

  document.querySelectorAll('.reveal-section').forEach(el => {
    revealObserver.observe(el)
  })
})

onUnmounted(() => {
  if (typeof window !== 'undefined') {
    window.removeEventListener('popstate', syncLangFromUrl)
  }
  if (countdownInterval) clearInterval(countdownInterval)
  window.removeEventListener('resize', initScratchCanvas)
  if (revealObserver) revealObserver.disconnect()
  if (bgm.value) bgm.value.pause()
})
</script>

<template>
  <div class="original-template-root" :class="{ 'lang-tamil': currentLang === 'tm' }">

    <!-- ══════════════════════════════════════════════════════════════════════
         BACKGROUND AUDIO PLAYER & FLOATING MUSIC TOGGLE BUTTON
         ══════════════════════════════════════════════════════════════════════ -->
    <audio 
      ref="bgm" 
      src="/audio/original_wedding_song.mp3" 
      preload="auto" 
      playsinline 
      webkit-playsinline
      @timeupdate="onAudioTimeUpdate"
    ></audio>

    <button 
      class="music-btn" 
      id="musicBtn" 
      type="button" 
      @click="toggleMusic"
      :aria-label="isPlayingMusic ? t.toggleMusicMute : t.toggleMusicPlay"
    >
      {{ isPlayingMusic ? '♫' : '♪' }}
    </button>

    <!-- ══════════════════════════════════════════════════════════════════════
         ENTRANCE: ORIGINAL HIGH-RES TEMPLE DOOR WITH 2-PANEL SLIDE
         ══════════════════════════════════════════════════════════════════════ -->
    <div id="entry" :class="{ open: isEntryOpen }">
      <div class="door-stage">
        <div class="door-half door-left"></div>
        <div class="door-half door-right"></div>
        <div class="entry-shade"></div>
      </div>

      <div class="entry-content">
        <p class="entry-kicker">{{ t.entryKickerTop }}</p>
        <h1 class="entry-title">{{ t.entryTitle }}</h1>
        <p class="entry-kicker entry-kicker-sub">{{ t.entryKickerSub }}</p>

        <button class="open-btn" id="openBtn" type="button" @click="openInvitation">
          {{ t.openInvitationBtn }}
        </button>

        <p class="entry-hint">{{ t.entryHint }}</p>
      </div>
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         MAIN INVITATION SECTIONS
         ══════════════════════════════════════════════════════════════════════ -->
    <main id="main" :class="{ visible: isMainVisible }">

      <!-- 1. HERO SECTION -->
      <section class="hero" :aria-label="t.timelineTitle">
        <!-- Sacred Invocations Top Header Quote (Visible only after door opened) -->
        <div v-if="isEntryOpen" class="hero-quote-banner" role="banner">
          <span class="quote-symbol">🕉️</span>
          <p class="quote-text">{{ t.headerQuote }}</p>
        </div>

        <div class="hero-inner">
          <div class="eyebrow">{{ t.togetherWithFamilies }}</div>
          <p class="hero-copy">{{ t.heroInviteCopy }}</p>

          <div class="names">
            <div class="parents parents-groom">
              <span>{{ t.groomParents }}</span>
            </div>
            <div class="relation-text">
              <span>({{ t.groomRelation }})</span>
            </div>
            <span class="name-kiruba">{{ t.groomName }}</span>

            <span class="weds">{{ t.weds }}</span>

            <div class="parents parents-bride">
              <span>{{ t.brideParents }}</span>
            </div>
            <div class="relation-text">
              <span>({{ t.brideRelation }})</span>
            </div>
            <span class="name-bhavya">{{ t.brideName }}</span>
          </div>

          <div class="hero-date">{{ t.heroDate }}</div>
          <div class="discover">{{ t.scrollPrompt }}</div>
        </div>
      </section>

      <!-- 2. DATE & SCRATCH CARD SECTION -->
      <section class="reveal-section date-section" id="date">
        <div class="date-inner">
          <h2 class="section-title">{{ t.dateSectionTitle }}</h2>
          <div class="sub">{{ t.scratchPromptSub }}</div>

          <div class="scratch-box-wrap">
            <div class="scratch-box" :class="{ revealed: isCardRevealed }">
              <div class="scratch-reveal-text">
                <div class="scratch-day">{{ t.scratchDay }}</div>
                <div class="scratch-date">{{ t.scratchDate }}</div>
              </div>

              <canvas 
                ref="scratchCanvasRef" 
                class="scratch-canvas" 
                :aria-label="t.scratchCanvasLabel"
                @pointerdown="onPointerDown"
                @pointermove="wipe"
                @pointerup="onPointerUp"
                @pointercancel="onPointerUp"
                @pointerleave="onPointerUp"
              ></canvas>
            </div>
          </div>

          <div class="scratch-tip" @click="revealScratch">
            {{ t.scratchTip }}
          </div>
        </div>
      </section>

      <!-- 3. ENGAGEMENT TIMELINE & EVENT SECTION -->
      <section class="reveal-section timeline" id="timeline">
        <div class="timeline-inner">
          <h2 class="section-title timeline-title">{{ t.timelineTitle }}</h2>
          <div class="sub">{{ t.timelineSub }}</div>

          <div class="timeline-journey">

            <!-- Event: Engagement Ceremony -->
            <article class="timeline-event">
              <div class="timeline-photo">
                <img src="/images/original-assets/timeline-reception.jpg" alt="Engagement ceremony celebration" />
              </div>
              <div class="timeline-card">
                <h3>{{ t.eventTitle }}</h3>
                <div class="timeline-meta">
                  <span>{{ t.eventDayDate }}</span>
                </div>
                <div class="timeline-time">{{ t.eventTime }}</div>
                <div class="timeline-location">
                  <strong>{{ t.venueName }}</strong><br>
                  {{ t.venueAddress }}
                </div>
                <div class="timeline-actions">
                  <a 
                    class="timeline-btn" 
                    target="_blank" 
                    rel="noopener" 
                    :href="googleMapsLocationUrl"
                  >
                    {{ t.viewLocationBtn }}
                  </a>
                  <a 
                    class="timeline-btn" 
                    target="_blank" 
                    rel="noopener" 
                    :href="googleCalendarUrl"
                  >
                    {{ t.addToCalendarBtn }}
                  </a>
                </div>
              </div>
            </article>

          </div>
        </div>
      </section>

      <!-- 4. COUNTDOWN SECTION -->
      <section class="reveal-section countdown" id="countdown">
        <div class="count-panel">
          <div class="eyebrow" style="color:#f4d99a;margin-bottom:12px">{{ t.countEyebrow }}</div>
          <h2 class="section-title count-title">{{ t.countTitle }}</h2>
          <div class="sub count-sub">{{ t.countSub }}</div>

          <div class="count-grid" role="timer" aria-live="polite">
            <div class="count-card">
              <div class="count-num" id="days">{{ countdown.days }}</div>
              <div class="count-label">{{ t.daysLabel }}</div>
            </div>
            <div class="count-card">
              <div class="count-num" id="hours">{{ countdown.hours }}</div>
              <div class="count-label">{{ t.hoursLabel }}</div>
            </div>
            <div class="count-card">
              <div class="count-num" id="mins">{{ countdown.mins }}</div>
              <div class="count-label">{{ t.minsLabel }}</div>
            </div>
            <div class="count-card">
              <div class="count-num" id="secs">{{ countdown.secs }}</div>
              <div class="count-label">{{ t.secsLabel }}</div>
            </div>
          </div>

          <div class="count-date">{{ t.countFooterDate }}</div>
        </div>
      </section>

      <!-- 5. GUEST NOTE SECTION -->
      <section class="reveal-section guest" id="guest">
        <div class="guest-card">
          <div class="guest-kicker">{{ t.guestKicker }}</div>
          <h2 class="section-title guest-title">{{ t.guestTitle }}</h2>
          <div class="sub guest-sub">{{ t.guestSub }}</div>

          <div class="guest-message">
            {{ t.guestMessage }}
          </div>

          <div class="signature">
            <div class="signature-tag">{{ t.signatureWithLove }}</div>
            <div class="signature-names">{{ t.signatureNames }}</div>
          </div>
        </div>
      </section>

      <!-- 6. FOOTER -->
      <footer class="reveal-section footer">
        <!-- Language Switcher Buttons (Bottom of the page: English / தமிழ்) -->
        <div class="footer-lang-switcher" aria-label="Language selection">
          <button 
            type="button"
            class="lang-switch-btn"
            :class="{ active: currentLang === 'en' }"
            @click="setLanguage('en')"
            title="View in English"
          >
            English
          </button>
          <span class="lang-switch-sep">•</span>
          <button 
            type="button"
            class="lang-switch-btn"
            :class="{ active: currentLang === 'tm' }"
            @click="setLanguage('tm')"
            title="தமிழில் பார்க்க"
          >
            தமிழ்
          </button>
        </div>

        <div class="footer-love">{{ t.madeWithLove }}</div>
        <a 
          class="instagram-btn" 
          href="https://invitesend.com" 
          target="_blank" 
          rel="noopener" 
          aria-label="Open InviteSend.com"
        >
          <span>InviteSend.com</span>
        </a>
      </footer>

    </main>

    <!-- Floating Flower Petals Container -->
    <div class="petal-layer" id="petals" ref="petalsLayer"></div>

  </div>
</template>

<style scoped>
/* ══════════════════════════════════════════════════════════════════════════
   ORIGINAL THEME DESIGN SYSTEM & CSS VARIABLES
   ══════════════════════════════════════════════════════════════════════════ */
.original-template-root {
  --gold: #c99a3e;
  --gold2: #f1d58b;
  --cream: #fff7e8;
  --ink: #243447;
  --brown: #173a63;
  --rose: #a73f50;
  --green: #3d7357;
  --shadow: 0 20px 70px rgba(18, 38, 58, 0.22);
  position: relative;
  width: 100%;
  min-height: 100vh;
  background: #20130c;
  color: var(--ink);
  font-family: 'Montserrat', system-ui, sans-serif;
  overflow-x: hidden;
}

section {
  position: relative;
  min-height: 100svh;
  overflow: hidden;
}

/* Scroll reveal sections */
section.reveal-section {
  opacity: 0.01;
  transform: translateY(28px);
  transition: opacity 1s cubic-bezier(0.22, 1, 0.36, 1), transform 1s cubic-bezier(0.22, 1, 0.36, 1);
}

section.reveal-section.in-view {
  opacity: 1;
  transform: none;
}

section.reveal-section > * {
  opacity: 0;
  transform: translateY(18px);
  transition: opacity 0.85s cubic-bezier(0.22, 1, 0.36, 1), transform 0.85s cubic-bezier(0.22, 1, 0.36, 1);
}

section.reveal-section.in-view > * {
  opacity: 1;
  transform: none;
}

/* ══════════════════════════════════════════════════════════════════════════
   ENTRANCE: SLIDING SPLIT TEMPLE DOORS
   ══════════════════════════════════════════════════════════════════════════ */
#entry {
  position: fixed;
  inset: 0;
  z-index: 1000;
  background: #160d08;
  display: grid;
  place-items: center;
  overflow: hidden;
  transition: opacity 0.75s ease;
}

#entry.open {
  pointer-events: none;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.6s ease 1.5s, visibility 0s linear 2.1s;
}

.door-stage {
  position: absolute;
  inset: 0;
  overflow: hidden;
  background: #120a06;
}

.door-half {
  position: absolute;
  top: 0;
  width: 50%;
  height: 100%;
  background-image: url('/images/original-assets/door.jpg');
  background-repeat: no-repeat;
  background-size: 200% 100%;
  transition: transform 1.65s cubic-bezier(0.77, 0, 0.18, 1);
  filter: saturate(0.96);
}

.door-left {
  left: 0;
  background-position: left center;
}

.door-right {
  right: 0;
  background-position: right center;
}

#entry.open .door-left {
  transform: translateX(-101%);
}

#entry.open .door-right {
  transform: translateX(101%);
}

.entry-shade {
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at 50% 44%, rgba(255, 222, 143, 0.08), transparent 34%),
              linear-gradient(180deg, rgba(0, 0, 0, 0.18), rgba(0, 0, 0, 0.48));
}

.entry-content {
  position: relative;
  z-index: 5;
  text-align: center;
  color: white;
  width: min(720px, 92vw);
  padding: 30px 20px;
  transition: opacity 0.55s ease, transform 0.65s ease;
}

#entry.open .entry-content {
  opacity: 0;
  transform: scale(1.045);
}

.entry-kicker {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(22px, 4.2vw, 34px);
  font-weight: 600;
  letter-spacing: 3px;
  color: #fff6df;
  text-shadow: 0 2px 14px rgba(0, 0, 0, 0.95), 0 0 24px rgba(0, 0, 0, 0.85);
  margin-bottom: 14px;
}

.entry-kicker-sub {
  font-size: clamp(20px, 3.6vw, 28px);
  font-weight: 500;
  letter-spacing: 2px;
  color: #faebd0;
  margin-bottom: 34px;
  text-shadow: 0 2px 14px rgba(0, 0, 0, 0.95), 0 0 24px rgba(0, 0, 0, 0.85);
}

.entry-title {
  font-family: 'Bodoni Moda', serif;
  font-size: clamp(54px, 11vw, 92px);
  font-weight: 700;
  line-height: 1.05;
  margin: 0 0 28px;
  color: #ffe9b8;
  letter-spacing: 1px;
  text-shadow: 0 4px 28px rgba(0, 0, 0, 0.95), 0 0 35px rgba(0, 0, 0, 0.85);
}

.open-btn {
  border: 1.5px solid #ffe199;
  background: linear-gradient(135deg, #f7df9e, #c68c2d);
  color: #271407;
  padding: 16px 42px;
  border-radius: 999px;
  cursor: pointer;
  letter-spacing: 2.2px;
  font-size: 14px;
  font-weight: 700;
  box-shadow: 0 12px 36px rgba(0, 0, 0, 0.6);
  transition: transform 0.25s, box-shadow 0.25s;
}

.open-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 16px 42px rgba(0, 0, 0, 0.7);
}

.entry-hint {
  margin-top: 16px;
  font-size: 13px;
  letter-spacing: 2px;
  font-weight: 500;
  color: #fff2d6;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.9);
  opacity: 0.92;
}

/* ══════════════════════════════════════════════════════════════════════════
   MAIN BODY & MUSIC TOGGLE
   ══════════════════════════════════════════════════════════════════════════ */
#main {
  opacity: 0;
  transform: translateY(18px);
  transition: opacity 1s ease 0.55s, transform 1s ease 0.55s;
}

#main.visible {
  opacity: 1;
  transform: none;
}

.music-btn {
  position: fixed;
  right: 18px;
  bottom: 20px;
  z-index: 100;
  width: 44px;
  height: 44px;
  border-radius: 50%;
  border: 1.5px solid rgba(245, 218, 157, 0.85);
  background: rgba(30, 10, 8, 0.88);
  color: #f5d58c;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  cursor: pointer;
  font-size: 19px;
  line-height: 1;
  box-shadow: 0 6px 22px rgba(0, 0, 0, 0.5), 0 0 14px rgba(245, 218, 157, 0.25);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.25s ease, background 0.25s ease, box-shadow 0.25s ease;
}

.music-btn:hover {
  transform: scale(1.08);
  box-shadow: 0 8px 26px rgba(0, 0, 0, 0.6), 0 0 18px rgba(245, 218, 157, 0.4);
}

/* ══════════════════════════════════════════════════════════════════════════
   1. HERO SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.hero {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  color: var(--ink);
  background: url('/images/original-assets/hero-new.jpg') center top / cover no-repeat;
  padding: 70px 16px 30px;
  min-height: 100vh;
}

/* Sacred Invocations Top Header Quote (Pinned to the very top, visible only after door opened) */
.hero-quote-banner {
  position: absolute;
  top: 14px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 25;
  width: max-content;
  max-width: min(92vw, 760px);
  padding: 6px 20px;
  background: rgba(30, 8, 8, 0.9);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(220, 180, 105, 0.6);
  border-radius: 999px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5), 0 0 16px rgba(220, 180, 105, 0.2);
  animation: heroQuoteFadeDown 1.1s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

.hero-quote-banner .quote-symbol {
  font-size: 13px;
  line-height: 1;
}

.hero-quote-banner .quote-text {
  font-family: 'Noto Serif Tamil', Georgia, serif;
  font-size: clamp(11.5px, 1.9vw, 13.5px);
  font-weight: 500;
  color: #fff2d2;
  letter-spacing: 0.3px;
  line-height: 1.4;
  margin: 0;
  text-shadow: 0 1px 4px rgba(0, 0, 0, 0.6);
}

@keyframes heroQuoteFadeDown {
  0% {
    opacity: 0;
    transform: translate(-50%, -18px);
  }
  100% {
    opacity: 1;
    transform: translate(-50%, 0);
  }
}

.hero-inner {
  position: relative;
  z-index: 2;
  width: min(920px, 92vw);
  padding: 50px 24px 44px;
  border: 1px solid rgba(220, 180, 105, 0.65);
  border-radius: 34px;
  /* Translucent glass so background painting shows through beautifully */
  background: rgba(255, 249, 237, 0.42);
  backdrop-filter: blur(2.5px);
  -webkit-backdrop-filter: blur(2.5px);
  box-shadow: 0 18px 60px rgba(39, 44, 60, 0.16);
}

.eyebrow {
  font-family: 'Montserrat', sans-serif;
  font-size: 11px;
  letter-spacing: 4px;
  text-transform: uppercase;
  color: #173a63;
  font-weight: 700;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.85);
}

.hero-copy {
  font-family: 'Playfair Display', serif;
  font-size: clamp(22px, 4vw, 36px);
  line-height: 1.3;
  margin: 18px auto 14px;
  max-width: 740px;
  color: #3b1425;
  font-weight: 600;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.9);
}

.names {
  font-family: 'Allura', cursive;
  font-size: clamp(74px, 14vw, 128px);
  font-weight: 400;
  letter-spacing: 0.5px;
  line-height: 0.88;
  margin: 8px 0;
}

.name-kiruba {
  display: block;
  font-family: 'Allura', cursive;
  font-weight: 400;
  color: #730707;
  letter-spacing: 0.5px;
  text-shadow: 
    0 1px 0 rgba(255, 255, 255, 0.95),
    0 2px 6px rgba(255, 255, 255, 0.9),
    0 6px 20px rgba(115, 7, 7, 0.22);
}

.name-bhavya {
  display: block;
  font-family: 'Allura', cursive;
  font-weight: 400;
  color: #730707;
  letter-spacing: 0.5px;
  text-shadow: 
    0 1px 0 rgba(255, 255, 255, 0.95),
    0 2px 6px rgba(255, 255, 255, 0.9),
    0 6px 20px rgba(115, 7, 7, 0.22);
}

.names .weds {
  display: block;
  font-family: 'Playfair Display', serif;
  font-size: 0.26em;
  font-weight: 700;
  letter-spacing: 5px;
  margin: 10px 0 6px;
  color: #9a6a18;
  text-transform: uppercase;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.9);
}

.parents {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(17px, 2.8vw, 22px);
  font-weight: 600;
  line-height: 1.35;
  letter-spacing: 0.3px;
  color: #2b1f1a;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.95);
  margin: 6px auto 4px;
}

.parents span {
  display: block;
}

.relation-text {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(15px, 2.4vw, 19px);
  font-style: italic;
  font-weight: 500;
  color: #7d5930;
  letter-spacing: 0.5px;
  text-shadow: 0 1px 2px rgba(255, 255, 255, 0.95);
  margin: 2px auto 6px;
}

.relation-text span {
  display: block;
}

.hero-date {
  font-family: 'Montserrat', sans-serif;
  font-size: 13px;
  letter-spacing: 4px;
  color: #8f6421;
  margin-top: 22px;
  font-weight: 700;
}

.discover {
  margin-top: 36px;
  font-size: 11px;
  letter-spacing: 2px;
  font-weight: 600;
  color: #315676;
  opacity: 0.9;
}

/* ══════════════════════════════════════════════════════════════════════════
   2. DATE & SCRATCH CARD SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.date-section {
  display: grid;
  place-items: center;
  text-align: center;
  background: linear-gradient(rgba(247, 237, 221, 0.45), rgba(244, 231, 212, 0.65)),
              url('/images/original-assets/date-new.jpg') center / cover no-repeat;
  padding: 78px 18px;
}

.date-inner {
  width: min(820px, 92vw);
}

.section-title {
  font-family: 'Bodoni Moda', serif;
  font-size: clamp(34px, 5.5vw, 56px);
  color: #234b72;
  margin: 0;
}

.sub {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(18px, 3vw, 24px);
  font-style: italic;
  color: #7d5930;
  margin-top: 4px;
}

.scratch-box-wrap {
  margin: 36px auto 18px;
  display: grid;
  place-items: center;
}

.scratch-box {
  position: relative;
  width: min(390px, 86vw);
  height: 200px;
  border-radius: 26px;
  border: 1px solid rgba(198, 149, 58, 0.65);
  box-shadow: 0 16px 48px rgba(35, 75, 114, 0.22);
  background: #fff8ea;
  overflow: hidden;
  display: grid;
  place-items: center;
  touch-action: none;
}

.scratch-reveal-text {
  text-align: center;
  user-select: none;
}

.scratch-day {
  font-family: 'Playfair Display', serif;
  font-size: 26px;
  letter-spacing: 3px;
  color: #234b72;
  font-weight: 600;
}

.scratch-date {
  font-family: 'Bodoni Moda', serif;
  font-size: 38px;
  letter-spacing: 2px;
  color: #9f3547;
  line-height: 1.1;
  margin-top: 4px;
}

.scratch-canvas {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  border-radius: 26px;
  cursor: crosshair;
  transition: opacity 0.42s ease;
}

.scratch-box.revealed .scratch-canvas {
  opacity: 0;
  pointer-events: none;
}

.scratch-tip {
  font-size: 11px;
  letter-spacing: 2px;
  color: #8c6328;
  margin-top: 8px;
  cursor: pointer;
}

/* ══════════════════════════════════════════════════════════════════════════
   3. ENGAGEMENT TIMELINE SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.timeline {
  background: linear-gradient(rgba(13, 28, 61, 0.48), rgba(12, 24, 52, 0.66)),
              url('/images/original-assets/timeline-new.jpg') center / cover no-repeat;
  color: #fff8e9;
  padding: 78px 18px;
}

.timeline-inner {
  width: min(760px, 96vw);
  margin: 0 auto;
  text-align: center;
}

.timeline-title {
  font-family: 'Playfair Display', serif;
  text-transform: uppercase;
  letter-spacing: 3px;
  font-size: clamp(30px, 5vw, 44px);
  margin-bottom: 6px;
  color: #ffe1a0;
  text-shadow: 0 3px 16px rgba(0, 0, 0, 0.5);
}

.timeline-inner > .sub {
  font-family: 'Cormorant Garamond', serif;
  font-size: 22px;
  color: #fff1d1;
  font-style: italic;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.45);
}

.timeline-journey {
  position: relative;
  margin: 34px auto 0;
  width: min(620px, 94vw);
}

.timeline-journey:before {
  display: none;
}

.timeline-event {
  position: relative;
  margin: 0 auto 58px;
  z-index: 1;
}

.timeline-photo {
  width: min(410px, 82vw);
  height: 180px;
  margin: 0 auto -18px;
  overflow: hidden;
  border: 10px solid rgba(255, 249, 237, 0.92);
  border-bottom: 0;
  border-radius: 220px 220px 22px 22px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 221, 147, 0.45);
}

.timeline-photo img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.timeline-card {
  position: relative;
  background: rgba(255, 250, 239, 0.94);
  border: 1px solid rgba(201, 154, 62, 0.62);
  border-radius: 22px;
  padding: 30px 22px 24px;
  box-shadow: 0 14px 38px rgba(0, 0, 0, 0.22);
}

.timeline-card:before {
  content: '♥';
  position: absolute;
  top: -13px;
  left: 50%;
  transform: translateX(-50%);
  width: 27px;
  height: 27px;
  border-radius: 50%;
  background: #fff8ea;
  border: 1px solid #c99a3e;
  color: #a73f50;
  font-size: 13px;
  line-height: 26px;
}

.timeline-event h3 {
  font-family: 'Allura', cursive;
  font-size: 52px;
  font-weight: 400;
  color: #a73f50;
  margin: 0 0 4px;
}

.timeline-meta {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 18px;
  flex-wrap: wrap;
  font-family: 'Playfair Display', serif;
  color: #234b72;
  font-size: 15px;
  font-weight: 500;
}

.timeline-time {
  font-family: 'Playfair Display', serif;
  color: #234b72;
  font-size: 14px;
  margin-top: 4px;
  font-weight: 600;
}

.timeline-location {
  margin: 14px auto 0;
  font-family: 'Cormorant Garamond', serif;
  font-size: 17px;
  line-height: 1.45;
  color: #5d5146;
  max-width: 500px;
}

.timeline-actions {
  display: flex;
  justify-content: center;
  gap: 10px;
  flex-wrap: wrap;
  margin-top: 17px;
}

.timeline-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 10px 17px;
  border: 1px solid #c99a3e;
  border-radius: 999px;
  background: linear-gradient(135deg, #fff7e5, #f3dfad);
  color: #234b72;
  text-decoration: none;
  font-family: inherit;
  font-size: 11px;
  letter-spacing: 0.6px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.25s, transform 0.2s, box-shadow 0.2s;
}

.timeline-btn:hover {
  background: #fff5df;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(201, 154, 62, 0.35);
}

.timeline-event:last-child {
  margin-bottom: 0;
}

/* ══════════════════════════════════════════════════════════════════════════
   4. COUNTDOWN SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.countdown {
  display: grid;
  place-items: center;
  text-align: center;
  background: linear-gradient(rgba(17, 34, 56, 0.42), rgba(17, 34, 56, 0.68)),
              url('/images/original-assets/countdown-hand.jpg') center / cover no-repeat;
  color: #f7e8cb;
  padding: 78px 18px;
}

.count-panel {
  width: min(780px, 92vw);
  background: rgba(18, 38, 62, 0.72);
  border: 1px solid rgba(226, 190, 111, 0.45);
  border-radius: 32px;
  padding: 48px 24px;
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.35);
}

.count-title {
  color: #fff1cf;
  font-size: clamp(30px, 5vw, 48px);
}

.count-sub {
  color: #f0d59e;
  max-width: 520px;
  margin: 8px auto 32px;
}

.count-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 14px;
  max-width: 620px;
  margin: 0 auto;
}

.count-card {
  border: 1px solid rgba(226, 190, 111, 0.4);
  background: rgba(255, 255, 255, 0.04);
  border-radius: 18px;
  padding: 20px 8px;
}

.count-num {
  font-family: 'Bodoni Moda', serif;
  font-size: clamp(34px, 5.5vw, 54px);
  color: #ffe099;
  line-height: 1;
}

.count-label {
  font-size: 10px;
  letter-spacing: 2.5px;
  text-transform: uppercase;
  color: #f0cf85;
  margin-top: 8px;
}

.count-date {
  margin-top: 32px;
  font-size: 11px;
  letter-spacing: 3px;
  color: #ffe2a3;
}

/* ══════════════════════════════════════════════════════════════════════════
   5. GUEST NOTE SECTION
   ══════════════════════════════════════════════════════════════════════════ */
.guest {
  display: grid;
  place-items: center;
  text-align: center;
  background: linear-gradient(rgba(247, 237, 221, 0.45), rgba(244, 231, 212, 0.65)),
              url('/images/original-assets/guest-new.jpg') center / cover no-repeat;
  padding: 78px 18px;
}

.guest-card {
  width: min(820px, 92vw);
  background: rgba(255, 248, 238, 0.84);
  border: 1px solid rgba(198, 149, 58, 0.55);
  border-radius: 34px;
  padding: 54px 28px;
  box-shadow: 0 20px 60px rgba(35, 75, 114, 0.16);
  backdrop-filter: blur(3px);
  -webkit-backdrop-filter: blur(3px);
}

.guest-kicker {
  font-size: 11px;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: #7d5930;
  margin-bottom: 8px;
}

.guest-title {
  color: #234b72;
}

.guest-sub {
  color: #8c6328;
}

.guest-message {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(21px, 3.6vw, 27px);
  font-weight: 500;
  line-height: 1.58;
  color: #3b2a2d;
  max-width: 650px;
  margin: 24px auto;
  letter-spacing: 0.3px;
  text-shadow: 0 1px 1px rgba(255, 255, 255, 0.7);
}

.signature {
  margin-top: 28px;
}

.signature-tag {
  font-family: 'Cormorant Garamond', serif;
  font-size: 20px;
  color: #8c6328;
}

.signature-names {
  font-family: 'Allura', cursive;
  font-size: clamp(40px, 6vw, 56px);
  color: #9f3547;
  line-height: 1.1;
  margin-top: 4px;
}

/* ══════════════════════════════════════════════════════════════════════════
   6. FOOTER
   ══════════════════════════════════════════════════════════════════════════ */
.footer {
  background: linear-gradient(135deg, #102e50, #173a63 55%, #244d72);
  color: #f4d99a;
  text-align: center;
  padding: 30px 18px 36px;
  border-top: 1px solid rgba(218, 172, 84, 0.35);
}

/* Footer Language Switcher Pill */
.footer-lang-switcher {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  background: rgba(18, 38, 62, 0.82);
  border: 1px solid rgba(226, 190, 111, 0.55);
  border-radius: 999px;
  padding: 5px 12px;
  margin-bottom: 16px;
  box-shadow: 0 4px 18px rgba(0, 0, 0, 0.35);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
}

.lang-switch-btn {
  background: transparent;
  border: none;
  color: #f4d59e;
  font-family: inherit;
  font-size: 13px;
  font-weight: 600;
  padding: 4px 12px;
  border-radius: 999px;
  cursor: pointer;
  transition: all 0.25s ease;
  letter-spacing: 0.5px;
}

.lang-switch-btn:hover {
  color: #ffffff;
}

.lang-switch-btn.active {
  background: linear-gradient(135deg, #fff2cc, #f3d489);
  color: #173a63;
  font-weight: 800;
  box-shadow: 0 2px 10px rgba(243, 212, 137, 0.45);
}

.lang-switch-sep {
  color: rgba(226, 190, 111, 0.45);
  font-size: 11px;
}

.footer-love {
  font-family: 'Cormorant Garamond', serif;
  font-size: 14px;
  color: #f0dcc0;
  margin-bottom: 8px;
}

.instagram-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: #e6c67e;
  text-decoration: none;
  border: 1px solid rgba(226, 190, 111, 0.45);
  padding: 8px 18px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.05);
  font-size: 11px;
  letter-spacing: 1px;
  font-weight: 600;
  transition: transform 0.25s, background 0.25s, border-color 0.25s;
}

.instagram-btn:hover {
  transform: translateY(-2px);
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(226, 190, 111, 0.75);
}

/* ══════════════════════════════════════════════════════════════════════════
   FLOWER PETALS CELEBRATION
   ══════════════════════════════════════════════════════════════════════════ */
.petal-layer {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 9999;
  overflow: hidden;
}

:deep(.petal) {
  position: absolute;
  top: -24px;
  width: 14px;
  height: 20px;
  background: radial-gradient(circle at 35% 35%, #fff1a8, #f59e0b 75%, #d97706);
  border-radius: 50% 50% 50% 0;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  animation: fallPetal linear forwards;
}

@keyframes fallPetal {
  0% {
    opacity: 0.95;
    transform: translateY(0) translateX(0) rotate(0deg);
  }
  100% {
    opacity: 0;
    transform: translateY(105vh) translateX(var(--drift, 50px)) rotate(720deg);
  }
}

/* ══════════════════════════════════════════════════════════════════════════
   RESPONSIVE DESIGN (MOBILE-FIRST TUNING)
   ══════════════════════════════════════════════════════════════════════════ */
@media (max-width: 700px) {
  .hero-inner {
    width: calc(100vw - 24px);
    padding: 38px 14px 34px;
    border-radius: 26px;
    background: rgba(255, 249, 237, 0.44);
    border: 1px solid rgba(220, 180, 105, 0.65);
    box-shadow: 0 16px 50px rgba(39, 44, 60, 0.16);
    backdrop-filter: blur(2.5px);
    -webkit-backdrop-filter: blur(2.5px);
  }

  .eyebrow {
    font-size: 11px;
    letter-spacing: 4px;
  }

  .hero-copy {
    font-size: clamp(20px, 5.5vw, 28px);
    margin: 16px auto 12px;
  }

  .names {
    font-size: clamp(64px, 18vw, 98px);
    line-height: 0.88;
    margin: 8px 0;
  }

  .names .weds {
    font-size: 0.24em;
    letter-spacing: 4px;
    margin: 6px 0 4px;
  }

  .parents {
    font-size: 16px;
    line-height: 1.3;
  }

  .date-section,
  .timeline,
  .countdown,
  .guest {
    padding: 62px 14px;
  }

  .scratch-box {
    height: 185px;
    width: min(360px, 86vw);
    border-radius: 22px;
  }

  .scratch-day {
    font-size: 23px;
    letter-spacing: 2px;
  }

  .scratch-date {
    font-size: 32px;
    letter-spacing: 1px;
    line-height: 1.05;
  }

  .timeline {
    padding: 62px 12px;
  }

  .timeline-inner {
    width: 100%;
  }

  .timeline-photo {
    height: 150px;
    width: min(350px, 84vw);
    border-width: 8px;
  }

  .timeline-card {
    padding: 26px 12px 20px;
  }

  .timeline-event h3 {
    font-size: 44px;
  }

  .timeline-meta {
    font-size: 13px;
    gap: 8px;
  }

  .timeline-location {
    font-size: 14px;
  }

  .count-panel {
    padding: 38px 16px;
    border-radius: 24px;
  }

  .count-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 9px;
  }

  .count-card {
    padding: 16px 6px;
  }

  .count-num {
    font-size: clamp(32px, 9vw, 44px);
  }

  .guest-card {
    padding: 38px 16px;
    border-radius: 24px;
  }

  .guest-message {
    font-size: 21px;
    line-height: 1.5;
  }

  .signature-names {
    font-size: 40px;
  }
}

/* ══════════════════════════════════════════════════════════════════════════
   TAMIL LANGUAGE TYPOGRAPHY OVERRIDES
   Reduces font sizes & font weights across all sections so Tamil text renders
   proportionately and cleanly without looking oversized or overly heavy.
   ══════════════════════════════════════════════════════════════════════════ */
.original-template-root.lang-tamil {
  font-family: 'Noto Sans Tamil', 'Montserrat', system-ui, sans-serif;
}

/* Door Entrance Section */
.original-template-root.lang-tamil .entry-kicker {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(16px, 3.2vw, 22px);
  font-weight: 500;
  letter-spacing: 1px;
}

.original-template-root.lang-tamil .entry-kicker-sub {
  font-size: clamp(15px, 2.8vw, 20px);
  font-weight: 500;
  letter-spacing: 0.5px;
}

.original-template-root.lang-tamil .entry-title {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(34px, 6.5vw, 54px);
  font-weight: 600;
  line-height: 1.25;
  letter-spacing: 0.5px;
}

.original-template-root.lang-tamil .open-btn {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 1px;
  padding: 13px 32px;
}

.original-template-root.lang-tamil .entry-hint {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 11px;
  font-weight: 400;
  letter-spacing: 0.5px;
}

/* Home / Hero Section */
.original-template-root.lang-tamil .hero-quote-banner .quote-text {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(12px, 2vw, 14px);
  font-weight: 500;
  line-height: 1.45;
}

.original-template-root.lang-tamil .eyebrow {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 11px;
  letter-spacing: 1.5px;
  font-weight: 600;
}

.original-template-root.lang-tamil .hero-copy {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(16px, 2.6vw, 22px);
  line-height: 1.45;
  font-weight: 500;
  max-width: 620px;
}

.original-template-root.lang-tamil .names {
  line-height: 1.15;
  margin: 6px 0;
}

.original-template-root.lang-tamil .names .name-kiruba,
.original-template-root.lang-tamil .names .name-bhavya {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(34px, 6.2vw, 52px);
  font-weight: 600;
  line-height: 1.18;
  letter-spacing: 0.5px;
}

.original-template-root.lang-tamil .names .weds {
  font-family: 'Noto Serif Tamil', serif;
  font-size: 0.32em;
  font-weight: 600;
  letter-spacing: 2px;
  margin: 8px 0 6px;
}

.original-template-root.lang-tamil .parents {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: clamp(13px, 2.1vw, 16px);
  font-weight: 500;
  line-height: 1.4;
  letter-spacing: 0.2px;
  margin: 6px auto 2px;
}

.original-template-root.lang-tamil .relation-text {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: clamp(12px, 1.9vw, 14px);
  font-weight: 500;
  line-height: 1.4;
  letter-spacing: 0.2px;
  color: #7d5930;
  margin: 2px auto 6px;
}

.original-template-root.lang-tamil .hero-date {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 12px;
  letter-spacing: 2px;
  font-weight: 600;
}

.original-template-root.lang-tamil .discover {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 10px;
  letter-spacing: 1px;
  font-weight: 500;
}

/* Scratch Card Date Section */
.original-template-root.lang-tamil .section-title {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(24px, 4vw, 36px);
  font-weight: 600;
}

.original-template-root.lang-tamil .sub {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: clamp(13px, 2.2vw, 17px);
  font-weight: 400;
  font-style: normal;
}

.original-template-root.lang-tamil .scratch-day {
  font-family: 'Noto Serif Tamil', serif;
  font-size: 19px;
  letter-spacing: 1px;
  font-weight: 600;
}

.original-template-root.lang-tamil .scratch-tip {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 10px;
  letter-spacing: 1px;
}

/* Timeline & Engagement Section */
.original-template-root.lang-tamil .timeline-title {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(24px, 4.2vw, 36px);
  letter-spacing: 1.5px;
  font-weight: 600;
}

.original-template-root.lang-tamil .timeline-card h3 {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(26px, 4.5vw, 38px);
  font-weight: 600;
  margin-bottom: 6px;
}

.original-template-root.lang-tamil .timeline-meta {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 13px;
  font-weight: 500;
}

.original-template-root.lang-tamil .timeline-time {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 13px;
  font-weight: 600;
}

.original-template-root.lang-tamil .timeline-location {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 14px;
  font-weight: 400;
  line-height: 1.55;
}

.original-template-root.lang-tamil .timeline-btn {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 11px;
  font-weight: 600;
}

/* Countdown Section */
.original-template-root.lang-tamil .count-panel .eyebrow {
  letter-spacing: 1px;
}

.original-template-root.lang-tamil .count-title {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(24px, 4.2vw, 36px);
  font-weight: 600;
}

.original-template-root.lang-tamil .count-sub {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 14px;
  font-weight: 400;
}

.original-template-root.lang-tamil .count-label {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 11px;
  font-weight: 500;
}

.original-template-root.lang-tamil .count-date {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 12px;
  letter-spacing: 1.5px;
  font-weight: 600;
}

/* Guest Note Section */
.original-template-root.lang-tamil .guest-kicker {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 10px;
  letter-spacing: 1px;
  font-weight: 600;
}

.original-template-root.lang-tamil .guest-title {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(26px, 4.5vw, 38px);
  font-weight: 600;
}

.original-template-root.lang-tamil .guest-sub {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 14px;
  font-weight: 400;
}

.original-template-root.lang-tamil .guest-message {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(15px, 2.4vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  max-width: 640px;
}

.original-template-root.lang-tamil .signature-tag {
  font-family: 'Noto Sans Tamil', sans-serif;
  font-size: 15px;
  font-weight: 500;
}

.original-template-root.lang-tamil .signature-names {
  font-family: 'Noto Serif Tamil', serif;
  font-size: clamp(24px, 4vw, 34px);
  font-weight: 600;
  margin-top: 6px;
}

/* Mobile Tweaks for Tamil */
@media (max-width: 700px) {
  .hero {
    padding-top: 60px;
  }

  .hero-quote-banner {
    top: 10px;
    padding: 5px 12px;
    width: calc(100% - 24px);
    max-width: 380px;
    border-radius: 999px;
    gap: 6px;
  }

  .original-template-root.lang-tamil .hero-quote-banner .quote-text {
    font-size: 11px;
    line-height: 1.35;
  }

  .original-template-root.lang-tamil .hero-copy {
    font-size: clamp(15px, 4.4vw, 19px);
    line-height: 1.45;
  }

  .original-template-root.lang-tamil .names .name-kiruba,
  .original-template-root.lang-tamil .names .name-bhavya {
    font-size: clamp(30px, 8.5vw, 42px);
  }

  .original-template-root.lang-tamil .parents {
    font-size: 13px;
    line-height: 1.4;
  }

  .original-template-root.lang-tamil .relation-text {
    font-size: 12px;
    line-height: 1.4;
  }

  .original-template-root.lang-tamil .timeline-card h3 {
    font-size: 26px;
  }

  .original-template-root.lang-tamil .guest-message {
    font-size: 15px;
    line-height: 1.7;
  }

  .original-template-root.lang-tamil .signature-names {
    font-size: 24px;
  }
}
</style>
