<template>
  <div class="min-h-screen bg-white text-black p-4">
    <h1 class="text-3xl font-bold mb-4">Country Search</h1>
    <div class="mb-6">
      <input
        type="text"
        v-model="searchTerm"
        placeholder="Search for a country"
        class="p-2 border border-gray-300 rounded w-full"
      />
    </div>

    <div class="grid grid-cols-1 gap-4">
      <div
        v-for="country in displayedCountries"
        :key="country.key"
        class="p-4 border border-gray-800 rounded cursor-pointer"
        @click="goToCountryPage(country.key)"
      >
        {{ country.value }}
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import axios from 'axios'
import { computed, onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'

const searchTerm = ref('')
const countries = ref([])
const router = useRouter()

const fetchCountries = async () => {
  try {
    const response = await axios.get(
      'https://date.nager.at/Api/v2/AvailableCountries',
    )
    countries.value = response.data
  } catch (error) {
    console.error('oooopsss', error)
  }
}

const displayedCountries = computed(() => {
  return countries.value
    .filter((country: { key: string; value: string }) => {
      return (
        country.value &&
        country.value.toLowerCase().includes(searchTerm.value.toLowerCase())
      )
    })
    .slice(0, 10)
})

const goToCountryPage = (countryKey: string) => {
  router.push({ name: 'country', params: { countryKey } })
}

onMounted(() => {
  fetchCountries()
})
</script>
