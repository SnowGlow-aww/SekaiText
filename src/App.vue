<template>
  <router-view />
  <Toast />
  <DownloadFloat />
</template>

<script setup lang="ts">
import { onMounted, watch } from 'vue'
import { useSettingsStore } from './stores/settings'
import { useDebugLog } from './composables/useDebugLog'
import Toast from './components/Toast.vue'
import DownloadFloat from './components/DownloadFloat.vue'

const settings = useSettingsStore()
const { enabled, initConsoleCapture } = useDebugLog()

function applyFontSize(size: number) {
  document.documentElement.style.setProperty('--editor-font-size', size + 'px')
}

function applyDebug(enabled: boolean) {
  if (enabled) initConsoleCapture()
}

watch(() => settings.settings.fontSize, applyFontSize, { immediate: true })
watch(() => settings.settings.debugEnabled, (v) => { enabled.value = v; applyDebug(v) })

onMounted(async () => {
  try {
    await settings.fetchSettings()
  } catch {
    // backend not available, use defaults
  }
  enabled.value = settings.settings.debugEnabled
  applyDebug(settings.settings.debugEnabled)
  applyFontSize(settings.settings.fontSize)
})
</script>
