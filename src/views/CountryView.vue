<template>
  <div class="min-h-screen bg-white text-black p-4">
    <button
      @click="goToHome"
      class="mb-4 p-2 bg-blue-500 text-white rounded hover:bg-blue-600"
    >
      Go to Home
    </button>

    <h1 class="text-3xl font-bold mb-4">Country: {{ countryName }}</h1>
    <h2 class="text-xl mb-2">Upcoming Holidays:</h2>

    <div v-if="isLoading" class="flex justify-center items-center">
      <div class="loader"></div>
    </div>

    <div v-else>
      <div v-if="holidays.length === 0">
        No holidays found for this country.
      </div>
      <ul class="list-disc ml-6">
        <li v-for="holiday in holidays" :key="holiday.date" class="mb-2">
          <strong>{{ holiday.localName }}</strong> - {{ holiday.date }} ({{
            holiday.types.join(', ')
          }})
        </li>
      </ul>
    </div>

    <div class="mt-6">
      <h2 class="text-xl mb-2">Select Year:</h2>
      <div class="flex flex-wrap gap-2">
        <button
          v-for="year in years"
          :key="year"
          @click="selectYear(year)"
          :class="[
            'p-2 border border-gray-300 rounded',
            year === selectedYear
              ? 'bg-blue-500 text-white'
              : 'hover:bg-gray-100',
          ]"
          :disabled="year === selectedYear"
        >
          {{ year }}
        </button>
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
import axios from 'axios'
import { computed, onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const holidays = ref([])
const years = ref([
  2020, 2021, 2022, 2023, 2024, 2025, 2026, 2027, 2028, 2029, 2030,
])
const currentYear = ref(new Date().getFullYear())
const isLoading = ref(true)
const selectedYear = ref(currentYear.value)
const countryName = computed(() => route.params.countryKey as string)

const selectYear = (year: number) => {
  selectedYear.value = year
  fetchHolidays(year)
}

const fetchHolidays = async (year: number) => {
  isLoading.value = true
  try {
    const response = await axios.get(
      `https://date.nager.at/api/v3/PublicHolidays/${year}/${countryName.value}`,
    )
    holidays.value = response.data
  } catch (error) {
    console.error('Error:', error)
  } finally {
    isLoading.value = false
  }
}

const goToHome = () => {
  router.push({ name: 'home' })
}

onMounted(() => {
  fetchHolidays(selectedYear.value)
})
</script>
<style scoped>
.loader {
  @apply border-4 border-t-blue-500 border-gray-300 rounded-full w-10 h-10 animate-spin;
}
</style>
