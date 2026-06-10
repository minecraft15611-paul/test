<template>
  <div class="min-h-screen bg-[#E9F1FF] flex flex-col items-center justify-center px-4 font-sans">

    <div class="bg-white rounded-2xl p-10 w-110 max-w-lg shadow-sm">

      <!-- Avatar icon -->
      <div class="flex justify-center mb-5">
        <div class="w-16 h-16 rounded-full bg-indigo-600 flex items-center justify-center">
          <LogInIcon class="text-white w-7 h-7" />
        </div>
      </div>

      <!-- Heading -->
      <div class="text-center mb-8">
        <h1 class="text-xl font-semibold text-gray-800">歡迎回來</h1>
        <p class="text-sm text-gray-400 mt-1">請登入您的帳號以繼續</p>
      </div>

      <!-- Fields -->
      <div class="space-y-5">

        <div>
          <label class="block text-sm text-gray-600 mb-1.5">電子郵件</label>
          <div class="relative">
            <MailIcon class="absolute left-3.5 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
            <input
              v-model="email"
              type="text"
              autocomplete="username"
              placeholder="your@email.com"
              :class="[
                'w-full pl-10 pr-4 py-3 border rounded-xl text-sm text-gray-700 placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition',
                errors.email ? 'border-red-400 bg-red-50' : 'border-gray-200'
              ]"
              @keyup.enter="handleLogin"
            />
          </div>
          <p v-if="errors.email" class="mt-1.5 text-xs text-red-500">{{ errors.email }}</p>
        </div>

        <div>
          <label class="block text-sm text-gray-600 mb-1.5">密碼</label>
          <div class="relative">
            <LockIcon class="absolute left-3.5 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
            <input
              v-model="password"
              type="password"
              autocomplete="new-password"
              placeholder="••••••••"
              :class="[
                'w-full pl-10 pr-4 py-3 border rounded-xl text-sm text-gray-700 placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition',
                errors.password ? 'border-red-400 bg-red-50' : 'border-gray-200'
              ]"
              @keyup.enter="handleLogin"
            />
          </div>
          <p v-if="errors.password" class="mt-1.5 text-xs text-red-500">{{ errors.password }}</p>
        </div>

        <!-- Remember + Forgot -->
        <div class="flex items-center justify-between pt-1">
          <label class="flex items-center gap-2 text-sm text-gray-600 cursor-pointer select-none">
            <input
              v-model="remember"
              type="checkbox"
              class="w-4 h-4 accent-indigo-600 rounded cursor-pointer"
            />
            記住我
          </label>
          <a href="#" class="text-sm text-indigo-500 hover:text-indigo-700 hover:underline transition">
            忘記密碼？
          </a>
        </div>

        <!-- Submit -->
        <button
          type="button"
          :disabled="loading"
          class="w-full bg-indigo-600 hover:bg-indigo-700 active:bg-indigo-800 disabled:opacity-60 text-white py-3 rounded-xl text-sm font-medium flex items-center justify-center gap-2 transition cursor-pointer"
          @click="handleLogin"
        >
          <LoaderIcon v-if="loading" class="w-4 h-4 animate-spin" />
          <LogInIcon v-else class="w-4 h-4" />
          {{ loading ? '登入中...' : '登入' }}
        </button>

        <!-- Hint -->
        <div class="border border-indigo-100 bg-indigo-50 rounded-xl px-4 py-3 text-sm text-indigo-400 text-center">
          💡 提示：輸入任意電子郵件和密碼即可登入
        </div>

      </div>
    </div>

    <!-- Footer -->
    <p class="mt-6 text-sm text-gray-500">
      還沒有帳號？
      <RouterLink to="/register" class="text-indigo-500 hover:text-indigo-700 hover:underline transition">
        立即註冊
      </RouterLink>
    </p>

  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { LogIn as LogInIcon, Mail as MailIcon, Lock as LockIcon, Loader as LoaderIcon } from 'lucide-vue-next'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const authStore = useAuthStore()

const email = ref('')
const password = ref('')
const remember = ref(false)
const loading = ref(false)
const errors = reactive({ email: '', password: '' })

function validate() {
  errors.email = ''
  errors.password = ''
  if (!email.value.trim()) {
    errors.email = '請輸入電子郵件'
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value.trim())) {
    errors.email = '請輸入有效的電子郵件格式'
  }
  if (!password.value) errors.password = '請輸入密碼'
  return !errors.email && !errors.password
}

async function handleLogin() {
  if (!validate()) return
  loading.value = true
  // API 無登入端點，直接 mock token 完成登入流程
  await new Promise(resolve => setTimeout(resolve, 300))
  authStore.setToken('mock-token')
  loading.value = false
  router.push('/dashboard')
}
</script>