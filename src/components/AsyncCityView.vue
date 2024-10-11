<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                You are currently previeweing this city, click the "+" icon to start tracking this city.
            </p>
        </div>
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{route.params.city}}</h1>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import {useRoute} from 'vue-router'
const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=$${route.query.lat}&lon=${route.query.lon}&exclude={part}&appid=${import.meta.env.VITE_SOME_KEY_WEATHER}&units=imperial`);

         // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime =
        utc + 1000 * weatherData.data.timezone_offset;
    });

    return weatherData.data;
    } catch (error)  {
        console.log(error)
    }

}

const weatherData = await getWeatherData();
</script>
