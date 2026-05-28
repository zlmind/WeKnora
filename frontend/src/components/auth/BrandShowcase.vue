<template>
  <div class="brand-showcase flex lg:flex-row flex-col items-center justify-center px-6 lg:px-16 xl:px-24 py-12 lg:py-0">
    <div class="max-w-2xl w-full">
      <!-- Logo - 移动端显示 -->
      <div class="lg:hidden flex justify-center mb-8">
        <img
          src="@/assets/img/homepage/logo.png"
          alt="WeKnora Logo"
          class="h-16 w-auto"
        />
      </div>

      <!-- 桌面端 Logo -->
      <div class="hidden lg:block mb-6">
        <img
          src="@/assets/img/homepage/logo.png"
          alt="WeKnora Logo"
          class="h-16 w-auto"
        />
      </div>

      <!-- 副标题 -->
      <p class="text-xl text-gray-600 mb-8 lg:mb-12 text-center lg:text-left whitespace-normal lg:whitespace-nowrap">
        企业级智能文档检索，让复杂文档理解与精准检索变得简单
      </p>

      <!-- 核心卖点 - 移动端垂直排列，桌面端横向排列 -->
      <div class="flex flex-col lg:flex-row gap-4 lg:gap-7 mb-8 lg:mb-0">
        <div class="flex items-start gap-4 lg:gap-5">
          <svg class="w-5 h-5 flex-shrink-0 mt-1" fill="none" stroke="#2563EB" viewBox="0 0 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
          <div>
            <h3 class="text-lg font-semibold text-gray-900 mb-1">智能检索</h3>
            <p class="text-sm text-gray-600">多模态理解，精准定位</p>
          </div>
        </div>

        <div class="flex items-start gap-4 lg:gap-5">
          <svg class="w-5 h-5 flex-shrink-0 mt-1" fill="none" stroke="#2563EB" viewBox="0 0 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
          <div>
            <h3 class="text-lg font-semibold text-gray-900 mb-1">知识融合</h3>
            <p class="text-sm text-gray-600">RAG增强，知识图谱</p>
          </div>
        </div>

        <div class="flex items-start gap-4 lg:gap-5">
          <svg class="w-5 h-5 flex-shrink-0 mt-1" fill="none" stroke="#2563EB" viewBox="0 0 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
          <div>
            <h3 class="text-lg font-semibold text-gray-900 mb-1">安全可靠</h3>
            <p class="text-sm text-gray-600">企业级部署，数据安全</p>
          </div>
        </div>
      </div>

      <!-- 漂浮图片 - 仅在桌面端显示 -->
      <div class="hidden lg:block relative mt-12 h-48">
        <FloatingImages />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import FloatingImages from './FloatingImages.vue'
</script>

<style scoped lang="css">
.brand-showcase {
  min-height: auto;
  width: 100%;
}

@media (min-width: 1024px) {
  .brand-showcase {
    min-height: 100vh;
    width: 50%;
  }
}
</style>