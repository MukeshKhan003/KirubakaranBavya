<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import VideoThemeInvitation from './components/VideoThemeInvitation.vue'
import EngagementInvitation from './components/EngagementInvitation.vue'

const currentPath = ref(typeof window !== 'undefined' ? window.location.pathname : '/')

const updatePath = () => {
  if (typeof window !== 'undefined') {
    currentPath.value = window.location.pathname
  }
}

onMounted(() => {
  updatePath()
  window.addEventListener('popstate', updatePath)
})

onUnmounted(() => {
  if (typeof window !== 'undefined') {
    window.removeEventListener('popstate', updatePath)
  }
})

// Check if user is visiting /mukesh/demo-invitation
const isDemoRoute = computed(() => {
  const p = currentPath.value.toLowerCase().replace(/\/+$/, '')
  return p.includes('/mukesh/demo-invitation')
})
</script>

<template>
  <div id="app-root-view">
    <!-- Existing Invitation Template: Rendered when URL is /mukesh/demo-invitation -->
    <EngagementInvitation v-if="isDemoRoute" />

    <!-- New Video-Themed Template: Rendered by default on / -->
    <VideoThemeInvitation v-else />
  </div>
</template>

<style>
/* App level styles */
#app {
  width: 100%;
  min-height: 100vh;
}
#app-root-view {
  width: 100%;
  min-height: 100vh;
}
</style>
