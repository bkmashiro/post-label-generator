<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import PostLabel from '@/components/PostLabel.vue'

const isEditable = ref(false)
const postLabelRef = ref<InstanceType<typeof PostLabel> | null>(null)

interface Contact {
  name: string;
  nameRuby: string;
  address: string[];
  addressRuby: string[];
}

interface ContactPair {
  id: string;
  name: string;
  returnAddress: Contact;
  recipientAddress: Contact;
}

// 从localStorage加载地址，如果没有则使用默认值
const loadAddress = (key: string, defaultValue: any) => {
  const saved = localStorage.getItem(key)
  return saved ? JSON.parse(saved) : defaultValue
}

const returnAddress = ref(loadAddress('returnAddress', {
  name: 'YAMADA TARO',
  nameRuby: 'ヤマダタロウ',
  address: [
    'YAMADA BUILDING 3F',
    '1-1-1 YOYOGI, SHIBUYA-KU',
    'TOKYO 151-0053',
    'JAPAN'
  ],
  addressRuby: [
    'ヤマダビル 3F',
    '東京都渋谷区代々木1-1-1',
    '〒151-0053',
    '日本'
  ]
}))

const recipientAddress = ref(loadAddress('recipientAddress', {
  name: 'JOHN SMITH',
  nameRuby: 'ジョン・スミス',
  address: [
    '123 MAIN STREET',
    'NEW YORK, NY 10001',
    'UNITED STATES OF AMERICA'
  ],
  addressRuby: [
    '123 メインストリート',
    'ニューヨーク州 ニューヨーク 10001',
    'アメリカ合衆国'
  ]
}))

// 联系人管理
const contacts = ref<ContactPair[]>(loadAddress('contacts', []))
const newContactName = ref('')
const showContactModal = ref(false)

const saveCurrentAsContact = () => {
  if (!newContactName.value.trim()) return

  const newContact: ContactPair = {
    id: Date.now().toString(),
    name: newContactName.value,
    returnAddress: { ...returnAddress.value },
    recipientAddress: { ...recipientAddress.value }
  }

  contacts.value.push(newContact)
  saveAddress('contacts', contacts.value)
  newContactName.value = ''
  showContactModal.value = false
}

const loadContact = (contact: ContactPair) => {
  returnAddress.value = { ...contact.returnAddress }
  recipientAddress.value = { ...contact.recipientAddress }
  saveAddress('returnAddress', returnAddress.value)
  saveAddress('recipientAddress', recipientAddress.value)
}

const deleteContact = (id: string) => {
  contacts.value = contacts.value.filter(c => c.id !== id)
  saveAddress('contacts', contacts.value)
}

// 防抖函数
const debounce = (fn: Function, delay: number) => {
  let timeout: number
  return (...args: any[]) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => fn(...args), delay)
  }
}

// 监听地址变化并保存到localStorage
const saveAddress = debounce((key: string, value: any) => {
  localStorage.setItem(key, JSON.stringify(value))
}, 500)

const updateReturnAddress = (field: string, value: any) => {
  if (field === 'address' || field === 'addressRuby') {
    returnAddress.value[field] = value.split('\n')
  } else {
    returnAddress.value[field] = value
  }
  saveAddress('returnAddress', returnAddress.value)
}

const updateRecipientAddress = (field: string, value: any) => {
  if (field === 'address' || field === 'addressRuby') {
    recipientAddress.value[field] = value.split('\n')
  } else {
    recipientAddress.value[field] = value
  }
  saveAddress('recipientAddress', recipientAddress.value)
}

// 交换地址
const swapAddresses = () => {
  const temp = { ...returnAddress.value }
  returnAddress.value = { ...recipientAddress.value }
  recipientAddress.value = temp
  saveAddress('returnAddress', returnAddress.value)
  saveAddress('recipientAddress', recipientAddress.value)
}

// 计算属性用于textarea绑定
const returnAddressText = computed({
  get: () => returnAddress.value.address.join('\n'),
  set: (value) => updateReturnAddress('address', value)
})

const returnAddressRubyText = computed({
  get: () => returnAddress.value.addressRuby.join('\n'),
  set: (value) => updateReturnAddress('addressRuby', value)
})

const recipientAddressText = computed({
  get: () => recipientAddress.value.address.join('\n'),
  set: (value) => updateRecipientAddress('address', value)
})

const recipientAddressRubyText = computed({
  get: () => recipientAddress.value.addressRuby.join('\n'),
  set: (value) => updateRecipientAddress('addressRuby', value)
})

const handlePrint = () => {
  window.print()
}
</script>

