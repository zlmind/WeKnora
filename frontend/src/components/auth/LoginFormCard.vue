<template>
  <div class="login-form-container w-full flex items-center justify-center p-6 sm:p-8 py-12 lg:py-0">
    <div class="w-full max-w-md">
      <!-- 移动端Logo -->
      <div class="lg:hidden flex justify-center mb-8">
        <img
          src="@/assets/img/homepage/logo.png"
          alt="WeKnora Logo"
          class="h-16 w-auto"
        />
      </div>

      <!-- 登录卡片 -->
      <div class="login-card bg-white dark:bg-gray-800 rounded-2xl shadow-2xl p-8 sm:p-10 border border-gray-100 dark:border-gray-700">
        <div class="mb-8">
          <h2 class="text-2xl font-bold text-gray-900 dark:text-gray-100 mb-2">{{ title }}</h2>
          <p class="text-base text-gray-600 dark:text-gray-400">欢迎使用千橙云智能文档检索平台</p>
        </div>

        <!-- 表单内容插槽 -->
        <slot></slot>

        <!-- 底部链接 -->
        <div v-if="showFooter" class="text-center text-sm text-gray-600 dark:text-gray-400">
          {{ footerText }}
          <a
            href="#"
            @click.prevent="$emit('footerAction')"
            class="text-blue-600 dark:text-blue-400 hover:text-blue-700 dark:hover:text-blue-300 font-medium transition-colors"
          >
            {{ footerActionText }}
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  title: string
  subtitle: string
  showFooter?: boolean
  footerText?: string
  footerActionText?: string
}

withDefaults(defineProps<Props>(), {
  showFooter: true,
  footerText: '还没有账户？',
  footerActionText: '立即注册'
})

defineEmits<{
  footerAction: []
}>()
</script>

<style scoped lang="css">
.login-form-container {
  min-height: auto;
}

@media (min-width: 1024px) {
  .login-form-container {
    min-height: 100vh;
    width: 50%;
  }
}

.login-card {
  width: 100%;
}

/* 全局隐藏表单必填星号 */
:deep(.t-form-item__required),
:deep(.t-is-required .t-form-item__required),
:deep(.t-form-item__label .t-form-item__required) {
  display: none !important;
  visibility: hidden !important;
  opacity: 0 !important;
  width: 0 !important;
  height: 0 !important;
  position: absolute !important;
  left: -9999px !important;
}

:deep(.t-form-item__label::before),
:deep(.t-form-item__label::after) {
  display: none !important;
  content: none !important;
}
</style>