<template>
  <div class="changeCity">
    <!-- =======> HEADER <========= -->
    <header>
      <h6>Location</h6>
    </header>

    <!-- =======> MAIN <========= -->
    <main>
      <input type="text" placeholder="Search city ..." v-model="inputData" />
      <div class="gpsImage">
        <img src="@/assets/svg/gps_black.svg" alt="gps" />
      </div>
      <ul class="listOfCities">
        <li
          v-for="(city, index) in filterCities"
          :key="index"
          @click="changeCity(city)"
        >
          <p class="paragraphCityName">{{ city.name }}</p>
          <p class="paragraphCityTemp">{{ Math.round(city.main.temp) }}°C</p>
        </li>
      </ul>
    </main>
  </div>
</template>



<!-- 
============================================================
||||==================== SCRIPT ========================||||
============================================================
-->



<script>
export default {
  name: "ChangeCity",
  props: ["weather"],

  // ----------- DATA --------------
  data() {
    return {
      inputData: null,
    };
  },

  // ----------- COMPUTED --------------
  computed: {
    filterCities() {
      if (this.inputData) {
        return this.weather.filter((data) =>
          data.name.toLowerCase().includes(this.inputData.toLowerCase())
        );
      } else {
        return this.weather;
      }
    },
  },

  // ----------- METHODS --------------
  methods: {
    changeCity(city) {
      // console.log(city);
      this.$emit("selectedCity", city);
      this.$emit("closeSelectCityPage", false);
    },
  },
};
</script>



<!-- 
============================================================
||||===================== STYLE ========================||||
============================================================
-->



<style lang="scss" scoped>
.changeCity {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 781px;
  background: #fff;
  border-radius: 25px 25px 0 0;
  box-shadow: 0px -16px 40px rgba(0, 0, 0, 0.2);

  header {
    @include displayFlex(row, center, center);

    h6 {
      display: inline-block;
      margin: 28px auto;
      font-size: 16px;
      font-weight: 500;
      color: var(--app-text-primary-color);
    }
  }

  main {
    @include displayFlex(column, center, center);
    position: relative;
    width: 100%;

    .gpsImage img {
      position: absolute;
      top: 11px;
      right: 35px;
      width: 11px;
      height: 16px;
      color: #000;
    }

    input {
      position: relative;
      border: none;
      outline: none;
      padding: 9px 15px;
      background: var(--input-background-color);
      width: 335px;
      height: 40px;
      margin: 0 auto;
      border-radius: 3px;
      font-size: 18px;
      font-weight: 500;
      color: var(--input-text-color);

      &::placeholder {
        font-size: 18px;
        font-weight: 500;
        font-style: italic;
      }
    }

    .listOfCities {
      list-style-type: none;
      width: 100%;
      padding: 39px 35px 0px 35px;

      li {
        @include displayFlex(row, space-between, center);
        margin-bottom: 8px;
        padding: 2px;
        cursor: pointer;

        &:hover {
          background: var(--city-name-background-color);
        }

        p {
          display: inline-block;
        }

        .paragraphCityName {
          font-size: 18px;
          font-weight: 400;
          color: #444444;
        }

        .paragraphCityTemp {
          font-size: 16px;
          font-weight: 300;
          color: #666666;
        }
      }
    }
  }
}
</style>