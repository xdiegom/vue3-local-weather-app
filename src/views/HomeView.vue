<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b duration-150 focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul v-if="mapBoxSearchResults?.length > 0 || searchError || (!searchError && mapBoxSearchResults?.length === 0)"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">Sorry, something went wrong, please try again</p>
        <p v-if="!searchError && mapBoxSearchResults.length === 0">No results match your query, try a different term.</p>
        <template v-else>
          <li @click="previewCity(searchResult)" v-for="searchResult in mapBoxSearchResults" :key="searchResult.id"
            class="py-2 cursor-pointer">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>

    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList/>
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import CityList from '../components/CityList.vue';
import CityCardSkeleton from '../components/CityCardSkeleton.vue';

const mapboxAPIKey = import.meta.env.VITE_MAPBOX_API_KEY;
const mapboxAPIUrl = import.meta.env.VITE_MAPBOX_API_URL;
const searchQuery = ref("")
const queryTimeout = ref(null)
const searchError = ref(null)
const mapBoxSearchResults = ref(null)
const router = useRouter();

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

const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(',')

  router.push({
    name: 'cityView',
    params: {
      state: state.replaceAll(" ", ""),
      city: city.replaceAll(" ", "")
    },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}
</script>