<template>
  <main class="min-h-screen bg-gray-100 py-8">
    <div class="container mx-auto px-4">
      <div class="flex justify-center gap-4 mb-8">
        <button @click="isEditable = !isEditable"
                class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
          {{ isEditable ? '隐藏编辑' : '显示编辑' }}
        </button>
        <button @click="showContactModal = true"
                class="px-4 py-2 bg-purple-500 text-white rounded-md hover:bg-purple-600 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-offset-2">
          保存联系人
        </button>
        <button @click="swapAddresses"
                class="px-4 py-2 bg-yellow-500 text-white rounded-md hover:bg-yellow-600 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:ring-offset-2">
          交换地址
        </button>
        <button @click="handlePrint"
                class="px-4 py-2 bg-green-500 text-white rounded-md hover:bg-green-600 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2">
          打印
        </button>
      </div>

      <!-- 联系人列表 -->
      <div v-if="contacts.length > 0"
           class="mb-8">
        <h3 class="text-lg font-semibold mb-2">已保存的联系人</h3>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          <div v-for="contact in contacts"
               :key="contact.id"
               class="bg-white p-4 rounded-lg shadow hover:shadow-md transition-shadow">
            <div class="flex justify-between items-start mb-2">
              <h4 class="font-medium">{{ contact.name }}</h4>
              <div class="flex gap-2">
                <button @click="loadContact(contact)"
                        class="text-blue-500 hover:text-blue-600">
                  加载
                </button>
                <button @click="deleteContact(contact.id)"
                        class="text-red-500 hover:text-red-600">
                  删除
                </button>
              </div>
            </div>
            <div class="text-sm text-gray-600">
              <div>{{ contact.returnAddress.name }} → {{ contact.recipientAddress.name }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- 保存联系人对话框 -->
      <div v-if="showContactModal"
           class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
        <div class="bg-white p-6 rounded-lg shadow-lg max-w-md w-full">
          <h3 class="text-lg font-semibold mb-4">保存联系人</h3>
          <input v-model="newContactName"
                 placeholder="输入联系人名称"
                 class="w-full px-3 py-2 border border-gray-300 rounded-md mb-4 focus:outline-none focus:ring-2 focus:ring-blue-500" />
          <div class="flex justify-end gap-2">
            <button @click="showContactModal = false"
                    class="px-4 py-2 text-gray-600 hover:text-gray-800">
              取消
            </button>
            <button @click="saveCurrentAsContact"
                    class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
              保存
            </button>
          </div>
        </div>
      </div>

      <div v-if="isEditable"
           class="grid grid-cols-1 md:grid-cols-2 gap-8 mb-8">
        <!-- Return Address Form -->
        <div class="bg-white p-6 rounded-lg shadow">
          <h2 class="text-xl font-bold mb-4">Return Address</h2>
          <div class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Name</label>
              <input v-model="returnAddress.name"
                     @input="e => updateReturnAddress('name', (e.target as HTMLInputElement).value)"
                     class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" />
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Name Ruby</label>
              <input v-model="returnAddress.nameRuby"
                     @input="e => updateReturnAddress('nameRuby', (e.target as HTMLInputElement).value)"
                     class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" />
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Address (one line per row)</label>
              <textarea v-model="returnAddressText"
                        rows="4"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Address Ruby (one line per row)</label>
              <textarea v-model="returnAddressRubyText"
                        rows="4"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
            </div>
          </div>
        </div>

        <!-- Recipient Address Form -->
        <div class="bg-white p-6 rounded-lg shadow">
          <h2 class="text-xl font-bold mb-4">Recipient Address</h2>
          <div class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Name</label>
              <input v-model="recipientAddress.name"
                     @input="e => updateRecipientAddress('name', (e.target as HTMLInputElement).value)"
                     class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" />
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Name Ruby</label>
              <input v-model="recipientAddress.nameRuby"
                     @input="e => updateRecipientAddress('nameRuby', (e.target as HTMLInputElement).value)"
                     class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500" />
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Address (one line per row)</label>
              <textarea v-model="recipientAddressText"
                        rows="4"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Address Ruby (one line per row)</label>
              <textarea v-model="recipientAddressRubyText"
                        rows="4"
                        class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"></textarea>
            </div>
          </div>
        </div>
      </div>

      <PostLabel v-model:return-address="returnAddress"
                 v-model:recipient-address="recipientAddress"
                 :is-editable="false"
                 ref="postLabelRef"
                 class="print-area" />
    </div>
  </main>
</template>

<style>
@media print {
  /* .controls {
    display: none;
  } */

  .container>*:not(.print-area) {
    display: none;
  }

  #vue-inspector-container,
  #__vue-devtools-container__ {
    display: none;
  }

  .print-area {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
  }
}
</style>
