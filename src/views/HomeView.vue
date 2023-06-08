<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b duration-150 focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul v-if="mapBoxSearchResults?.length > 0 || searchError || (!searchError && mapBoxSearchResults?.length === 0)"
        class="absolute bg-weather secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">Sorry, something went wrong, please try again</p>
        <p v-if="!searchError && mapBoxSearchResults.length === 0">No results match your query, try a different term.</p>
        <template v-else>
          <li v-for="searchResult in mapBoxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';

const mapboxAPIKey = import.meta.env.VITE_MAPBOX_API_KEY;
const mapboxAPIUrl = import.meta.env.VITE_MAPBOX_API_URL;
const searchQuery = ref("")
const queryTimeout = ref(null)
const searchError = ref(null)
const mapBoxSearchResults = ref(null)

console.log();

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {

      try {
        const result = await axios.get(
          `${mapboxAPIUrl}/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapBoxSearchResults.value = result.data.features;
      } catch (error) {
        searchError.value = true
        setTimeout(() => searchError.value = false, 3000);
      }

      return;
    }
    mapBoxSearchResults.value = null;
  }, 300);
}
</script>