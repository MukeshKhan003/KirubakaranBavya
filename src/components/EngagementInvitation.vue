<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import TempleDoorOpening from './TempleDoorOpening.vue'

// ══════════════════════════════════════════════════════════════════════════
// 1. LANGUAGE SUPPORT (ENGLISH & TAMIL)
// ══════════════════════════════════════════════════════════════════════════
const currentLang = ref('tm')

// Multilingual Translations Dictionary
const translations = {
  en: {
    bannerText: 'நமசிவாய எனும் மங்கள நாதத்துடன்… சிவனும் சக்தியும் சாட்சியாக, இரு உள்ளங்கள் இணையும் திருநாள்…',
    groomName: 'M. KIRUBAKARAN',
    connector: '❤',
    brideName: 'N. BHAVYA',
    invitationSubtitle: 'Are Inviting You to Their Sacred Engagement',
    dateFormatted: '25.10.2026',
    timeFormatted: '10.30AM - 12:00PM',
    venueFull: 'Sornam Arumugam Marriage Hall, Tittagudi',
    ceremonyConcluded: "🔔 Ceremony Concluded with Lord Shiva & Goddess Parvati's Divine Blessings",
    // Redesigned Details Section
    detailsTitle: '🪔 Sacred Engagement Covenant 🪔',
    detailsDesc: 'Together with our families, we cordially invite you to the auspicious Engagement Ceremony of M. Kirubakaran and N. Bhavya.',
    // Three Segments
    dateLabel: 'Date',
    dateVal: '25.10.2026',
    dateSub: 'Sunday',
    timeLabel: 'Time',
    timeVal: '10.30AM - 12:00PM',
    timeSub: 'Auspicious Muhurtham',
    venueLabel: 'Venue',
    venueVal: 'Tittagudi, Sornam Arumugam Marriage Hall',
    venueSub: 'Cuddalore District, Tamil Nadu',
    openInMapsBtn: 'Open in Maps',
    // Parents Invitation Section
    parentsTitle: 'With Love',
    groomParentsLabel: "Groom's Parents",
    groomParents: 'K.M. Mohan, M. Akila Priya',
    brideParentsLabel: "Bride's Parents",
    brideParents: 'R. Nakeeran, N. Selvarani',
    // Bottom Decorative Quote
    bottomQuote: '“Your Presence will make our day more special”'
  },
  tm: {
    bannerText: 'நமசிவாய எனும் மங்கள நாதத்துடன்… சிவனும் சக்தியும் சாட்சியாக, இரு உள்ளங்கள் இணையும் திருநாள்…',
    groomName: 'M. கிருபாகரன்',
    connector: '❤',
    brideName: 'N. பவ்யா',
    invitationSubtitle: 'தங்களை அன்புடன் எங்களது நிச்சயதார்த்த விழாவிற்கு அழைக்கிறோம்',
    dateFormatted: '25.10.2026',
    timeFormatted: 'காலை 10.30 - நண்பகல் 12.00',
    venueFull: 'சொர்ணம் ஆறுமுகம் திருமண மண்டபம், திட்டக்குடி',
    ceremonyConcluded: '🔔 சிவ பார்வதி திருவருளுடன் நிச்சயதார்த்த விழா இனிதே நிறைவடைந்தது',
    // Redesigned Details Section
    detailsTitle: '🪔 சுப நிச்சயதார்த்த விழா 🪔',
    detailsDesc: 'எங்கள் குடும்பத்தாருடன் இணைந்து, M. கிருபாகரன் மற்றும் N. பவ்யா ஆகியோரின் சுப நிச்சயதார்த்த விழாவிற்கு தங்களை அன்போடு அழைக்கிறோம்.',
    // Three Segments
    dateLabel: 'தேதி',
    dateVal: '25.10.2026',
    dateSub: 'ஞாயிற்றுக்கிழமை',
    timeLabel: 'நேரம்',
    timeVal: 'காலை 10.30 - நண்பகல் 12:00',
    timeSub: 'சுப முகூர்த்தம்',
    venueLabel: 'இடம்',
    venueVal: 'சொர்ணம் ஆறுமுகம் திருமண மண்டபம், திட்டக்குடி',
    venueSub: 'கடலூர் மாவட்டம், தமிழ்நாடு',
    openInMapsBtn: 'வரைபடத்தில் பார்க்க (Maps)',
    // Parents Invitation Section
    parentsTitle: 'அன்புடன்',
    groomParentsLabel: 'மணமகன் பெற்றோர்',
    groomParents: 'K.M. மோகன், M. அகில பிரியா',
    brideParentsLabel: 'மணமகள் பெற்றோர்',
    brideParents: 'R. நக்கீரன், N. செல்வராணி',
    // Bottom Decorative Quote
    bottomQuote: '“தங்களின் வருகை எங்கள் நன்னாளை மேலும் சிறப்பாக்கும்”'
  }
}

