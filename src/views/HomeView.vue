

<template>
  <main class="container text-black">
    <div class="pt-4 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state" class="py-4 px-2 w-full bg-transparent border-b-[2px] border-gray-300  focus:outline-none focus:shadow-lg">
      <ul v-if="mapboxSearchResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 mt-4 top-[66px]">
        <p v-if="searchError">Sorry, something went wrong! Please try again.</p>
        <p v-if="!searchError && mapboxSearchResults.length === 0">No results matched your query. Please try again.</p>

        <template v-else>
          <li 
          v-for="searchResult in mapboxSearchResults" :key="searchResult.id" 
          class="py-2 px-1  text-white cursor-pointer m-2 rounded-sm"
          @click="previewCity(searchResult)"
        >
            {{ searchResult.properties.full_address }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>


<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
const searchQuery = ref("")
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)
const searchError = ref(null)
const router = useRouter();


const previewCity = (searchResult) => {
  const [city, state] = searchResult.properties.full_address.split(",")
  const lat = searchResult.properties.coordinates.latitude;
  const long = searchResult.properties.coordinates.longitude;
  console.log (lat, long)

  // console.log(city, state);
  router.push({
    name: "cityView",
    params: {state: state.replaceAll(" ", ""), city: city},
    query: {
      lat: lat,
      long: long
    },
    preview: true,
  })
}

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value != "") {
      try {
        const result = await axios.get(`https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}&access_token=${import.meta.env.VITE_SOME_KEY}&types=place`);
        mapboxSearchResults.value = result.data.features;
      // console.log(mapboxSearchResults.value)
      } catch {
        searchError.value = true;
      }
      
      return;
    }
    mapboxSearchResults.value = null;
  }, 1000);
}
</script>