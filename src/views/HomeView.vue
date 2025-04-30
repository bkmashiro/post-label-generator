<script setup lang="ts">
import { ref, computed } from 'vue'
import PostLabel from '@/components/PostLabel.vue'

const isEditable = ref(false)
const postLabelRef = ref<InstanceType<typeof PostLabel> | null>(null)
const returnAddress = ref({
  name: 'Guo Baorou',
  nameRuby: '锅包肉',
  address: [
    '11st Floor, No. 4514, Yeshou St',
    'Wucheng District, Jinhua, Zhejiang 321000',
    'CHINA',
  ],
  addressRuby: [
    '野兽街 4514号 11楼',
    '浙江省金华市婺城区',
    '中华人民共和国',
  ]
})

const recipientAddress = ref({
  name: 'Zun Damon',
  nameRuby: 'ズンダモン',
  address: [
    'NAGISA BUILDING 3F, 1-1-1 YOYOGI',
    'SHIBUYA-KU, TOKYO 151-0053',
    'JAPAN'
  ],
  addressRuby: [
    'ナギサビル 3F 代々木1-1-1',
    '東京都渋谷区 〒151-0053',
    '日本'
  ]
})

const handlePrint = () => {
  window.print()
}

const updateReturnAddress = (field: keyof typeof returnAddress.value, value: any) => {
  if (field === 'address' || field === 'addressRuby') {
    returnAddress.value[field] = value.split('\n').filter(Boolean)
  } else {
    returnAddress.value[field] = value
  }
}

const updateRecipientAddress = (field: keyof typeof recipientAddress.value, value: any) => {
  if (field === 'address' || field === 'addressRuby') {
    recipientAddress.value[field] = value.split('\n').filter(Boolean)
  } else {
    recipientAddress.value[field] = value
  }
}

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
</script>

<template>
  <main class="min-h-screen bg-gray-100 py-8">
    <div class="container mx-auto px-4">
      <div class="flex justify-center gap-4 mb-8">
        <button @click="isEditable = !isEditable"
                class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
          {{ isEditable ? 'Hide Edit' : 'Show Edit' }}
        </button>
        <button @click="handlePrint"
                class="px-4 py-2 bg-green-500 text-white rounded-md hover:bg-green-600 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2">
          Print
        </button>
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

  .print-area {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
  }
}
</style>
