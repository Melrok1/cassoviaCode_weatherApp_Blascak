<template>
  <div class="city">

    <!-- -----------HEADER----------- -->
    <header v-if="selectedCity">
      <section class="currentTimeAndDate">
        <p>{{ dateBuilder() }} | {{ normalizeTime() }}</p>
      </section>
      <section class="changeCityBtn" @click="showChangeCitySection">
        <p>{{selectedCity.name}}, Slovakia <img src="@/assets/svg/gps.svg" alt="gps"></p>
      </section>
    </header>

    <!-- -----------MAIN-----------  -->
    <main v-if="selectedCity">
      <div class="weather">
        <img 
          :src="require(`@/assets/weather-icon/${this.selectedCity.weather[0].icon}.svg`)" 
          width="40" 
          height="40"
          alt="weIco" 
        />
        <p>{{selectedCity.weather[0].main}}</p>
      </div>
      <div class="temp">{{ Math.round(selectedCity.main.temp) }}</div>
      <div class="minMax">
        <p>{{ Math.round(selectedCity.main.temp_min) }}°C <img src="@/assets/svg/arrow-up.svg" alt="up"></p>
        <p>{{ Math.round(selectedCity.main.temp_max) }}°C <img src="@/assets/svg/arrow-down.svg" alt="down"></p>
      </div>
      <div class="humidity">
        <img src="@/assets/svg/027-humidity.svg" alt="humIco">
        <p class="weatherValue">{{ selectedCity.main.humidity }}%</p>
        <p class="weatherTitle">Humidity</p>
      </div>
      <div class="pressure">
        <img src="@/assets/svg/050-barometer.svg" alt="barIco">
        <p class="weatherValue">{{ selectedCity.main.pressure }}mBar</p>
        <p class="weatherTitle">Pressure</p>
      </div>
      <div class="wind">
        <img src="@/assets/svg/001-wind-1.svg" alt="windIco">
        <p class="weatherValue">{{ Math.round(selectedCity.wind.speed * 3.6)}} km/h</p>
        <p class="weatherTitle">Wind</p>
      </div>
      <div class="sunrise">
        <img src="@/assets/svg/008-sunrise.svg" alt="sunrIco">
        <p class="weatherValue">{{ normalizeTime(selectedCity.sys.sunrise) }}</p>
        <p class="weatherTitle">Sunrise</p>
      </div>
      <div class="sunset">
        <img src="@/assets/svg/007-sunset.svg" alt="suntIco">
        <p class="weatherValue">{{ normalizeTime(selectedCity.sys.sunset) }}</p>
        <p class="weatherTitle">Sunset</p>        
      </div>
      <div class="daytime">
        <img src="@/assets/svg/sand-clock.svg" alt="clockIco">
        <p class="weatherValue">{{ dayTime(selectedCity.sys.sunrise, selectedCity.sys.sunset) }}</p>
        <p class="weatherTitle">Daytime</p> 
      </div>
      <div class="weatherForecast" v-for="(day, index) in forecast" :key="index">
        <img :src="require(`@/assets/weather-icon/${day.weatherIcon}.svg`)" alt="up" width="24" height="24"/>
        <p class="weatherValue">{{ day.date }} </p>
        <div class="minMaxRow">
          <p>{{ Math.round(day.tempMin) }}°C <img src="@/assets/svg/arrow-up.svg" alt="up"></p>
          <p>{{ Math.round(day.tempMax) }}°C <img src="@/assets/svg/arrow-down.svg" alt="down"></p>
        </div>
      </div>
    </main>

  </div>
</template>


<!-- /////////////////// SCRIPT ///////////////////// -->


<script>
import axios from 'axios'
// import { bus } from '@/main.js'

