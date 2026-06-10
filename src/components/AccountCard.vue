<template>
  <div class="bg-white rounded-xl border border-gray-200 shadow-sm p-6">

    <!-- Avatar + Name + Status -->
    <div class="flex items-center gap-3 mb-4">
      <div class="w-12 h-12 rounded-full bg-indigo-500 flex items-center justify-center flex-shrink-0">
        <UserIcon class="text-white w-6 h-6" />
      </div>
      <div>
        <span class="text-base font-medium text-gray-800 block">{{ account.name }}</span>
        <span
          :class="account.status === 'ON' ? 'bg-green-500' : 'bg-gray-400'"
          class="inline-block text-white text-[10px] font-medium px-2 py-0.5 rounded-full mt-1"
        >
          {{ account.status === 'ON' ? '啟用' : '停用' }}
        </span>
      </div>
    </div>

    <!-- Info -->
    <div class="space-y-2 mb-4">
      <div class="flex items-center gap-2 text-sm text-gray-500">
        <MailIcon class="w-4 h-4 flex-shrink-0" />
        <span class="truncate">{{ account.email }}</span>
      </div>
      <div class="flex items-center gap-2 text-sm text-gray-500">
        <UserCircleIcon class="w-4 h-4 flex-shrink-0" />
        <span>{{ roleLabel }}</span>
      </div>
      <div class="flex items-center gap-2 text-sm text-gray-500">
        <CalendarIcon class="w-4 h-4 flex-shrink-0" />
        <span>{{ formatDate(account.createdAt) }}</span>
      </div>
    </div>

    <!-- Buttons -->
    <div class="border-t border-gray-100 pt-4 flex gap-2">
      <button
        @click="$emit('edit', account)"
        class="flex-1 flex items-center justify-center gap-1.5 text-sm text-indigo-500 bg-indigo-50 hover:bg-indigo-100 py-2 rounded-lg transition cursor-pointer"
      >
        <PencilIcon class="w-3.5 h-3.5" />
        編輯
      </button>
      <button
        @click="$emit('delete', account)"
        class="flex-1 flex items-center justify-center gap-1.5 text-sm text-red-500 bg-red-50 hover:bg-red-100 py-2 rounded-lg transition cursor-pointer"
      >
        <Trash2Icon class="w-3.5 h-3.5" />
        刪除
      </button>
    </div>

  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import {
  User as UserIcon,
  Mail as MailIcon,
  UserCircle as UserCircleIcon,
  Calendar as CalendarIcon,
  Pencil as PencilIcon,
  Trash2 as Trash2Icon,
} from 'lucide-vue-next'
import type { Account } from '@/api/accounts'

const props = defineProps<{
  account: Account
}>()

defineEmits<{
  edit: [account: Account]
  delete: [account: Account]
}>()

const roleLabel = computed(() => {
  const map: Record<string, string> = {
    ADMIN: '管理員',
    EDITOR: '編輯',
    USER: '用戶',
    GUEST: '訪客',
  }
  return map[props.account.roleLevel] ?? props.account.roleLevel
})

function formatDate(iso: string) {
  return iso ? iso.slice(0, 10) : '-'
}
</script>