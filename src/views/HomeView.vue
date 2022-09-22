<template>
  <div class="home">
    <div class="backgroundImage"></div>
    <!-- <HeaderTemplate :selectedCity="selectedCity"/> -->
    <WeatherReport
      v-if="!showSearchScreen"
      :selectedCity="selectedCity"
      :api_key="api_key"
    />
    <main></main>
  </div>
</template>




<script>
// import HeaderTemplate from '@/components/Header.vue'
import WeatherReport from "@/components/WeatherReport.vue";
import axios from "axios";

export default {
  name: "HomeView",
  components: {
    // HeaderTemplate,
    WeatherReport,
  },
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

  computed: {
    listOfCityId() {
      let listOfIds = [];
      this.query.forEach((city) => listOfIds.push(city._id));
      return listOfIds.toString();
    },
  },

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
      });
  },

  created() {
    // bus.$on("selectedCity", (data) => {
    //   this.selectedCity = data;
    // }),
    // bus.$on("closeSelectCityPage", (data) => {
    //   this.showSearchScreen = data;
    // });
  },
};
</script>











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