// Current active translation accessor
const t = computed(() => translations[currentLang.value] || translations.tm)

// Synchronize Language from URL (/lang-en or /lang-tm)
const syncLangFromUrl = () => {
  if (typeof window === 'undefined') return
  const path = window.location.pathname.toLowerCase()
  const search = window.location.search.toLowerCase()
  const hash = window.location.hash.toLowerCase()
  if (path.includes('lang-en') || search.includes('lang=en') || hash.includes('lang-en')) {
    currentLang.value = 'en'
  } else {
    // Default to Tamil on initial page load
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
// 2. COUNTDOWN TIMER
// ══════════════════════════════════════════════════════════════════════════
const eventDateIso = '2026-10-25T10:30:00'
const countdownText = ref({
  days: '00',
  hours: '00',
  minutes: '00',
  seconds: '00'
})
const countdownExpired = ref(false)
let countdownInterval = null

const startCountdownTimer = (targetDateStr) => {
  if (countdownInterval) clearInterval(countdownInterval)
  
  const updateTimer = () => {
    const target = new Date(targetDateStr).getTime()
    const now = new Date().getTime()
    const diff = target - now

    if (diff <= 0) {
      countdownExpired.value = true
      countdownText.value = { days: '00', hours: '00', minutes: '00', seconds: '00' }
      clearInterval(countdownInterval)
      return
    }

    const days = Math.floor(diff / (1000 * 60 * 60 * 24))
    const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
    const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))
    const seconds = Math.floor((diff % (1000 * 60)) / 1000)

    const pad = (num) => String(num).padStart(2, '0')
    countdownText.value = {
      days: pad(days),
      hours: pad(hours),
      minutes: pad(minutes),
      seconds: pad(seconds)
    }
  }

  updateTimer()
  countdownInterval = setInterval(updateTimer, 1000)
}

const countdownFormatted = computed(() => {
  return `${countdownText.value.days}d ${countdownText.value.hours}h ${countdownText.value.minutes}m ${countdownText.value.seconds}s`
})

// ══════════════════════════════════════════════════════════════════════════
// 3. BACKGROUND MUSIC (AUTOMATIC PLAYBACK & ICON-ONLY FLOATING BUTTON)
// ══════════════════════════════════════════════════════════════════════════
const songUrl = '/audio/wedding_flute.mp3'
const isPlayingMusic = ref(false)
const audioElement = ref(null)

const userEvents = ['click', 'touchstart', 'touchend', 'pointerdown', 'mousedown', 'keydown']

const cleanupListeners = () => {
  userEvents.forEach(evt => {
    document.removeEventListener(evt, handleFirstInteraction, true)
    window.removeEventListener(evt, handleFirstInteraction, true)
  })
}

