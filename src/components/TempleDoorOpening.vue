<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  currentLang: {
    type: String,
    default: 'tm'
  }
})

const emit = defineEmits(['unlock', 'opened'])

// Animation lifecycle states: 'closed' | 'unlocking' | 'opening' | 'opened'
const doorState = ref('closed')
const isOverlayVisible = ref(true)

const unlockDoor = () => {
  if (doorState.value !== 'closed') return

  // 1. Trigger unlock sequence
  doorState.value = 'unlocking'
  emit('unlock')

  // 2. After lock unlatches, swing doors open smoothly
  setTimeout(() => {
    doorState.value = 'opening'
  }, 400)

  // 3. Once doors fully swing open, fade out overlay and complete transition
  setTimeout(() => {
    doorState.value = 'opened'
    emit('opened')
    setTimeout(() => {
      isOverlayVisible.value = false
      if (typeof document !== 'undefined') {
        document.body.style.overflow = ''
      }
    }, 600)
  }, 2200)
}

onMounted(() => {
  if (typeof document !== 'undefined') {
    // Keep scroll locked while temple doors are closed
    document.body.style.overflow = 'hidden'
  }
})

onUnmounted(() => {
  if (typeof document !== 'undefined') {
    document.body.style.overflow = ''
  }
})
</script>

