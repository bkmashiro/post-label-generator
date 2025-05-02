<script setup lang="ts">
import { ref, defineExpose } from 'vue'

interface Address {
  name: string;
  nameRuby: string;
  address: string[];
  addressRuby: string[];
}

interface Props {
  returnAddress: Address;
  recipientAddress: Address;
}

const props = defineProps<Props>()

</script>

<template>
  <div class="w-full max-w-4xl min-h-[400px] border border-gray-300 p-5 bg-white mx-auto my-5 relative">
    <!-- Return Address -->
    <div class="absolute top-0 left-0 w-1/3 scale-70">
      <div class="space-y-2">
        <div class="font-bold">
          <ruby class="text-sm">
            {{ props.returnAddress.name }}
            <rt v-if="props.returnAddress.nameRuby"
                class="text-xs text-gray-600">{{ props.returnAddress.nameRuby }}</rt>
          </ruby>
        </div>
        <div class="space-y-1">
          <div v-for="(line, index) in props.returnAddress.address"
               :key="index">
            <ruby class="text-sm">
              {{ line }}
              <rt v-if="props.returnAddress.addressRuby[index]?.trim()"
                  class="text-xs text-gray-600">{{ props.returnAddress.addressRuby[index] }}</rt>
            </ruby>
          </div>
        </div>
      </div>
    </div>

    <!-- Recipient Address -->
    <div class="absolute top-32 left-1/2 origin-top-left transform -translate-x-1/3 w-2/3">
      <div class="space-y-2">
        <div class="font-bold">
          <ruby class="text-base">
            {{ props.recipientAddress.name }}
            <rt v-if="props.recipientAddress.nameRuby"
                class="text-xs text-gray-600">{{ props.recipientAddress.nameRuby }}</rt>
          </ruby>
        </div>
        <div class="space-y-1">
          <div v-for="(line, index) in props.recipientAddress.address"
               :key="index">
            <ruby class="text-base">
              {{ line }}
              <rt v-if="props.recipientAddress.addressRuby[index]?.trim()"
                  class="text-xs text-gray-600">{{ props.recipientAddress.addressRuby[index] }}</rt>
            </ruby>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@media print {
  .post-label {
    border: none;
    margin: 0;
    padding: 0;
  }
}
</style>
