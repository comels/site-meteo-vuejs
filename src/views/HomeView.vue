<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";

const router = useRouter();
const search = ref("");
const error = ref(null);
const timeout = ref(null);
const mapboxResult = ref(null);
const token =
  "pk.eyJ1IjoiY29tZWxzIiwiYSI6ImNsaXNzMTEwcDFmcW4za29jZWdvNDF1aTIifQ.gFS7e9tnmN_9-uyYDzeoxQ";

const getSearch = () => {
  clearTimeout(timeout.value);
  timeout.value = setTimeout(async () => {
    if (search.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${token}&types=place`
        );
        mapboxResult.value = result.data.features;
      } catch {
        error.value = true;
      }
      return;
    }
    mapboxResult.value = null;
  }, 300);
};

const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        v-model="search"
        @input="getSearch"
        type="text"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus: outline-none"
        placeholder="Rechercher une ville"
      />
      <ul
        v-if="mapboxResult"
        class="absolute bg-weather-secondary text-white w-full py-2 px-1 top-[66px]"
      >
        <p v-if="error">Sorry, something went wrong, please try again.</p>
        <p v-if="!error && mapboxResult.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            @click="previewCity(city)"
            class="py-1 cursor-pointer"
            v-for="city in mapboxResult"
            :key="city.id"
          >
            {{ city.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <p>Loading...</p>
        </template>
      </Suspense>
    </div>
  </main>
</template>
