<script setup>
import { ref } from "vue";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";
import { uid } from "uid";

const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};

const route = useRoute();
const router = useRouter();
const savedCities = ref([]);
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};
</script>

<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <p class="text-2xl font-light">METEOVUE</p>
        </div>
      </RouterLink>
      <div class="flex flex-1 justify-end gap-3">
        <i
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary cursor-pointer duration-200"
          @click="toggleModal"
        ></i>
        <i
          class="fa-solid fa-plus text-xl hover:text-weather-secondary cursor-pointer duration-200"
          @click="addCity"
          v-if="route.query.preview"
        ></i>
      </div>
      <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h2 class="text-2xl">Comment ça marche :</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>
              Recherchez votre ville en saisissant son nom dans la barre de
              recherche.
            </li>
            <li>Sélectionnez une ville parmi les résultats.</li>
            <li>
              Suivez la ville en cliquant sur l'icône "+" en haut à droite. La
              ville est ainsi enregistrée et peut être consultée ultérieurement
              sur la page d'accueil.
            </li>
          </ol>

          <h2 class="text-2xl">Supprimer une ville :</h2>
          <p>
            Si vous ne souhaitez plus suivre une ville, il vous suffit de la
            sélectionner sur la page d'accueil. En bas de la page, vous aurez la
            possibilité de supprimer la ville.
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>
