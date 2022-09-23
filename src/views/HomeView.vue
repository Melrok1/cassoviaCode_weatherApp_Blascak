<template>
  <div class="home">

    <div class="backgroundImage"></div>

    <SearchCity 
      v-if="showSearchScreen" 
      :weather="weather"
      @selectedCity="(city) => this.selectedCity = city"
      @closeSelectCityPage="(styte) => this.showSearchScreen = state"
    />

    <WeatherReport
      v-if="!showSearchScreen"
      :selectedCity="selectedCity"
      :api_key="api_key"
      @closeSelectCityPage="(state) => this.showSearchScreen = state"
    />

  </div>
</template>



<!-- 
============================================================
||||==================== SCRIPT ========================||||
============================================================
-->



<script>

import WeatherReport from '@/components/WeatherReport.vue'
import SearchCity from '@/components/CitySearch.vue'
import axios from 'axios'

export default {
  name: "HomeView",
  // ------ COMPONENTS ---------
  components: {
    WeatherReport,
    SearchCity
  },
  // ------ DATA ---------
  data() {
    return {
      api_key: "799bcbb5b55d3dc99bf1e0f1674a5b28",
      query: [
        { name: "Bratistava", _id: 3060972 },
        { name: "Humenné", _id: 724627 },
        { name: "Košice", _id: 724443 },
        { name: "Michalovce", _id: 724144 },
        { name: "Sobrance", _id: 723554 },
      ],
      weather: null,
      selectedCity: null,
      showSearchScreen: false,
    };
  },
  // ------ COMPUTED ---------
  computed: {
    listOfCityId() {
      let listOfIds = [];
      this.query.forEach((city) => listOfIds.push(city._id));
      return listOfIds.toString();
    },
  },
  // ------ MOUNTED ---------
  mounted() {
    axios
      .get(
        `https://api.openweathermap.org/data/2.5/group?id=${this.listOfCityId}&weather?q=&units=metric&appid=${this.api_key}`
      )
      .then((response) => {
        this.weather = response.data.list;
        this.selectedCity = response.data.list[2];
      })
      .then(() => {
        if (this.weather) {
          axios
            .get(
              `https://api.openweathermap.org/data/2.5/weather?q=Koromľa&weather?q=&units=metric&appid=${this.api_key}`
            )
            .then((response) => {
              this.weather.push(response.data);
            });
        } else {
          console.log("get city by name err");
        }
      })
      .catch(error => {
      console.error("openweathermap error", error);
    });
  },
  // ------ CREATED ---------
  created() {},
};
</script>



<!-- 
============================================================
||||===================== STYLE ========================||||
============================================================
-->



<style lang="scss" scoped>
	.backgroundImage {
		width: 100%;
		height: 300px;
		background-image: url("@/assets/img/cityBackground.png");
		background-position: top;
		background-repeat: no-repeat;
		background-size: cover;
	}
</style>
