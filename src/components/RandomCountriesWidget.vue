<template>
  <div
    class="sticky top-0 bg-white z-10 p-4 border border-gray-800 rounded shadow"
  >
    <h2 class="text-2xl font-bold mb-4">Random Countries Widget</h2>
    <div class="grid grid-cols-1 gap-4">
      <div
        v-for="randomCountry in countries"
        :country="randomCountry.country"
        class="p-4 border border-gray-800 rounded bg-gray-100 hover:bg-gray-200"
      >
        <h3 class="font-semibold text-lg">{{ randomCountry.value }}</h3>
        <p>Next Holiday: {{ randomCountry.nextHoliday.localName }}</p>
        <p>Date: {{ randomCountry.nextHoliday.date }}</p>
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
defineProps({
  countries: Array,
})
</script>
