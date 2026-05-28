<template>
  <div class="login-background">
    <div class="background-image">
      <img
        src="@/assets/img/homepage/background.png"
        alt="Background"
        class="w-full h-full object-cover"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

// 检测深色模式
const isDarkMode = computed(() => {
  return document.documentElement.getAttribute('theme-mode') === 'dark'
})
</script>

<style scoped lang="css">
.login-background {
  position: absolute;
  inset: 0;
  z-index: 0;
}

.background-image {
  width: 100%;
  height: 100%;
}

/* 深色模式适配 */
@media (prefers-color-scheme: dark) {
  .login-background {
    filter: brightness(0.8);
  }
}

html[theme-mode="dark"] .login-background {
  filter: brightness(0.7);
}
</style>