

<template>
  <main class="container text-black">
    <div class="pt-4 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state" class="py-4 px-2 w-full bg-transparent border-b-[2px] border-gray-300  focus:outline-none focus:shadow-lg">
      <ul v-if="mapboxSearchResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 mt-4 top-[66px]">
        <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 px-1  text-white cursor-pointer m-2 rounded-sm">
          {{ searchResult.properties.full_address }}
        </li>
      </ul>
    </div>
  </main>
</template>


<script setup>
import { ref } from "vue";
import axios from "axios";
const searchQuery = ref("")
const mapboxAPIKey = "pk.eyJ1IjoiZ2lvdmFubmlhcml6b2xhIiwiYSI6ImNtMjNyZDlmazA5MDEyaW9oNDVjeXk5ZWQifQ.sS35A0lHe16uUQJKr-ix0Q";
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value != "") {
      const result = await axios.get(`https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}&access_token=${mapboxAPIKey}&types=place`);
      mapboxSearchResults.value = result.data.features;
      console.log(mapboxSearchResults.value)
      return;
    }
    mapboxSearchResults.value = null;
  }, 1000);
}
</script>