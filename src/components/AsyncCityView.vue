<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                You are currently previeweing this city, click the "+" icon to start tracking this city.
            </p>
        </div>
        <div class="flex flex-col items-center text-weatherp-secondary py-12">
            <h1 class="text-4xl mb-2">{{route.params.city}}</h1>
             <p class="text-sm mb-12">
                {{
                  new Date(weatherData.dt).toLocaleDateString(
                    "en-us",
                    {
                      weekday: "short",
                      day: "2-digit",
                      month: "long",
                    }
                  )
                }}
                {{
                  new Date(weatherData.dt).toLocaleTimeString(
                    "en-us",
                    {
                      timeStyle: "short",
                    }
                  )
                }}
              </p>
              <p class="text-8xl mb-8">
                {{ Math.round(weatherData.main.temp) }} &deg;
              </p>
              <p>
                Feels like
                {{ Math.round(weatherData.main.feels_like) }} &deg;
              </p>
              <div class="flex gap-4">
                <p>
                    Max:
                    {{ Math.round(weatherData.main.temp_max) }} &deg;
                  </p>
                  <p>
                    Min:
                    {{ Math.round(weatherData.main.temp_min) }} &deg;
                  </p>
              </div>
              <p class="capitalize">
                {{ weatherData.weather[0].description }}
              </p>
              <img
                class="w-[150px] bg-weather-gray rounded-full h-auto mt-8"
                :src="
                  `http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`
                "
                alt=""
              />
              <div class="flex items-center gap-2 py-12 text-red-400 cursor-pointer durations-150 hover:text-red-500" @click="removeCity">
                <i class="fa-solid fa-trash"></i>
                <p>Remove City</p>
              </div>
        </div>

       
    </div>
</template>

<script setup>
import axios from 'axios';
import {useRoute, useRouter} from 'vue-router'
const route = useRoute();

const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.long}&appid=${import.meta.env.VITE_SOME_KEY_WEATHER}&units=imperial`);

    return weatherData.data;
    console.log(weatherData.data);
    } catch (error)  {
        console.log(error)
    }

}

const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCities"));
    const updatedCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem('savedCities', JSON.stringify(updatedCities));
    router.push({name: "home"})
}
</script>
