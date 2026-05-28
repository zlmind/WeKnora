<template>
  <div class="login-page min-h-screen relative overflow-hidden">
    <!-- 背景图 -->
    <LoginBackground />

    <!-- 右上角：官方网站 -->
    <div class="absolute top-4 lg:top-6 right-4 lg:right-6 z-20">
      <OfficialLink
        url="https://htdt.cn/#/index"
        text="官方网站"
      />
    </div>

    <!-- 主要内容区域 -->
    <main class="relative z-10 min-h-screen flex flex-col lg:flex-row">
      <!-- 左侧品牌展示区 -->
      <BrandShowcase />

      <!-- 右侧登录表单区 -->
      <LoginFormCard
        :title="isRegisterMode ? $t('auth.createAccount') : $t('auth.login')"
        :subtitle="isRegisterMode ? $t('auth.registerSubtitle') : $t('auth.subtitle')"
        :show-footer="registrationEnabled"
        :footer-text="isRegisterMode ? $t('auth.haveAccount') : $t('auth.noAccount')"
        :footer-action-text="isRegisterMode ? $t('auth.backToLogin') : $t('auth.registerNow')"
        @footer-action="toggleMode"
      >
        <!-- 登录表单内容 -->
        <div v-if="!isRegisterMode" class="login-form-content">
          <form @submit.prevent="handleLogin" class="space-y-5">
            <!-- 邮箱 -->
            <div class="space-y-2">
              <label for="email" class="text-gray-700 text-sm font-medium block">邮箱</label>
              <input
                type="email"
                id="email"
                v-model="formData.email"
                :placeholder="$t('auth.emailPlaceholder')"
                :disabled="loading"
                class="w-full h-11 px-4 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all"
              >
            </div>

            <!-- 密码 -->
            <div class="space-y-2">
              <label for="password" class="text-gray-700 text-sm font-medium block">密码</label>
              <input
                type="password"
                id="password"
                v-model="formData.password"
                :placeholder="$t('auth.passwordPlaceholder')"
                :disabled="loading"
                @keydown.enter="handleLogin"
                class="w-full h-11 px-4 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all"
              >
            </div>

            <!-- 登录按钮 -->
            <button
              type="submit"
              :disabled="loading"
              class="w-full h-11 bg-blue-600 hover:bg-blue-700 text-white font-medium rounded-lg transition-all duration-200 hover:shadow-lg disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {{ loading ? $t('auth.loggingIn') : $t('auth.login') }}
            </button>

            <!-- OIDC 登录 -->
            <div v-if="oidcEnabled" class="oidc-section">
              <div class="oidc-divider">
                <span>{{ $t('auth.orContinueWith') }}</span>
              </div>
              <button
                type="button"
                :disabled="loading || oidcLoading"
                @click="handleOIDCLogin"
                class="w-full h-11 border border-gray-200 bg-white text-blue-600 hover:bg-gray-50 font-medium rounded-lg transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed"
              >
                {{ oidcLoading ? $t('auth.redirectingToOIDC') : oidcLoginText }}
              </button>
            </div>
          </form>
        </div>

        <!-- 注册表单内容 -->
        <div v-if="isRegisterMode" class="register-form-content">
          <form @submit.prevent="handleRegister" class="space-y-5">
            <!-- 用户名 -->
            <div class="space-y-2">
              <label for="reg-username" class="text-gray-700 text-sm font-medium block">用户名</label>
              <input
                type="text"
                id="reg-username"
                v-model="registerData.username"
                :placeholder="$t('auth.usernamePlaceholder')"
                :disabled="loading"
                class="w-full h-11 px-4 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all"
              >
            </div>

            <!-- 邮箱 -->
            <div class="space-y-2">
              <label for="reg-email" class="text-gray-700 text-sm font-medium block">邮箱</label>
              <input
                type="email"
                id="reg-email"
                v-model="registerData.email"
                :placeholder="$t('auth.emailPlaceholder')"
                :disabled="loading"
                class="w-full h-11 px-4 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all"
              >
            </div>

            <!-- 密码 -->
            <div class="space-y-2">
              <label for="reg-password" class="text-gray-700 text-sm font-medium block">密码</label>
              <input
                type="password"
                id="reg-password"
                v-model="registerData.password"
                :placeholder="$t('auth.passwordPlaceholder')"
                :disabled="loading"
                @keydown.enter="handleRegister"
                class="w-full h-11 px-4 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all"
              >
            </div>

            <!-- 确认密码 -->
            <div class="space-y-2">
              <label for="reg-confirmPassword" class="text-gray-700 text-sm font-medium block">确认密码</label>
              <input
                type="password"
                id="reg-confirmPassword"
                v-model="registerData.confirmPassword"
                :placeholder="$t('auth.confirmPasswordPlaceholder')"
                :disabled="loading"
                @keydown.enter="handleRegister"
                class="w-full h-11 px-4 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition-all"
              >
            </div>

            <!-- 注册按钮 -->
            <button
              type="submit"
              :disabled="loading"
              class="w-full h-11 bg-blue-600 hover:bg-blue-700 text-white font-medium rounded-lg transition-all duration-200 hover:shadow-lg disabled:opacity-50 disabled:cursor-not-allowed"
            >
              {{ loading ? $t('auth.registering') : $t('auth.register') }}
            </button>
          </form>
        </div>
      </LoginFormCard>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, onMounted, onBeforeUnmount } from 'vue'
