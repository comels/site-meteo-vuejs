<script setup>
const props = defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
console.log(props.city.weather);
</script>

<template>
  <div
    class="flex py-6 px-3 bg-weather-secondary rounded-md cursor-pointer shadow-md"
  >
    <div class="flex flex-col flex-1">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
    </div>
    <div class="flex flex-col gap-2">
      <p class="text-3xl self-end">
        {{ Math.round(city.weather.current.temp) }}&deg
      </p>
      <div class="flex gap-2">
        <span class="text-xs">
          {{ Math.round(city.weather.daily[0].temp.min) }}&deg /
        </span>
        <span class="text-xs">
          {{ Math.round(city.weather.daily[0].temp.max) }}&deg
        </span>
      </div>
    </div>
  </div>
</template>