export default {
  name: 'Weather-report',
  props: ['selectedCity', 'api_key'],

  // ----------- DATA --------------
  data() {
    return {
      forecast: []
    }
  },

  // ----------- METHODS --------------
  methods: {
    showChangeCitySection() {
    //   bus.$emit('closeSelectCityPage', true);
      this.$emit('closeSelectCityPage', true);
    },

    normalizeTime(time = null) {

      var date = time ? new Date(time * 1000) : new Date();
      var hours = date.getHours();
      var minutes = date.getMinutes();
      var ampm = hours >= 12 ? 'pm' : 'am';

      hours = hours % 12;
      hours = hours ? hours : 12;
      minutes = minutes < 10 ? '0' + minutes : minutes;

      var strTime = hours + ':' + minutes + ' ' + ampm;

      return strTime;
    },

    dayTime(sunrise, sunset) {
      let time = (sunset - sunrise);
      let n = new Date(0,0);
      n.setSeconds(time);
      let dayTime = n.toTimeString().slice(0, 5).replace(':', 'h');

      return `${dayTime}m`;
    },

    dateBuilder(snap = null) {
      let d = snap ? new Date(snap * 1000) : new Date();
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let shortDays = ["Sun", "Mon", "Tue", "Wen", "Thu", "Fri", "Sat"];
      let months = [
        "Jan", "Feb", "Mar", "Apr", "May", "Jun", 
        "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
      ];

      let day = days[d.getDay()];
      let shortDay = shortDays[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return snap ? `${shortDay}, ${date}` : `${day}, ${date} ${month} ${year}`;
    }

  }, 

  // ----------- MOUNTED --------------
  mounted() {
    axios
      .get(`https://api.openweathermap.org/data/2.5/onecall?lat=${this.selectedCity ? 
        this.selectedCity.coord.lat : 48.713951}&lon=${this.selectedCity ? 
          this.selectedCity.coord.lon : 21.25808}&exclude=current,minutely,hourly,alerts&units=metric&appid=${this.api_key}`)
      .then(response => {
        let normalizeForecastData = [];
        
          response.data.daily.map(doc => {
            let singleDay = {
              date: this.dateBuilder(doc.dt),
              tempMin: doc.temp.min,
              tempMax: doc.temp.max,
              weather: doc.weather[0].main,
              weatherIcon: doc.weather[0].icon
            }
            normalizeForecastData.push(singleDay);
          });

        this.forecast = normalizeForecastData.filter((x, index) => index > 0 && index <= 3);
      });

  }
}
</script>


<!-- /////////////////// STYLE ///////////////////// -->


<style lang="scss" scoped>

.city {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 531px;
  background: #fff;
  border-radius: 25px 25px 0 0;
  box-shadow: 0px -16px 40px rgba(0, 0, 0, 0.2);

  header {
    @include displayFlex(row, space-between, center);
    width: 100%;
    height: 48px;
    border-radius: 25px 25px 0 0;

    .currentTimeAndDate {
      font-size: 14px;
      font-weight: 400;
      color: var(--app-text-primary-color);
      padding: 18px 8px 13px 5px ;
      margin: 0 auto;
    }

    .changeCityBtn {
      @include displayFlex(row, center, center);
      background: var(--city-name-background-color);
      min-width: 156px;
      height: 100%;
      border-radius: 0px 25px 0px 25px;
      font-size: 16px;
      font-weight: 500;
      color: var(--city-name-text-color);
      cursor: pointer;
    }
  }

  main {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(4, 1fr);
    padding: 24px 31px 39px 19px;
    height: calc(100% - 24px - 24px);

    .weatherTitle {
      font-size: 8px;
      font-weight: 500;
      color: var(--app-text-primary-color);
    }

    .weatherValue {
      font-size: 16px;
      font-weight: 500;
    }

    div {
      @include displayFlex(column, center, center);

      img {
        margin-bottom: 12px;
      }
    }

    .weather {
      font-size: 18px;
      font-weight: 500;
      color: #000000;

      img {
        opacity: 0.3;
      }
    }

    .temp {
      @include displayFlex(row, center, center);
      position: relative;
      font-weight: 300;
      font-size: 64px;

      &::after {
        content: '°C';
        font-size: 24px;
        font-weight: 500;
        color: #666666;
        position: relative;
        top: -15px;
        left: 0px;
      }
    }

    .humidity {
      font-size: 16px;
      font-weight: 500;
    }

    .minMax {
      color: #666666;
      font-size: 16px;
      font-weight: 300;
      padding-right: 30%;

      p {
        width: 100%;
        text-align: right;
      }

      p:nth-child(1) {
        img {
          position: relative;
          top: 8px;
          right: 0px;
        }
      }

      p:nth-child(2) {
        img {
          position: relative;
          top: 12px;
          right: 0px;
        }
      }
    }

    .weatherForecast {
      width: 95px;
      height: 101px;
      margin: 0 auto;
      border-radius: 16px;
      box-shadow: 0px 8px 24px rgba(0, 0, 0, 0.1);

      img {
        opacity: 0.4;
      }

      .minMaxRow {
        @include displayFlex(row, space-between, center);
        width: 100%;
        padding-left: 18px;
        padding-right: 18px;
        font-size: 8px;
        font-weight: 500;
        color: #aaaaaa;

        img {
          width: 5px;
          height: 7px;
          position: relative;
          top: 13px;
        }
      }
    }

  }
}

</style>