<template>
  <div 
    v-if="isOverlayVisible" 
    class="temple-doors-stage"
    :class="{
      'state-unlocking': doorState === 'unlocking',
      'state-opening': doorState === 'opening',
      'state-opened': doorState === 'opened'
    }"
  >
    <!-- Divine Inner Celestial Glow revealed behind the opening doors -->
    <div class="divine-inner-sanctum-glow">
      <div class="sanctum-light-burst"></div>
      <div class="sanctum-deity-silhouette">
        <span class="sanctum-om">🕉️</span>
      </div>
    </div>

    <!-- Sacred Top Toran / Arch Frame -->
    <header class="door-frame-toran">
      <div class="toran-garland-strip">
        <span class="toran-deco hidden lg:inline">🌿</span>
        <span class="toran-deco hidden md:inline">🌼</span>
        <span class="toran-bell hidden sm:inline">🔔</span>
        <div class="toran-center-crest">
          <div class="crest-body-wrap flex items-center justify-center gap-2">
            <span class="crest-om">🕉️</span>
            <p class="crest-title">நமசிவாய எனும் மங்கள நாதத்துடன்… சிவனும் சக்தியும் சாட்சியாக, இரு உள்ளங்கள் இணையும் திருநாள்…</p>
          </div>
        </div>
        <span class="toran-bell hidden sm:inline">🔔</span>
        <span class="toran-deco hidden md:inline">🌼</span>
        <span class="toran-deco hidden lg:inline">🌿</span>
      </div>
    </header>

    <!-- Side Temple Pillars (Frame Edges) -->
    <div class="door-frame-pillar pillar-left-frame"></div>
    <div class="door-frame-pillar pillar-right-frame"></div>

    <!-- 3D Temple Doors Container -->
    <div class="doors-perspective-viewport">

      <!-- LEFT DOOR LEAF -->
      <div class="temple-door-leaf leaf-left">
        <div class="door-wood-grain"></div>
        <div class="door-inner-border">
          <!-- Traditional Carved Panels Grid -->
          <div class="door-panels-grid">
            <div class="panel-box" v-for="i in 6" :key="'l-' + i">
              <div class="brass-lotus-medallion">
                <span class="medallion-core">✦</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Left Ornate Brass Ring Handle (Kada) -->
        <div class="door-handle-kada handle-left">
          <div class="kada-lion-mount"></div>
          <div class="kada-brass-ring"></div>
        </div>

        <!-- Meeting Seam Overlap Strip -->
        <div class="door-astragal-strip"></div>
      </div>

      <!-- RIGHT DOOR LEAF -->
      <div class="temple-door-leaf leaf-right">
        <div class="door-wood-grain"></div>
        <div class="door-inner-border">
          <!-- Traditional Carved Panels Grid -->
          <div class="door-panels-grid">
            <div class="panel-box" v-for="i in 6" :key="'r-' + i">
              <div class="brass-lotus-medallion">
                <span class="medallion-core">✦</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Right Ornate Brass Ring Handle (Kada) -->
        <div class="door-handle-kada handle-right">
          <div class="kada-lion-mount"></div>
          <div class="kada-brass-ring"></div>
        </div>
      </div>

    </div>

    <!-- ══════════════════════════════════════════════════════════════════
         CENTRAL SACRED BRASS PADLOCK & LATCH MECHANISM
         ══════════════════════════════════════════════════════════════════ -->
    <div 
      class="central-lock-mechanism"
      :class="{ 'lock-unlocked': doorState !== 'closed' }"
      @click="unlockDoor"
      role="button"
      tabindex="0"
      aria-label="Tap to Unlock"
    >
      <!-- Heavy Antique Brass Latch Plates (Hasp) -->
      <div class="brass-latch-bar left-latch"></div>
      <div class="brass-latch-bar right-latch"></div>

      <!-- Lock Aura & Radiance Rings -->
      <div class="lock-radiance-aura"></div>
      <div class="lock-pulse-ring"></div>

      <!-- Heavy Brass Temple Padlock SVG Artwork -->
      <div class="temple-padlock-wrapper">
        <svg class="temple-padlock-svg" viewBox="0 0 160 210" fill="none" xmlns="http://www.w3.org/2000/svg">
          <defs>
            <!-- Gold Metallic Gradients -->
            <linearGradient id="brassGold" x1="0%" y1="0%" x2="100%" y2="100%">
              <stop offset="0%" stop-color="#fffbeb" />
              <stop offset="25%" stop-color="#fbbf24" />
              <stop offset="50%" stop-color="#d97706" />
              <stop offset="75%" stop-color="#92400e" />
              <stop offset="100%" stop-color="#fbbf24" />
            </linearGradient>

            <linearGradient id="shackleShine" x1="0%" y1="0%" x2="100%" y2="0%">
              <stop offset="0%" stop-color="#d97706" />
              <stop offset="30%" stop-color="#fffbeb" />
              <stop offset="70%" stop-color="#f59e0b" />
              <stop offset="100%" stop-color="#78350f" />
            </linearGradient>

            <filter id="goldGlow" x="-20%" y="-20%" width="140%" height="140%">
              <feGaussianBlur stdDeviation="6" result="blur" />
              <feComposite in="SourceGraphic" in2="blur" operator="over" />
            </filter>
          </defs>

          <!-- Hinged Curved Shackle (U-Bar) -->
          <g class="padlock-shackle-group">
            <path 
              d="M48 95 V50 C48 28 62 14 80 14 C98 14 112 28 112 50 V95" 
              stroke="url(#shackleShine)" 
              stroke-width="16" 
              stroke-linecap="round"
              fill="none" 
            />
            <!-- Shackle Collar Rings -->
            <rect x="40" y="86" width="16" height="12" rx="3" fill="#78350f" stroke="#fbbf24" stroke-width="2"/>
            <rect x="104" y="86" width="16" height="12" rx="3" fill="#78350f" stroke="#fbbf24" stroke-width="2"/>
          </g>

          <!-- Heavy Ornate Padlock Body -->
          <g class="padlock-body-group" filter="url(#goldGlow)">
            <!-- Outer Shielded Body -->
            <rect x="25" y="90" width="110" height="105" rx="20" fill="url(#brassGold)" stroke="#451a03" stroke-width="3" />
            
            <!-- Inset Filigree Border -->
            <rect x="33" y="98" width="94" height="89" rx="14" fill="#2d0a0a" stroke="#fbbf24" stroke-width="2" />
            
            <!-- Inner Medallion Center -->
            <circle cx="80" cy="138" r="28" fill="url(#brassGold)" stroke="#78350f" stroke-width="2" />
            <circle cx="80" cy="138" r="23" fill="#1e0505" />

            <!-- Sacred Om & Diya Engraving -->
            <text x="80" y="146" font-size="20" text-anchor="middle" fill="#fbbf24" font-weight="bold">🕉️</text>

            <!-- Keyhole Slot -->
            <path d="M77 168 L83 168 L81 178 L79 178 Z" fill="#000000" />
            <circle cx="80" cy="168" r="3.5" fill="#000000" />

            <!-- Corner Floral Studs on Lock -->
            <circle cx="38" cy="103" r="3" fill="#fbbf24" />
            <circle cx="122" cy="103" r="3" fill="#fbbf24" />
            <circle cx="38" cy="182" r="3" fill="#fbbf24" />
            <circle cx="122" cy="182" r="3" fill="#fbbf24" />
          </g>
        </svg>
      </div>

      <!-- "Tap the Lock to Enter" Prompt Badge -->
      <div class="unlock-action-pill">
        <span class="pill-sparkle">🪔</span>
        <span class="pill-text">
          <template v-if="doorState === 'closed'">
            Tap to Unlock
          </template>
          <template v-else>
            {{ currentLang === 'tm' ? 'திருக்கதவு திறக்கிறது...' : 'Opening Temple Gates...' }}
          </template>
        </span>
        <span class="pill-sparkle">🪔</span>
      </div>

    </div>

    <!-- Sacred Bottom Threshold / Step -->
    <div class="door-bottom-threshold">
      <div class="threshold-rangoli">
        <span class="kolam-ornament">❖ ❖ ❖ 🪔 ❖ ❖ ❖</span>
      </div>
    </div>

  </div>
