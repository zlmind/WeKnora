<template>
  <div class="floating-images-container">
    <div class="floating-image float-animation-slow" style="left: 0; top: 4rem;">
      <img
        src="@/assets/img/homepage/float-1.png"
        alt="Feature 1"
        class="h-32 w-auto"
      />
    </div>
    <div class="floating-image float-animation-medium" style="left: 35%; top: 0;">
      <img
        src="@/assets/img/homepage/float-2.png"
        alt="Feature 2"
        class="h-36 w-auto"
      />
    </div>
    <div class="floating-image float-animation-fast" style="left: 70%; top: 3rem;">
      <img
        src="@/assets/img/homepage/float-3.png"
        alt="Feature 3"
        class="h-28 w-auto"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
// 漂浮动画图片组件，使用 Tailwind 动画
</script>

<style scoped lang="css">
.floating-images-container {
  position: relative;
  margin-top: 3rem;
  height: 12rem;
}

.floating-image {
  position: absolute;
}

@keyframes float {
  0%, 100% { transform: translateY(0px) translateX(0px); }
  50% { transform: translateY(-20px) translateX(10px); }
}

@keyframes float-slow {
  0%, 100% { transform: translateY(0px) translateX(0px); }
  50% { transform: translateY(-15px) translateX(-10px); }
}

@keyframes float-delay {
  0%, 100% { transform: translateY(0px) translateX(0px); }
  50% { transform: translateY(-25px) translateX(5px); }
}

@keyframes float-medium {
  0%, 100% { transform: translateY(0px) translateX(0px); }
  50% { transform: translateY(-18px) translateX(8px); }
}

@keyframes float-fast {
  0%, 100% { transform: translateY(0px) translateX(0px); }
  50% { transform: translateY(-22px) translateX(-5px); }
}

.float-animation {
  animation: float 6s ease-in-out infinite;
}

.float-animation-slow {
  animation: float-slow 8s ease-in-out infinite;
}

.float-animation-delay {
  animation: float-delay 10s ease-in-out infinite;
}

.float-animation-medium {
  animation: float-medium 6s ease-in-out infinite;
}

.float-animation-fast {
  animation: float-fast 4s ease-in-out infinite;
}

/* 支持减少动画偏好 */
@media (prefers-reduced-motion: reduce) {
  .floating-image {
    animation: none;
  }
}
</style>