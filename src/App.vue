<template>
  <div
      class="w-full min-h-screen bg-gray-900 text-slate-400 flex items-center flex-col gap-4 font-sans pt-8 select-none">
    <SearchForm @setLoading="setLoading" @getResults="updateResults"/>
    <Loader v-if="isLoading"/>
    <SearchResults v-if="!isLoading && isVisible" :results="results"/>
  </div>
</template>

<script setup>
import {nextTick, ref} from 'vue'
import SearchForm from './components/SearchForm.vue'
import SearchResults from './components/SearchResults.vue'
import Loader from './components/Loader.vue'

const isLoading = ref(false)
const results = ref([])
const isVisible = ref(false)

const setLoading = (value) => {
  results.value = []
  isLoading.value = value
}

const updateResults = async (newResults) => {
  results.value = []
  await nextTick()
  results.value = Array.from(newResults)
  isVisible.value = true
}
</script>