import { useRouter } from 'vue-router'
import { useI18n } from 'vue-i18n'
import { MessagePlugin } from 'tdesign-vue-next'

// 导入新组件
import LoginBackground from '@/components/auth/LoginBackground.vue'
import BrandShowcase from '@/components/auth/BrandShowcase.vue'
import LoginFormCard from '@/components/auth/LoginFormCard.vue'
import OfficialLink from '@/components/auth/OfficialLink.vue'

// 导入 API 和 store
import { login, register, getOIDCAuthorizationURL, getOIDCConfig, getAuthConfig, userInfoFromApi, autoSetup } from '@/api/auth'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const authStore = useAuthStore()
const { t } = useI18n()

// 表单引用（已移除TDesign，不再需要）
// const formRef = ref()
// const registerFormRef = ref()
const loading = ref(false)
const oidcLoading = ref(false)
const oidcEnabled = ref(false)
const oidcProviderName = ref('')
const registrationEnabled = ref(true)
const isRegisterMode = ref(false)

// 注册表单引用
const registerFormRef = ref()

// OIDC 登录文本
const oidcLoginText = computed(() => {
  if (oidcProviderName.value) {
    return t('auth.oidcLoginWithProvider', { provider: oidcProviderName.value })
  }
  return t('auth.oidcLogin')
})

// 登录表单数据
const formData = reactive<{
  email: string
  password: string
}>({
  email: '',
  password: ''
})

// 登录表单验证规则
const formRules = computed(() => ({
  email: [
    { required: true, message: t('auth.emailRequired'), type: 'error' },
    { email: true, message: t('auth.emailInvalid'), type: 'error' }
  ],
  password: [
    { required: true, message: t('auth.passwordRequired'), type: 'error' },
    { min: 8, message: t('auth.passwordMinLength'), type: 'error' },
    { max: 32, message: t('auth.passwordMaxLength'), type: 'error' },
    { pattern: /[a-zA-Z]/, message: t('auth.passwordMustContainLetter'), type: 'error' },
    { pattern: /\d/, message: t('auth.passwordMustContainNumber'), type: 'error' }
  ]
}))

// 注册表单数据
const registerData = reactive<{
  username: string
  email: string
  password: string
  confirmPassword: string
}>({
  username: '',
  email: '',
  password: '',
  confirmPassword: ''
})

// 注册表单验证规则
const registerRules = computed(() => ({
  username: [
    { required: true, message: t('auth.usernameRequired'), type: 'error' },
    { min: 2, message: t('auth.usernameMinLength'), type: 'error' },
    { max: 20, message: t('auth.usernameMaxLength'), type: 'error' },
    {
      pattern: /^[a-zA-Z0-9_一-龥]+$/,
      message: t('auth.usernameInvalid'),
      type: 'error'
    }
  ],
  email: [
    { required: true, message: t('auth.emailRequired'), type: 'error' },
    { email: true, message: t('auth.emailInvalid'), type: 'error' }
  ],
  password: [
    { required: true, message: t('auth.passwordRequired'), type: 'error' },
    { min: 8, message: t('auth.passwordMinLength'), type: 'error' },
    { max: 32, message: t('auth.passwordMaxLength'), type: 'error' },
    { pattern: /[a-zA-Z]/, message: t('auth.passwordMustContainLetter'), type: 'error' },
    { pattern: /\d/, message: t('auth.passwordMustContainNumber'), type: 'error' }
  ],
  confirmPassword: [
    { required: true, message: t('auth.confirmPasswordRequired'), type: 'error' },
    {
      validator: (val: string) => val === registerData.password,
      message: t('auth.passwordMismatch'),
      type: 'error'
    }
  ]
}))

