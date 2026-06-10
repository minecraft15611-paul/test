<template>
  <div class="min-h-screen bg-slate-100 font-sans">

    <!-- Header -->
    <header class="bg-white border-b border-gray-200 shadow-sm px-4 sm:px-8 py-4">
      <div class="max-w-6xl mx-auto flex flex-col sm:flex-row sm:items-center sm:justify-between gap-2">
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 rounded-xl bg-indigo-600 flex items-center justify-center shrink-0">
            <UsersIcon class="text-white w-5 h-5" />
          </div>
          <div>
            <h1 class="text-[25px] font-normal text-gray-800">帳號管理系統</h1>
            <p class="text-[16px] text-gray-400">管理您的所有帳號</p>
          </div>
        </div>
        <button
          @click="handleLogout"
          class="flex items-center gap-2 text-sm text-gray-500 hover:text-gray-800 transition cursor-pointer self-start sm:self-auto"
        >
          <LogOutIcon class="w-4 h-4 text-[16px]" />
          登出
        </button>
      </div>
    </header>

    <!-- Main -->
    <main class="max-w-6xl mx-auto px-4 sm:px-8 py-6 sm:py-8">

      <!-- Search + Add -->
      <div class="flex flex-col sm:flex-row items-stretch sm:items-center gap-3 mb-6">
        <div class="relative flex-1">
          <SearchIcon class="absolute left-4 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
          <input
            v-model="searchQuery"
            type="text"
            placeholder="搜尋帳號（姓名、郵件、角色）..."
            class="w-full pl-11 pr-4 py-3 border border-gray-200 rounded-xl text-sm text-gray-700 placeholder-gray-400 bg-white focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition"
          />
        </div>
        <button
          @click="openAddModal"
          class="flex items-center justify-center gap-2 bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-3 rounded-xl text-sm font-medium transition cursor-pointer whitespace-nowrap"
        >
          <PlusIcon class="w-4 h-4" />
          新增帳號
        </button>
      </div>

      <!-- Stats -->
      <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 mb-6">
        <div class="bg-white rounded-xl border border-gray-200 shadow-sm px-6 py-5">
          <p class="text-sm text-gray-500 mb-1">總帳號數</p>
          <p class="text-2xl font-semibold text-gray-800">{{ accountsStore.accounts.length }}</p>
        </div>
        <div class="bg-white rounded-xl border border-gray-200 shadow-sm px-6 py-5">
          <p class="text-sm text-gray-500 mb-1">啟用中</p>
          <p class="text-2xl font-semibold text-gray-800">{{ accountsStore.activeCount }}</p>
        </div>
        <div class="bg-white rounded-xl border border-gray-200 shadow-sm px-6 py-5">
          <p class="text-sm text-gray-500 mb-1">已停用</p>
          <p class="text-2xl font-semibold text-gray-800">{{ accountsStore.inactiveCount }}</p>
        </div>
      </div>

      <!-- Loading -->
      <div v-if="accountsStore.loading" class="flex justify-center py-20">
        <div class="w-8 h-8 border-4 border-indigo-200 border-t-indigo-600 rounded-full animate-spin"></div>
      </div>

      <!-- Error -->
      <div v-else-if="accountsStore.error" class="bg-red-50 border border-red-200 text-red-600 rounded-xl px-6 py-4 text-sm">
        {{ accountsStore.error }}
      </div>

      <!-- Account Cards -->
      <div v-else class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
        <AccountCard
          v-for="account in filteredAccounts"
          :key="account.id"
          :account="account"
          @edit="openEditModal"
          @delete="handleDelete"
        />
      </div>

      <!-- Empty -->
      <div v-if="!accountsStore.loading && !accountsStore.error && filteredAccounts.length === 0" class="bg-white rounded-xl border border-gray-200 shadow-sm py-16 flex flex-col items-center gap-3">
        <UsersIcon class="w-14 h-14 text-gray-300" />
        <p class="text-gray-500 font-medium">找不到帳號</p>
        <p class="text-sm text-gray-400">沒有符合搜尋條件的帳號</p>
      </div>

    </main>

    <!-- Modal -->
    <div
      v-if="showModal"
      class="fixed inset-0 bg-black/50 flex items-center justify-center z-50 px-4"
      @click.self="closeModal"
    >
      <div class="bg-white rounded-2xl w-full max-w-md p-6 sm:p-8">

        <div class="flex items-center justify-between mb-6">
          <h2 class="text-lg font-semibold text-gray-800">
            {{ isEditing ? '編輯帳號' : '新增帳號' }}
          </h2>
          <button @click="closeModal" class="text-gray-400 hover:text-gray-600 transition cursor-pointer">
            <XIcon class="w-5 h-5" />
          </button>
        </div>

        <div class="space-y-4">
          <div>
            <label class="block text-sm text-gray-600 mb-1.5">姓名 <span class="text-red-500">*</span></label>
            <input
              v-model="form.name"
              type="text"
              placeholder="請輸入姓名"
              class="w-full px-4 py-2.5 border border-gray-200 rounded-xl text-sm text-gray-700 placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition"
            />
          </div>
          <div>
            <label class="block text-sm text-gray-600 mb-1.5">電子郵件 <span class="text-red-500">*</span></label>
            <input
              v-model="form.email"
              type="email"
              placeholder="email@example.com"
              class="w-full px-4 py-2.5 border border-gray-200 rounded-xl text-sm text-gray-700 placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition"
            />
          </div>
          <div>
            <label class="block text-sm text-gray-600 mb-1.5">角色 <span class="text-red-500">*</span></label>
            <select
              v-model="form.roleLevel"
              class="w-full px-4 py-2.5 border border-gray-200 rounded-xl text-sm text-gray-700 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition bg-white"
            >
              <option value="ADMIN">管理員</option>
              <option value="EDITOR">編輯</option>
              <option value="USER">用戶</option>
              <option value="GUEST">訪客</option>
            </select>
          </div>
          <div>
            <label class="block text-sm text-gray-600 mb-1.5">狀態 <span class="text-red-500">*</span></label>
            <select
              v-model="form.status"
              class="w-full px-4 py-2.5 border border-gray-200 rounded-xl text-sm text-gray-700 focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent transition bg-white"
            >
              <option value="ON">啟用</option>
              <option value="OFF">停用</option>
            </select>
          </div>
        </div>

        <div class="flex gap-3 mt-6">
          <button
            @click="closeModal"
            class="flex-1 py-2.5 rounded-xl text-sm text-gray-500 bg-gray-100 hover:bg-gray-200 transition cursor-pointer"
          >
            取消
          </button>
          <button
            @click="handleSubmit"
            :disabled="submitting"
            class="flex-1 py-2.5 rounded-xl text-sm text-white bg-indigo-600 hover:bg-indigo-700 disabled:opacity-50 transition cursor-pointer"
          >
            {{ submitting ? '處理中...' : (isEditing ? '儲存變更' : '新增帳號') }}
          </button>
        </div>

      </div>
    </div>

  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import {
  Users as UsersIcon,
  LogOut as LogOutIcon,
  Search as SearchIcon,
  Plus as PlusIcon,
  X as XIcon,
} from 'lucide-vue-next'
import AccountCard from '@/components/AccountCard.vue'
import { useAccountsStore } from '@/stores/accounts'
import { useAuthStore } from '@/stores/auth'
import type { Account } from '@/api/accounts'

