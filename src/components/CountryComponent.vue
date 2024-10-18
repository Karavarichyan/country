<template>
  <div class="p-5">
    <h1>{{ countryName }}</h1>

    <div class="flex space-x-2 mt-5">
      <button
        v-for="year in years"
        :key="year"
        :class="year === selectedYear ? 'bg-black text-white' : 'border'"
        @click="fetchHolidays(year)"
        class="p-2"
      >
        {{ year }}
      </button>
    </div>

    <div class="grid grid-cols-1 gap-4 mt-5">
      <div v-for="holiday in holidays" :key="holiday.date" class="p-4 border">
        <h3>{{ holiday.localName }}</h3>
        <p>{{ holiday.date }}</p>
        <p>Type: {{ holiday.type }}</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import axios from 'axios';

export default defineComponent({
  setup() {
    const route = useRoute();
    const countryName = ref('');
    const holidays = ref([]);
    const selectedYear = ref(new Date().getFullYear());
    const years = ref(Array.from({ length: 11 }, (v, i) => 2020 + i));

    // Fetch holidays by year
    const fetchHolidays = async (year: number) => {
      const countryCode = route.params.countryCode;
      selectedYear.value = year;
      const { data } = await axios.get(`https://date.nager.at/api/v2/PublicHoliday/${year}/${countryCode}`);
      holidays.value = data;
    };

    // Fetch holidays for current year on mount
    onMounted(() => {
      fetchHolidays(selectedYear.value);
    });

    return { countryName, holidays, years, selectedYear, fetchHolidays };
  },
});
</script>
