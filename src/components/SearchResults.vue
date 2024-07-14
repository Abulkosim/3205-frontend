<template>
  <div class="max-w-md w-full flex gap-2 mb-2 flex-col">
    <div v-if="results.length" v-for="result in results" :key="result.email"
         class="w-full bg-gray-800 p-4 rounded-lg shadow-md">
      <div class="flex items-center gap-4">
        <div class="w-16 h-16 rounded-full flex items-center justify-center border border-gray-700">
          <img :src="user" alt="user"/>
        </div>
        <div>
          <h5 class="text-lg font-semibold">{{ result.email }}</h5>
          <p class="text-sm">{{ result.number }}</p>
        </div>
      </div>
    </div>
    <div v-else class="w-full bg-gray-800 p-4 rounded-lg shadow-md">
      <p class="text-center text-lg font-semibold">No results found</p>
    </div>
  </div>
</template>

<script setup>
import user from '../assets/user.svg'

const props = defineProps(['results'])
</script>
