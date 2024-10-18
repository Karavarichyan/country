<template>
  <div class="p-5">
    <input
      v-model="searchQuery"
      type="text"
      placeholder="Search countries"
      class="w-full p-2 mb-5 border"
    />
    <ul>
      <li v-for="country in filteredCountries" :key="country.name">
        <router-link :to="`/country/${country.alpha2Code}`">{{ country.name }}</router-link>
      </li>
    </ul>

    <h2 class="mt-10 text-lg font-semibold">Random Countries Widget</h2>
    <div class="grid grid-cols-1 gap-4 mt-5">
      <div v-for="country in randomCountries" :key="country.name" class="p-4 border">
        <h3>{{ country.name }}</h3>
        <p>Next Holiday: {{ country.nextHoliday.localName }}</p>
        <p>Date: {{ country.nextHoliday.date }}</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from 'vue';
import axios from 'axios';

export default defineComponent({
  setup() {
    const searchQuery = ref('');
    const countries = ref([]);
    const randomCountries = ref([]);

    // Fetch all countries
    onMounted(async () => {
      const { data } = await axios.get('https://date.nager.at/api/v2/AvailableCountries');
      countries.value = data;

      // Fetch random 3 countries' holidays
      for (let i = 0; i < 3; i++) {
        const randomCountry = data[Math.floor(Math.random() * data.length)];
        const holidayData = await axios.get(`https://date.nager.at/api/v2/PublicHoliday/2024/${randomCountry.alpha2Code}`);
        randomCountries.value.push({
          name: randomCountry.name,
          nextHoliday: holidayData.data[0],
        });
      }
    });

    const filteredCountries = computed(() => {
      return countries.value.filter(country =>
        country.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    });

    return { searchQuery, filteredCountries, randomCountries };
  },
});
</script>