</template>

<style scoped>
/* ══════════════════════════════════════════════════════════════════════════
   TEMPLE DOORS STAGE & FULLSCREEN PERSPECTIVE
   ══════════════════════════════════════════════════════════════════════════ */
.temple-doors-stage {
  position: fixed;
  inset: 0;
  width: 100vw;
  height: 100vh;
  z-index: 999999;
  background: #0f0303;
  overflow: hidden;
  perspective: 1600px;
  user-select: none;
  -webkit-user-select: none;
  transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1), transform 0.8s ease;
}

.temple-doors-stage.state-opened {
  opacity: 0;
  pointer-events: none;
  transform: scale(1.04);
}

/* Divine Golden Light Burst behind parting doors */
.divine-inner-sanctum-glow {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: radial-gradient(circle at center, rgba(251, 191, 36, 0.55) 0%, rgba(217, 119, 6, 0.25) 40%, rgba(15, 3, 3, 0.95) 75%);
  z-index: 1;
  pointer-events: none;
}

.sanctum-light-burst {
  width: 100vw;
  height: 100vh;
  background: radial-gradient(circle at center, rgba(255, 255, 255, 0.75) 0%, rgba(251, 191, 36, 0.5) 25%, transparent 65%);
  opacity: 0.15;
  transition: opacity 1.6s ease, transform 1.8s ease;
  transform: scale(0.85);
}

.state-opening .sanctum-light-burst,
.state-opened .sanctum-light-burst {
  opacity: 0.95;
  transform: scale(1.3);
}

