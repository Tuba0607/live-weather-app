<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />

      <ul
        class="absolute bg-weather-secondary text-white w-full py-2 px-1 top-[66px]"
        v-if="openWeatherSearchResults"
      >
        <li class="py-2 cursor-pointer">
          <p @click="previewCity(openWeatherSearchResults)">
            {{ openWeatherSearchResults.name }}
          </p>
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";

const router = useRouter();

const previewCity = (searchResults) => {
  const city = searchResults.name;
  router.push({
    name: "cityView",
    params: { city: city },
    query: {
      lat: searchResults.coord.lat,
      lng: searchResults.coord.lon,
      preview: true,
    },
  });
};
const url = "https://api.openweathermap.org/data/2.5/";
const openWeathermapAPIKEY = "9c68608e38c9b2ea2c3aa31dc808a014";
const searchQuery = ref("");
const queryTimeout = ref(null);
const openWeatherSearchResults = ref(null);
const searchError = ref(null);
const getSearchResults = () => {
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `${url}weather?q=${searchQuery.value}&appid=${openWeathermapAPIKEY}&units=metric`
        );
        openWeatherSearchResults.value = result.data;
      } catch (error) {
        searchError.value = true;
        console.error(error);
      }

      return;
    }
    openWeatherSearchResults.value = null;
  }, 300);
};
</script>
