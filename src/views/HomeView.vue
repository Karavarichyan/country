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

    <div v-if="isLoading" class="flex justify-center items-center">
      <div class="loader"></div>
    </div>

    <div v-else class="flex">
      <div class="flex-1">
        <div class="grid grid-cols-1 gap-4">
          <RouterLink
            v-for="country in displayedCountries"
            :country="country.key"
            class="p-4 border border-gray-800 rounded cursor-pointer w-82"
            :to="{ name: 'country', params: { countryKey: country.key } }"
          >
            {{ country.value }}
          </RouterLink>
        </div>
      </div>

      <div class="flex-1 ml-4">
        <RandomCountriesWidget :countries="randomCountries" />
        <ScrollTopButton />
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
import RandomCountriesWidget from '@/components/RandomCountriesWidget.vue'
import ScrollTopButton from '@/components/ScrollTopButton.vue'
import axios from 'axios'
import { computed, onMounted, ref } from 'vue'
import { RouterLink } from 'vue-router'

const searchTerm = ref('')
const countries = ref([])
const randomCountries = ref([])
const isLoading = ref(true)

const fetchCountries = async () => {
  isLoading.value = true
  try {
    const response = await axios.get(
      'https://date.nager.at/Api/v2/AvailableCountries',
    )
    countries.value = response.data
    await getRandomCountries()
  } catch (error) {
    console.error('oooopsss', error)
  } finally {
    isLoading.value = false
  }
}

const getRandomCountries = async () => {
  isLoading.value = true
  const random = []

  while (random.length < 3) {
    const randomIndex = Math.floor(Math.random() * countries.value.length)
    const country = countries.value[randomIndex]

    if (!random.includes(country)) {
      random.push(country)
    }
  }

  randomCountries.value = await Promise.all(
    random.map(country =>
      fetchNextHoliday(country.key).then(nextHoliday => ({
        value: country.value,
        key: country.key,
        nextHoliday,
      })),
    ),
  )
  isLoading.value = false
}

const fetchNextHoliday = async (countrycountry: string) => {
  const year = new Date().getFullYear()
  try {
    const response = await axios.get(
      `https://date.nager.at/api/v3/PublicHolidays/${year}/${countrycountry}`,
    )
    return response.data[0] || { name: 'No holidays', date: '' }
  } catch (error) {
    console.error('Error fetching next holiday:', error)
    return { name: 'Error fetching holiday', date: '' }
  }
}

const displayedCountries = computed(() => {
  return countries.value.filter(
    (country: { country: string; value: string }) => {
      return (
        country.value &&
        country.value.toLowerCase().includes(searchTerm.value.toLowerCase())
      )
    },
  )
})

onMounted(() => {
  fetchCountries()
})
</script>

<style>
.loader {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #000;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
}

@countryframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