.sanctum-deity-silhouette {
  position: absolute;
  font-size: clamp(4rem, 15vw, 9rem);
  opacity: 0.25;
  filter: drop-shadow(0 0 35px #fbbf24);
  animation: pulseOm 3s infinite ease-in-out;
}

@keyframes pulseOm {
  0%, 100% { transform: scale(1); opacity: 0.25; }
  50% { transform: scale(1.08); opacity: 0.45; }
}

/* ══════════════════════════════════════════════════════════════════════════
   TOP TORAN / SACRED ARCH
   ══════════════════════════════════════════════════════════════════════════ */
.door-frame-toran {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 60px;
  background: linear-gradient(180deg, rgba(20, 4, 4, 0.95) 0%, rgba(45, 10, 10, 0.85) 80%, transparent 100%);
  border-bottom: 2px solid rgba(251, 191, 36, 0.4);
  z-index: 50;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 5px 25px rgba(0, 0, 0, 0.8);
}

.toran-garland-strip {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: clamp(0.25rem, 1.8vw, 0.75rem);
  font-size: 1.15rem;
}

.toran-center-crest {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 0.35rem 1.25rem;
  background: rgba(30, 6, 6, 0.92);
  border: 1.5px solid #fbbf24;
  border-radius: 999px;
  box-shadow: 0 0 15px rgba(251, 191, 36, 0.35);
  max-width: min(92vw, 760px);
  text-align: center;
}

.crest-body-wrap {
  width: 100%;
}

.crest-title {
  font-family: 'Noto Serif Tamil', 'Outfit', Georgia, serif;
  font-size: clamp(0.68rem, 1.5vw, 0.82rem);
  font-weight: 600;
  color: #fbbf24;
  letter-spacing: 0.02em;
  text-shadow: 0 0 10px rgba(251, 191, 36, 0.5);
  line-height: 1.4;
  margin: 0;
}

/* Side Stone / Gold Pillars */
.door-frame-pillar {
  position: absolute;
  top: 0;
  bottom: 0;
  width: clamp(14px, 3.5vw, 28px);
  background: linear-gradient(90deg, #120303 0%, #3b0f0f 50%, #150404 100%);
  border-right: 1.5px solid rgba(251, 191, 36, 0.35);
  border-left: 1.5px solid rgba(251, 191, 36, 0.35);
  box-shadow: inset 0 0 12px rgba(0, 0, 0, 0.9), 0 0 15px rgba(251, 191, 36, 0.2);
  z-index: 45;
}

.pillar-left-frame { left: 0; }
.pillar-right-frame { right: 0; }

/* ══════════════════════════════════════════════════════════════════════════
   3D TEMPLE DOORS VIEWPORT & LEAVES
   ══════════════════════════════════════════════════════════════════════════ */
.doors-perspective-viewport {
  position: absolute;
  inset: 0;
  display: flex;
  transform-style: preserve-3d;
  z-index: 10;
}

.temple-door-leaf {
  position: relative;
  width: 50%;
  height: 100%;
  background: 
    radial-gradient(ellipse at center, rgba(62, 16, 16, 0.75) 0%, rgba(20, 5, 5, 0.95) 100%),
    repeating-linear-gradient(90deg, #2b0b0b 0px, #1a0505 4px, #260808 8px, #360f0f 12px);
  border-top: 3px solid #fbbf24;
  border-bottom: 4px solid #b45309;
  box-shadow: 
    inset 0 0 60px rgba(0, 0, 0, 0.95), 
    inset 0 0 25px rgba(251, 191, 36, 0.15),
    0 10px 40px rgba(0, 0, 0, 0.8);
  transition: transform 1.8s cubic-bezier(0.22, 1, 0.36, 1);
  will-change: transform;
  overflow: hidden;
}

.leaf-left {
  transform-origin: left center;
  border-right: 2px solid rgba(251, 191, 36, 0.35);
}

.leaf-right {
  transform-origin: right center;
  border-left: 2px solid rgba(251, 191, 36, 0.35);
}

/* Door Swing Key Animations */
.state-opening .leaf-left,
.state-opened .leaf-left {
  transform: rotateY(-105deg) scale(0.98);
}

.state-opening .leaf-right,
.state-opened .leaf-right {
  transform: rotateY(105deg) scale(0.98);
}

.door-inner-border {
  position: absolute;
  inset: clamp(14px, 3.5vw, 28px);
  border: 2px solid rgba(251, 191, 36, 0.38);
  background: rgba(18, 4, 4, 0.45);
  box-shadow: inset 0 0 25px rgba(0, 0, 0, 0.85);
  display: flex;
  padding: clamp(8px, 1.8vw, 16px);
}

/* Carved Panels Grid on Each Door */
.door-panels-grid {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-rows: repeat(6, 1fr);
  gap: clamp(8px, 1.6vw, 16px);
}

.panel-box {
  background: linear-gradient(145deg, #1b0505 0%, #300a0a 60%, #150303 100%);
  border: 1.5px solid rgba(251, 191, 36, 0.3);
  border-radius: 8px;
  box-shadow: 
    inset 0 3px 10px rgba(0, 0, 0, 0.8), 
    inset 0 -2px 6px rgba(251, 191, 36, 0.12),
    0 2px 8px rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.brass-lotus-medallion {
  width: clamp(26px, 5.5vw, 46px);
  height: clamp(26px, 5.5vw, 46px);
  border-radius: 50%;
  background: radial-gradient(circle at 35% 35%, #fffbeb 0%, #fbbf24 40%, #b45309 85%, #78350f 100%);
  box-shadow: 
    0 0 10px rgba(251, 191, 36, 0.4), 
    inset 0 1px 3px rgba(255, 255, 255, 0.8),
    0 4px 8px rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid #78350f;
}

.medallion-core {
  font-size: clamp(0.7rem, 1.8vw, 1.1rem);
  color: #451a03;
  font-weight: 900;
}

/* Ornate Kada Door Handles */
.door-handle-kada {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  z-index: 20;
}

.handle-left { right: clamp(20px, 4.5vw, 45px); }
.handle-right { left: clamp(20px, 4.5vw, 45px); }

.kada-lion-mount {
  width: clamp(26px, 4.8vw, 42px);
  height: clamp(26px, 4.8vw, 42px);
  background: radial-gradient(circle, #fde68a 0%, #d97706 60%, #78350f 100%);
  border-radius: 50%;
  border: 2px solid #fbbf24;
  box-shadow: 0 0 15px rgba(251, 191, 36, 0.45);
}

.kada-brass-ring {
  width: clamp(34px, 6.2vw, 56px);
  height: clamp(48px, 9vw, 80px);
  border: clamp(5px, 1.1vw, 8px) solid #fbbf24;
  border-radius: 50%;
  margin-top: -8px;
  box-shadow: 
    0 8px 18px rgba(0, 0, 0, 0.8), 
    inset 0 0 10px rgba(0, 0, 0, 0.7);
  background: transparent;
}

/* Center Astragal Overlap Strip */
.door-astragal-strip {
  position: absolute;
  top: 0;
  right: -8px;
  width: 16px;
  height: 100%;
  background: linear-gradient(90deg, #92400e 0%, #fbbf24 50%, #78350f 100%);
  border-left: 1px solid rgba(255, 255, 255, 0.4);
  border-right: 1px solid rgba(0, 0, 0, 0.8);
  box-shadow: 0 0 12px rgba(0, 0, 0, 0.75);
  z-index: 15;
}

/* ══════════════════════════════════════════════════════════════════════════
   CENTRAL SACRED BRASS PADLOCK & LATCH
   ══════════════════════════════════════════════════════════════════════════ */
.central-lock-mechanism {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 100;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  outline: none;
  transition: transform 0.4s ease, opacity 0.5s ease;
}

.central-lock-mechanism:hover {
  transform: translate(-50%, -50%) scale(1.05);
}

.central-lock-mechanism:active {
  transform: translate(-50%, -50%) scale(0.98);
}

/* Horizontal Brass Latch Plates */
.brass-latch-bar {
  position: absolute;
  top: 48%;
  height: 20px;
  background: linear-gradient(180deg, #fef08a 0%, #d97706 50%, #78350f 100%);
  border: 1.5px solid #451a03;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.75);
  z-index: -1;
}

.left-latch {
  right: 50%;
  width: clamp(70px, 14vw, 130px);
  border-radius: 6px 0 0 6px;
}

.right-latch {
  left: 50%;
  width: clamp(70px, 14vw, 130px);
  border-radius: 0 6px 6px 0;
}

/* Glowing Pulsing Ring Around Lock */
.lock-radiance-aura {
  position: absolute;
  width: clamp(140px, 28vw, 240px);
  height: clamp(140px, 28vw, 240px);
  border-radius: 50%;
  background: radial-gradient(circle, rgba(251, 191, 36, 0.45) 0%, rgba(217, 119, 6, 0.15) 50%, transparent 70%);
  animation: pulseAuraLock 2.4s infinite ease-in-out;
  pointer-events: none;
}

.lock-pulse-ring {
  position: absolute;
  width: clamp(110px, 22vw, 180px);
  height: clamp(110px, 22vw, 180px);
  border-radius: 50%;
  border: 2px solid rgba(251, 191, 36, 0.6);
  animation: rippleRingLock 2.4s infinite linear;
  pointer-events: none;
}

@keyframes pulseAuraLock {
  0%, 100% { transform: scale(1); opacity: 0.6; }
  50% { transform: scale(1.15); opacity: 0.95; }
}

@keyframes rippleRingLock {
  0% { transform: scale(0.9); opacity: 0.8; }
  100% { transform: scale(1.5); opacity: 0; }
}

.temple-padlock-wrapper {
  width: clamp(105px, 20vw, 150px);
  filter: drop-shadow(0 15px 30px rgba(0, 0, 0, 0.9)) drop-shadow(0 0 20px rgba(251, 191, 36, 0.4));
  transition: transform 0.4s ease;
}

.padlock-shackle-group {
  transform-origin: 48px 95px;
  transition: transform 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
}

/* Unlock Motion */
.lock-unlocked .padlock-shackle-group {
  transform: translateY(-22px) rotate(16deg);
}

.lock-unlocked.central-lock-mechanism {
  animation: unlockBurst 1.2s forwards cubic-bezier(0.25, 1, 0.5, 1);
}

@keyframes unlockBurst {
  0% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
  30% { transform: translate(-50%, -50%) scale(1.18); filter: brightness(1.5); opacity: 1; }
  100% { transform: translate(-50%, -50%) scale(0.7) translateY(40px); opacity: 0; pointer-events: none; }
}

/* Prompt Badge Below Lock */
.unlock-action-pill {
  margin-top: 1.25rem;
  display: inline-flex;
  align-items: center;
  gap: 0.6rem;
  padding: 0.65rem 1.4rem;
  background: rgba(28, 6, 6, 0.92);
  border: 1.8px solid #fbbf24;
  border-radius: 999px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.7), 0 0 18px rgba(251, 191, 36, 0.35);
  animation: floatPill 2.5s infinite ease-in-out;
  transition: all 0.3s ease;
}

.central-lock-mechanism:hover .unlock-action-pill {
  background: #3b0a0a;
  border-color: #fde68a;
  box-shadow: 0 10px 30px rgba(217, 119, 6, 0.55);
}

.pill-text {
  font-family: 'Cinzel', 'Noto Serif Tamil', Georgia, serif;
  font-size: clamp(0.85rem, 2.4vw, 1.02rem);
  font-weight: 700;
  color: #fef08a;
  letter-spacing: 0.06em;
  text-shadow: 0 0 10px rgba(251, 191, 36, 0.5);
  white-space: nowrap;
}

.pill-sparkle {
  font-size: 1.05rem;
}

@keyframes floatPill {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

/* ══════════════════════════════════════════════════════════════════════════
   BOTTOM SACRED THRESHOLD
   ══════════════════════════════════════════════════════════════════════════ */
.door-bottom-threshold {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 40px;
  background: linear-gradient(0deg, #170404 0%, #2f0a0a 70%, transparent 100%);
  border-top: 2px solid rgba(251, 191, 36, 0.45);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
  box-shadow: 0 -5px 25px rgba(0, 0, 0, 0.85);
}

.threshold-rangoli {
  color: #fbbf24;
  font-size: 0.95rem;
  letter-spacing: 0.3em;
  opacity: 0.85;
  text-shadow: 0 0 8px rgba(251, 191, 36, 0.5);
}

/* ══════════════════════════════════════════════════════════════════════════
   RESPONSIVE ADJUSTMENTS
   ══════════════════════════════════════════════════════════════════════════ */
@media (max-width: 768px) {
  .door-frame-toran {
    height: auto;
    min-height: 48px;
    padding: 0.35rem 0.5rem;
  }

  .toran-center-crest {
    padding: 0.4rem 0.85rem;
    border-radius: 0.85rem;
    max-width: 94vw;
  }

  .crest-title {
    font-size: clamp(0.66rem, 2.7vw, 0.76rem);
    letter-spacing: 0.015em;
    line-height: 1.45;
  }

  .door-inner-border {
    inset: 10px;
    padding: 6px;
  }

  .door-panels-grid {
    gap: 8px;
  }

  .unlock-action-pill {
    padding: 0.5rem 1.1rem;
    margin-top: 0.9rem;
  }

  .pill-text {
    font-size: 0.8rem;
  }
}
</style>