const handleFirstInteraction = () => {
  if (audioElement.value && !isPlayingMusic.value) {
    audioElement.value.muted = false
    audioElement.value.volume = 0.85
    const p = audioElement.value.play()
    if (p !== undefined) {
      p.then(() => {
        isPlayingMusic.value = true
        cleanupListeners()
      }).catch((err) => {
        console.warn('Waiting for valid user gesture:', err)
      })
    }
  }
}

const initAudioAutoplay = () => {
  if (!audioElement.value) return

  audioElement.value.volume = 0.85
  audioElement.value.muted = false

  // Proactively attach interaction listeners to document & window
  userEvents.forEach(evt => {
    document.addEventListener(evt, handleFirstInteraction, { capture: true, passive: true })
    window.addEventListener(evt, handleFirstInteraction, { capture: true, passive: true })
  })

  // Direct unmuted playback attempt on load / reload
  const playPromise = audioElement.value.play()
  if (playPromise !== undefined) {
    playPromise.then(() => {
      isPlayingMusic.value = true
      cleanupListeners()
    }).catch(() => {
      // Browser policy deferred playback until first gesture; listeners will trigger immediately
    })
  }
}

const toggleMusic = () => {
  if (!audioElement.value) return

  if (isPlayingMusic.value) {
    audioElement.value.pause()
    isPlayingMusic.value = false
  } else {
    audioElement.value.muted = false
    audioElement.value.volume = 0.85
    audioElement.value.play().then(() => {
      isPlayingMusic.value = true
      cleanupListeners()
    }).catch(err => {
      console.warn('Playback error:', err)
      isPlayingMusic.value = false
    })
  }
}

// ══════════════════════════════════════════════════════════════════════════
// 4. EXTERNAL LINKS & LIFECYCLE
// ══════════════════════════════════════════════════════════════════════════
const googleMapsLocationUrl = 'https://maps.app.goo.gl/8r8R7PVDQ1oatWbVA'

// ══════════════════════════════════════════════════════════════════════════
// 5. TEMPLE DOOR ENTRANCE ANIMATION
// ══════════════════════════════════════════════════════════════════════════
const isTempleDoorUnlocked = ref(false)
const isTempleDoorOpened = ref(false)

const handleDoorUnlock = () => {
  isTempleDoorUnlocked.value = true
  if (audioElement.value) {
    audioElement.value.muted = false
    audioElement.value.volume = 0.85
    audioElement.value.play().then(() => {
      isPlayingMusic.value = true
      cleanupListeners()
    }).catch((err) => {
      console.warn('Playback error on door unlock:', err)
    })
  }
}

const handleDoorOpened = () => {
  isTempleDoorOpened.value = true
}

onMounted(() => {
  syncLangFromUrl()
  window.addEventListener('popstate', syncLangFromUrl)
  startCountdownTimer(eventDateIso)
  initAudioAutoplay()
})

onUnmounted(() => {
  if (typeof window !== 'undefined') {
    window.removeEventListener('popstate', syncLangFromUrl)
  }
  cleanupListeners()
  if (countdownInterval) clearInterval(countdownInterval)
  if (audioElement.value) {
    audioElement.value.pause()
  }
})
</script>