// 获取后端 OIDC 重定向 URI
const getBackendOIDCRedirectURI = () => `${window.location.origin}/api/v1/auth/oidc/callback`

// 加载 OIDC 配置
const loadOIDCConfig = async () => {
  try {
    const response = await getOIDCConfig()
    oidcEnabled.value = !!response.success && !!response.enabled
    oidcProviderName.value = response.provider_display_name || ''
  } catch {
    oidcEnabled.value = false
    oidcProviderName.value = ''
  }
}

// 加载认证配置
const loadAuthConfig = async () => {
  try {
    const response = await getAuthConfig()
    registrationEnabled.value = response.registration_mode !== 'invite_only'
  } catch {
    registrationEnabled.value = true
  }
}

// 处理 OIDC 登录
const handleOIDCLogin = async () => {
  try {
    oidcLoading.value = true
    const response = await getOIDCAuthorizationURL(getBackendOIDCRedirectURI())
    const authorizationURL = response.authorization_url

    if (!response.success || !authorizationURL) {
      MessagePlugin.error(response.message || t('auth.oidcLoginFailed'))
      return
    }

    window.location.href = authorizationURL
  } catch (error: any) {
    console.error('OIDC 登录跳转失败:', error)
    MessagePlugin.error(error.message || t('auth.oidcLoginFailed'))
  } finally {
    oidcLoading.value = false
  }
}

// 持久化登录响应
const persistLoginResponse = async (response: any) => {
  const activeTenant = response.active_tenant || response.tenant
  if (response.user && activeTenant && response.token) {
    const homeTenantIdRaw = response.user.tenant_id ?? activeTenant.id
    authStore.setUser(userInfoFromApi(response.user, homeTenantIdRaw))
    authStore.setToken(response.token)
    if (response.refresh_token) {
      authStore.setRefreshToken(response.refresh_token)
    }
    authStore.setTenant({
      id: String(activeTenant.id) || '',
      name: activeTenant.name || '',
      api_key: activeTenant.api_key || '',
      owner_id: response.user.id || '',
      created_at: activeTenant.created_at || new Date().toISOString(),
      updated_at: activeTenant.updated_at || new Date().toISOString()
    })
    if (Array.isArray(response.memberships)) {
      authStore.setMemberships(response.memberships)
    }

    const activeIdNum = Number(activeTenant.id)
    const homeIdNum = Number(homeTenantIdRaw)
    if (Number.isFinite(activeIdNum) && Number.isFinite(homeIdNum) && activeIdNum !== homeIdNum) {
      authStore.setSelectedTenant(activeIdNum, activeTenant.name || null)
    } else {
      authStore.setSelectedTenant(null, null)
    }
  }

  router.replace('/platform/knowledge-bases')
}

// 简单的表单验证函数
const validateEmail = (email: string) => {
  const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return re.test(email)
}

const validatePassword = (password: string) => {
  // 至少8位，包含字母和数字
  const hasLetter = /[a-zA-Z]/.test(password)
  const hasNumber = /[0-9]/.test(password)
  return password.length >= 8 && hasLetter && hasNumber
}

const validateUsername = (username: string) => {
  // 2-20位，支持字母、数字、下划线和中文字符
  return username.length >= 2 && username.length <= 20
}

// 处理登录
const handleLogin = async () => {
  try {
    // 手动验证表单
    if (!formData.email) {
      MessagePlugin.error(t('auth.emailRequired'))
      return
    }
    if (!validateEmail(formData.email)) {
      MessagePlugin.error(t('auth.emailInvalid'))
      return
    }
    if (!formData.password) {
      MessagePlugin.error(t('auth.passwordRequired'))
      return
    }
    if (!validatePassword(formData.password)) {
      MessagePlugin.error(t('auth.passwordMinLength'))
      return
    }

    loading.value = true

    const response = await login({
      email: formData.email,
      password: formData.password,
    })

    if (response.success) {
      await persistLoginResponse(response)
    } else {
      MessagePlugin.error(response.message || t('auth.loginError'))
    }
  } catch (error: any) {
    console.error('登录错误:', error)
    MessagePlugin.error(error.message || t('auth.loginErrorRetry'))
  } finally {
    loading.value = false
  }
}