const router = useRouter()
const accountsStore = useAccountsStore()
const authStore = useAuthStore()

const searchQuery = ref('')
const debouncedQuery = ref('')
let debounceTimer: ReturnType<typeof setTimeout> | null = null

watch(searchQuery, (q) => {
  if (debounceTimer) clearTimeout(debounceTimer)
  debounceTimer = setTimeout(() => {
    debouncedQuery.value = q.trim().toLowerCase()
  }, 300)
})

const filteredAccounts = computed(() => {
  if (!debouncedQuery.value) return accountsStore.accounts
  return accountsStore.accounts.filter(a =>
    a.name?.toLowerCase().includes(debouncedQuery.value) ||
    a.email?.toLowerCase().includes(debouncedQuery.value) ||
    a.roleLevel?.toLowerCase().includes(debouncedQuery.value)
  )
})

const showModal = ref(false)
const isEditing = ref(false)
const submitting = ref(false)
const editingId = ref<string | null>(null)

const form = ref({
  name: '',
  email: '',
  roleLevel: 'USER' as 'ADMIN' | 'EDITOR' | 'USER' | 'GUEST',
  status: 'ON' as 'ON' | 'OFF',
})

function openAddModal() {
  isEditing.value = false
  editingId.value = null
  form.value = { name: '', email: '', roleLevel: 'USER', status: 'ON' }
  showModal.value = true
}

function openEditModal(account: Account) {
  isEditing.value = true
  editingId.value = account.id
  form.value = {
    name: account.name,
    email: account.email,
    roleLevel: account.roleLevel,
    status: account.status,
  }
  showModal.value = true
}

function closeModal() {
  showModal.value = false
}

async function handleSubmit() {
  if (!form.value.name || !form.value.email) return
  submitting.value = true
  try {
    if (isEditing.value && editingId.value) {
      await accountsStore.editAccount(editingId.value, form.value)
    } else {
      await accountsStore.addAccount(form.value)
    }
    closeModal()
  } catch {
    alert(isEditing.value ? '編輯失敗' : '新增失敗')
  } finally {
    submitting.value = false
  }
}

async function handleDelete(account: Account) {
  if (!confirm(`確定要刪除「${account.name}」？`)) return
  try {
    await accountsStore.removeAccount(account.id)
  } catch {
    alert('刪除失敗')
  }
}

function handleLogout() {
  authStore.logout()
  router.push('/login')
}

onMounted(() => accountsStore.fetchAccounts())
</script>