<template>
  <div 
    class="wedding-theme-k" 
    :class="{ 
      'lang-tamil': currentLang === 'tm',
      'door-closed-theme': !isTempleDoorOpened 
    }" 
    style="position: relative;"
  >

    <!-- ══════════════════════════════════════════════════════════════════════
         TEMPLE ENTRANCE DOOR OPENING ANIMATION OVERLAY
         ══════════════════════════════════════════════════════════════════════ -->
    <TempleDoorOpening 
      :currentLang="currentLang" 
      @unlock="handleDoorUnlock" 
      @opened="handleDoorOpened" 
    />

    <!-- ══════════════════════════════════════════════════════════════════════
         MAIN WEDDING INVITATION CONTENT (REVEALED ON DOOR OPEN)
         ══════════════════════════════════════════════════════════════════════ -->
    <div 
      class="wedding-invitation-inner-wrap" 
      :class="{ 
        'door-revealed': isTempleDoorUnlocked,
        'door-opened-clear': isTempleDoorOpened 
      }"
    >

    <!-- ══════════════════════════════════════════════════════════════════════
         1. SACRED INVOCATIONS TOP HEADER BAR (PINNED TO TOP)
         ══════════════════════════════════════════════════════════════════════ -->
    <div class="divine-blessings-banner">
      <span class="blessing-symbol">🕉️</span>
      <p class="blessing-text">{{ t.bannerText }}</p>
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         LANGUAGE SWITCHER PILL (DESKTOP ONLY: ENGLISH / தமிழ்)
         Supports /lang-en and /lang-tm
         ══════════════════════════════════════════════════════════════════════ -->
    <div class="lang-switch-container hidden md:block">
      <div class="lang-switch-pill">
        <a 
          href="/lang-en" 
          @click.prevent="setLanguage('en')" 
          class="lang-btn" 
          :class="{ active: currentLang === 'en' }"
          title="View in English"
        >
          English
        </a>
        <span class="lang-sep">•</span>
        <a 
          href="/lang-tm" 
          @click.prevent="setLanguage('tm')" 
          class="lang-btn" 
          :class="{ active: currentLang === 'tm' }"
          title="தமிழில் பார்க்க"
        >
          தமிழ்
        </a>
      </div>
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         2. TRADITIONAL TEMPLE GOPURAM PILLARS (LEFT & RIGHT)
         ══════════════════════════════════════════════════════════════════════ -->
    <div class="temple-animation-gopurams">
      <img src="/images/pillar.jpg" class="gopuram-pillar-img pillar-left" alt="Left Temple Pillar" />
      <img src="/images/pillar.jpg" class="gopuram-pillar-img pillar-right" alt="Right Temple Pillar" />
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         3. SIVAN AND PARVATI DIVINE WATERMARK BACKGROUND
         ══════════════════════════════════════════════════════════════════════ -->
    <div class="divine-murugan-bg divine-sivan-parvati-bg gold-style">
      <img 
        src="/images/sivan_parvati_gold.png" 
        class="murugan-watermark-img sivan-parvati-img" 
        alt="Lord Shiva and Goddess Parvati Divine Blessings" 
      />
    </div>

    <!-- ══════════════════════════════════════════════════════════════════════
         HERO SECTION: HANGING TEMPLE BELL + ANIMATED NAMES & COUNTDOWN
         ══════════════════════════════════════════════════════════════════════ -->
    <header class="hero-section flex flex-col items-center relative overflow-hidden">

      <!-- Hanging Swinging Brass Temple Bell -->
      <div class="hanging-bell-wrapper">
        <img src="/images/bell.jpg" class="temple-brass-bell" alt="Temple Bell" />
      </div>

      <!-- Animated Names Section (Groom & Bride) - Temple Bells Signature -->
      <div class="animated-names-container flex flex-col items-center text-center">
        <h1 class="wedding-names-animated">
          <span class="name-block groom-name">{{ t.groomName }}</span>
          <span class="heart-symbol-block" aria-label="love">
            <svg class="names-heart-svg" viewBox="0 0 24 24" fill="currentColor">
              <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"/>
            </svg>
          </span>
          <span class="name-block bride-name">{{ t.brideName }}</span>
        </h1>
        <p class="animated-subtitle">{{ t.invitationSubtitle }}</p>

        <!-- Auspicious Countdown Timer with Event Date Header -->
        <div v-if="!countdownExpired" class="countdown-widget-animated flex flex-col items-center">
          <div class="countdown-lbl">🪔 {{ t.dateFormatted }} 🪔</div>
          <div class="countdown-val">{{ countdownFormatted }}</div>
        </div>
        <div v-else class="event-ended-badge-animated">
          {{ t.ceremonyConcluded }}
        </div>
      </div>

    </header>

    <!-- ══════════════════════════════════════════════════════════════════════
         REDESIGNED DETAILS SECTION (THREE SEPARATE SEGMENTS: DATE, TIME, VENUE)
         ══════════════════════════════════════════════════════════════════════ -->
    <main class="details-section container">

      <div class="divine-details-card flex flex-col gap-6 text-center">
        
        <h2 class="divine-section-title">{{ t.detailsTitle }}</h2>

        <p class="divine-desc-p">
          {{ t.detailsDesc }}
        </p>

        <!-- Three Separate Segments (DATE | TIME | VENUE) -->
        <div class="segments-triad-grid">

          <!-- Segment 1: Date -->
          <div class="details-segment-card segment-date-card">
            <div class="segment-icon-glow">
              <svg class="segment-icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <rect x="3" y="4" width="18" height="18" rx="2" ry="2" stroke-width="1.8"/>
                <line x1="16" y1="2" x2="16" y2="6" stroke-width="1.8"/>
                <line x1="8" y1="2" x2="8" y2="6" stroke-width="1.8"/>
                <line x1="3" y1="10" x2="21" y2="10" stroke-width="1.8"/>
                <circle cx="8" cy="14" r="1.2" fill="currentColor"/>
                <circle cx="12" cy="14" r="1.2" fill="currentColor"/>
                <circle cx="16" cy="14" r="1.2" fill="currentColor"/>
                <circle cx="8" cy="18" r="1.2" fill="currentColor"/>
                <circle cx="12" cy="18" r="1.2" fill="currentColor"/>
                <circle cx="16" cy="18" r="1.2" fill="currentColor"/>
              </svg>
            </div>
            <span class="segment-tag">{{ t.dateLabel }}</span>
            <div class="segment-main-val">{{ t.dateVal }}</div>
            <div class="segment-sub-val">{{ t.dateSub }}</div>
          </div>

          <!-- Segment 2: Time -->
          <div class="details-segment-card segment-time-card">
            <div class="segment-icon-glow">
              <svg class="segment-icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <circle cx="12" cy="12" r="10" stroke-width="1.8"/>
                <polyline points="12 6 12 12 16 14" stroke-width="1.8"/>
              </svg>
            </div>
            <span class="segment-tag">{{ t.timeLabel }}</span>
            <div class="segment-main-val">{{ t.timeVal }}</div>
            <div class="segment-sub-val">{{ t.timeSub }}</div>
          </div>

          <!-- Segment 3: Venue -->
          <div class="details-segment-card segment-venue-card">
            <div class="segment-icon-glow">
              <svg class="segment-icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7z" stroke-width="1.8"/>
                <circle cx="12" cy="9" r="2.8" stroke-width="1.8"/>
              </svg>
            </div>
            <span class="segment-tag">{{ t.venueLabel }}</span>
            <div class="segment-venue-title">{{ t.venueVal }}</div>
            <div class="segment-sub-val">{{ t.venueSub }}</div>
            
            <!-- Open in Maps Button (Below Venue Details) -->
            <div class="venue-action-wrapper">
              <a 
                :href="googleMapsLocationUrl" 
                target="_blank" 
                rel="noopener" 
                class="btn-maps-action"
              >
                <span class="btn-maps-pin">📍</span>
                <span>{{ t.openInMapsBtn }}</span>
                <span class="btn-maps-arrow">↗</span>
              </a>
            </div>
          </div>

        </div>

        <!-- ══════════════════════════════════════════════════════════════════
             4. PARENTS INVITATION SECTION (அன்புடன்)
             ══════════════════════════════════════════════════════════════════ -->
        <div class="parents-invitation-section">
          <!-- Centered Header: அன்புடன் -->
          <div class="parents-title-wrapper">
            <div class="parents-ornament-line"></div>
            <div class="parents-title-inner">
              <span class="parents-flourish">❦</span>
              <h3 class="parents-section-title">{{ t.parentsTitle }}</h3>
              <span class="parents-flourish">❦</span>
            </div>
            <div class="parents-ornament-line"></div>
          </div>

          <!-- Parents 2-Column Layout: Left (Groom Parents) & Right (Bride Parents) -->
          <div class="parents-grid-layout">
            <!-- Left: Groom's Parents -->
            <div class="parents-col parents-col-groom">
              <span class="parents-role-badge">{{ t.groomParentsLabel }}</span>
              <div class="parents-name-text">{{ t.groomParents }}</div>
            </div>

            <!-- Center Auspicious Diya Divider -->
            <div class="parents-center-diya">
              <span class="diya-glow-icon">🪔</span>
            </div>

            <!-- Right: Bride's Parents -->
            <div class="parents-col parents-col-bride">
              <span class="parents-role-badge">{{ t.brideParentsLabel }}</span>
              <div class="parents-name-text">{{ t.brideParents }}</div>
            </div>
          </div>
        </div>

        <!-- ══════════════════════════════════════════════════════════════════
             5. BOTTOM DECORATIVE QUOTE (GREAT VIBES FONT)
             ══════════════════════════════════════════════════════════════════ -->
        <div class="bottom-presence-quote-container">
          <div class="quote-accent-sprig">❦</div>
          <p class="bottom-presence-quote">{{ t.bottomQuote }}</p>
        </div>

      </div>

    </main>

    <!-- ══════════════════════════════════════════════════════════════════════
         5. FOOTER: MADE BY INVITESEND.COM
         ══════════════════════════════════════════════════════════════════════ -->
    <footer class="wedding-invitation-footer">
      <div class="footer-inner-container">
        <div class="footer-top-ornament">
          <span class="ornament-line"></span>
          <span class="ornament-symbol">🪔</span>
          <span class="ornament-line"></span>
        </div>
        <!-- Language Switcher in Footer (Accessible on all devices including Mobile) -->
        <div class="footer-lang-pill-wrap">
          <button 
            type="button"
            @click="setLanguage('tm')" 
            class="footer-lang-btn" 
            :class="{ active: currentLang === 'tm' }"
            title="தமிழில் பார்க்க"
          >
            தமிழ்
          </button>
          <span class="footer-lang-sep">•</span>
          <button 
            type="button"
            @click="setLanguage('en')" 
            class="footer-lang-btn" 
            :class="{ active: currentLang === 'en' }"
            title="View in English"
          >
            English
          </button>
        </div>

        <a 
          href="https://invitesend.com" 
          target="_blank" 
          rel="noopener noreferrer" 
          class="footer-credits-link"
          title="Made by InviteSend.com"
        >
          <span class="credits-prefix">Made by</span>
          <span class="credits-brand">InviteSend.com</span>
        </a>
      </div>
    </footer>

    <!-- ══════════════════════════════════════════════════════════════════════
         FLOATING AUDIO / MUSIC PLAYER WIDGET (ICON ONLY)
         ══════════════════════════════════════════════════════════════════════ -->
    <div class="global-premium-music-widget">
      <button 
        @click="toggleMusic" 
        class="music-float-btn" 
        :class="{ 'is-playing': isPlayingMusic }"
        :title="isPlayingMusic ? (currentLang === 'tm' ? 'இசையை நிறுத்த' : 'Mute Background Music') : (currentLang === 'tm' ? 'இசையை இயக்க' : 'Play Background Music')"
        aria-label="Toggle Music"
      >
        <span class="music-icon-glow"></span>
        <span class="music-icon">{{ isPlayingMusic ? '🎵' : '🔇' }}</span>
      </button>

      <!-- Background Audio Element -->
      <audio 
        ref="audioElement" 
        :src="songUrl" 
        loop 
        preload="auto" 
        playsinline 
        webkit-playsinline
        style="display: none;"
      ></audio>
    </div>

    </div> <!-- End wedding-invitation-inner-wrap -->

  </div>
</template>