// 切换登录/注册模式
const toggleMode = () => {
  isRegisterMode.value = !isRegisterMode.value

  // 清空注册表单
  Object.keys(registerData).forEach(key => {
    (registerData as any)[key] = ''
  })
}

// 处理注册
const handleRegister = async () => {
  try {
    // 手动验证表单
    if (!registerData.username) {
      MessagePlugin.error(t('auth.usernameRequired'))
      return
    }
    if (!validateUsername(registerData.username)) {
      MessagePlugin.error(t('auth.usernameMinLength'))
      return
    }
    if (!registerData.email) {
      MessagePlugin.error(t('auth.emailRequired'))
      return
    }
    if (!validateEmail(registerData.email)) {
      MessagePlugin.error(t('auth.emailInvalid'))
      return
    }
    if (!registerData.password) {
      MessagePlugin.error(t('auth.passwordRequired'))
      return
    }
    if (!validatePassword(registerData.password)) {
      MessagePlugin.error(t('auth.passwordMinLength'))
      return
    }
    if (registerData.password !== registerData.confirmPassword) {
      MessagePlugin.error(t('auth.passwordMismatch'))
      return
    }

    loading.value = true

    const response = await register({
      username: registerData.username,
      email: registerData.email,
      password: registerData.password
    })

    if (response.success) {
      MessagePlugin.success(t('auth.registerSuccess'))

      // 切换到登录模式并填入邮箱
      isRegisterMode.value = false
      formData.email = registerData.email

      // 清空注册表单
      Object.keys(registerData).forEach(key => {
        (registerData as any)[key] = ''
      })
    } else {
      MessagePlugin.error(response.message || t('auth.registerFailed'))
    }
  } catch (error: any) {
    console.error('注册错误:', error)
    MessagePlugin.error(error.message || t('auth.registerError'))
  } finally {
    loading.value = false
  }
}

// 组件挂载
onMounted(async () => {
  // 检查是否已登录
  if (authStore.isLoggedIn) {
    router.replace('/platform/knowledge-bases')
    return
  }

  // Lite 版本自动设置
  const AUTO_SETUP_FAILED_KEY = 'weknora_auto_setup_failed'
  if (localStorage.getItem(AUTO_SETUP_FAILED_KEY) !== 'true') {
    try {
      const response = await autoSetup()
      if (response.success) {
        authStore.setLiteMode(true)
        await persistLoginResponse(response)
        return
      } else {
        localStorage.setItem(AUTO_SETUP_FAILED_KEY, 'true')
      }
    } catch {
      localStorage.setItem(AUTO_SETUP_FAILED_KEY, 'true')
    }
  }

  loadOIDCConfig()
  loadAuthConfig()

  // 检查是否有保存的邮箱
  const savedEmail = localStorage.getItem('weknora_saved_email')
  if (savedEmail) {
    formData.email = savedEmail
  }
})

// 组件卸载
onBeforeUnmount(() => {
  // 清理工作
})
</script>

<style scoped lang="css">
.login-page {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}

/* 深色模式支持 */
@media (prefers-color-scheme: dark) {
  .login-page {
    color-scheme: dark;
  }
}

html[theme-mode="dark"] .login-page {
  color-scheme: dark;
}

/* 登录表单样式 */
.login-form-content {
  width: 100%;
}

.register-form-content {
  width: 100%;
}

.space-y-5 > * + * {
  margin-top: 1.25rem;
}

.space-y-2 > * + * {
  margin-top: 0.5rem;
}

/* 深色模式适配 */
html[theme-mode="dark"] .space-y-2 label {
  color: rgb(229, 231, 235);
}

html[theme-mode="dark"] .space-y-2 input {
  background: rgb(17, 24, 39);
  border-color: rgb(75, 85, 99);
  color: rgb(229, 231, 235);
}

html[theme-mode="dark"] .space-y-2 input::placeholder {
  color: rgb(107, 114, 128);
}

/* OIDC相关样式 */
.oidc-section {
  margin-top: 1rem;
}

.oidc-divider {
  position: relative;
  margin: 0.5rem 0 0.5rem;
  text-align: center;
  color: rgb(156, 163, 175);
  font-size: 0.75rem;
}

.oidc-divider span {
  position: relative;
  z-index: 1;
  padding: 0 0.75rem;
  background: rgba(255, 255, 255, 0.95);
}

.oidc-divider::before {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  border-top: 1px solid rgb(229, 231, 235);
}
</style>
