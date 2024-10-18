<template>
  <div class="min-h-screen bg-white text-black p-4">
    <h1 class="text-3xl font-bold mb-4">Country: {{ countryName }}</h1>
    <h2 class="text-xl mb-2">Upcoming Holidays:</h2>
    <div v-if="holidays.length === 0">No holidays found for this country.</div>
    <ul class="list-disc ml-6">
      <li v-for="holiday in holidays" :key="holiday.date" class="mb-2">
        <strong>{{ holiday.localName }}</strong> - {{ holiday.date }} ({{ holiday.types.join(', ') }})
      </li>
    </ul>

    <div class="mt-6">
      <h2 class="text-xl mb-2">Select Year:</h2>
      <div class="flex space-x-2">
        <button
          v-for="year in years"
          :key="year"
          @click="fetchHolidays(year)"
          class="p-2 border border-gray-300 rounded hover:bg-gray-100"
        >
          {{ year }}
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import { useRoute } from 'vue-router';
import axios from 'axios';

export default defineComponent({
  name: 'CountryView',
  setup() {
    const route = useRoute();
    const countryKey = route.params.countryKey as string; // Получаем параметр countryKey
    const holidays = ref([]); // Список праздников
    const years = ref([2020, 2021, 2022, 2023, 2024, 2025, 2026, 2027, 2028, 2029, 2030]); // Годы для переключения
    const currentYear = ref(new Date().getFullYear()); // Текущий год

    const countryName = computed(() => {
      return countryKey; // Здесь вы можете использовать API для получения имени страны, если нужно
    });

    const fetchHolidays = async (year: number) => {
      try {
        const response = await axios.get(`https://date.nager.at/api/v3/PublicHolidays/${year}/${countryKey}`);
        holidays.value = response.data; // Загружаем праздники для выбранного года и страны
      } catch (error) {
        console.error('Error fetching holidays:', error);
      }
    };

    // Загружаем праздники при монтировании компонента
    fetchHolidays(currentYear.value);

    return {
      countryKey,
      holidays,
      years,
      fetchHolidays,
      countryName,
    };
  },
});
</script>
