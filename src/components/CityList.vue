<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"></CityCard>
    </div>

    <p v-if="savedCities.length === 0">
        No locations addded. To start tracking a location, search in the filed above.
    </p>
</template>

<script setup>
import axios from 'axios';
import CityCard from './CityCard.vue';
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const savedCities = ref([]);

const openWeatherApiKey = import.meta.env.VITE_OPENWEATHER_API_KEY;
const openWeatherApiUrl = import.meta.env.VITE_OPENWEATHER_API_URL;

const getCities = async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

        const requests = [];

        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`${openWeatherApiUrl}/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${openWeatherApiKey}&units=imperial`)
            )
        });

        const weatherData = await Promise.all(requests);

        await new Promise((res) => setTimeout(res, 1000));
        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
    }
}

await getCities();

const router = useRouter()
const goToCityView = (city) => {
    router.push({
        name: 'cityView',
        params: {
            state: city.state,
            city: city.city
        },
        query: { 
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng 
        }
    })
}

</script>