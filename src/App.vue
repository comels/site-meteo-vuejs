<script setup>
import { RouterView } from "vue-router";
import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<template>
  <div class="flex flex-col bg-weather-primary min-h-screen font-Roboto">
    <SiteNavigation />
    <RouterView />
  </div>
</template>
