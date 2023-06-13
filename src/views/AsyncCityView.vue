<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const router = useRouter();
const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({
    name: "home",
  });
};

const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&lang=fr&exclude={part}&appid=02e577ae1011d96d3d6828104325cc40&units=metric`
    );

    // cal current date & time
    // getTimezoneOffset() : return the time difference between UTC and Local Time in minutes. Ensuite on converti en milliseconde.
    const localOffset = new Date().getTimezoneOffset() * 60000;

    // weatherData.data.current.dt = heure actuelle
    const utc = weatherData.data.current.dt * 1000 + localOffset;

    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });
    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};
const weatherData = await getWeatherData();
</script>

<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>Click + to add the city.</p>
    </div>
    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-2xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("fr", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("fr", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-5xl mb-5">
        {{ Math.round(weatherData.current.temp) }}&deg
      </p>

      <img
        class="h-auto w-[150px]"
        :src="`https://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt="icon-weather"
      />
    </div>
    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-5">
      <div class="mx-8 text-white">
        <div class="flex gap-10 overflow-x-scroll">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-sm">
              {{
                new Date(hourData.currentTime).toLocaleTimeString("fr", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[40px] object-cover"
              :src="`https://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt="icon-weather"
            />
            <p class="text-xl">{{ Math.round(hourData.temp) }}&deg</p>
          </div>
        </div>
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="capitalize flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("fr", {
                weekday: "short",
                day: "2-digit",
              })
            }}
          </p>
          <img
            class="h-[50px] w-[50px] object-cover"
            :src="`https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt="icon-weather"
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>{{ Math.round(day.temp.min) }}&deg /</p>
            <p>{{ Math.round(day.temp.max) }}&deg</p>
          </div>
        </div>
      </div>
    </div>

    <div
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>
