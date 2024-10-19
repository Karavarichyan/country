<template>
  <button
    v-if="showScrollButton"
    @click="scrollToTop"
    class="fixed bottom-4 right-4 w-12 h-12 flex items-center justify-center bg-blue-500 text-white rounded-full shadow hover:bg-blue-600 z-50"
  >
    ↑
  </button>
</template>
<script lang="ts" setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'

const showScrollButton = ref(false)

const handleScroll = () => {
  showScrollButton.value = window.scrollY > 200
}

const scrollToTop = () => {
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})

onBeforeUnmount(() => {
  window.removeEventListener('scroll', handleScroll)
})
</script>
