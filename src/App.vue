<template>
  <div id="app">
    <div class="bar">
      <input
        type="text"
        class="search-bar"
        placeholder="Search a City..."
        v-model="query"
        @keypress="fetchWeather"
      />
    </div>
    <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
      <div class="location-box">
        <Icons :weather="weather"/> 
        <div class="location">
          {{ weather.name }}, {{ weather.sys.country }}
        </div>
        <div class="date">{{ dateBuilder() }}</div>
      </div>
      <div class="weather-box" v-if="typeof weather.main != 'undefined'">
        <div class="temp">{{ Math.round(weather.main.temp) }}°C</div>
      </div>
    </div>
    <FiveDay :weatherFive="weatherFive" />
  </div>
</template>

<script>
import axios from "axios";
import FiveDay from "./components/FiveDay.vue";
import Icons from "./components/Icons";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";

export default {
  name: "App",
  components: {
    FiveDay,
    Icons,
  },
  data() {
    return {
      api_key: "2bbd84d695f75e90260a321f5b80b8b5",
      url_base: "https://api.openweathermap.org/data/2.5/",
      url_five: "https://api.openweathermap.org/data/2.5/forecast?q=",
      query: "London",
      weather: {},
      weatherFive: {},
    };
  },
  methods: {
    async fetch() {
      try {
        const response1 = await axios.get(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        );
        this.weather = response1.data;
        const response2 = await axios.get(
          `${this.url_five}${this.query}&appid=${this.api_key}`
        );
        console.log(response2.data.list);
        const hello = response2.data.list;
        const filterList = hello.filter((elem) => {
          return elem.dt_txt.includes("15:00:00");
        });
        this.weatherFive = filterList;
      } catch (e) {
        console.log(e);
      }
    },
    async fetchWeather(e) {
      if (e.key == "Enter") {
        this.fetch();
      }
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
  },
  mounted() {
    this.fetch();
  },
};
</script>

<style>
body {
  background-image: url("2850815.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}

.bar {
  text-align: center;
  margin-top: 8%;
}

input {
  position: absolute;
  top: 14%;
  left: 37%;
  width: 400px;
  height: 45px;
  border-radius: 30px;
  border: none;
  padding: 10px 40px;
  box-shadow: 0px 3px 0px #777;
  font-size: 17px;
}

.weather-wrap {
  position: relative;
}

.location-box,
.weather-box {
  text-align: center;
}
.date {
  font-size: 40px;
}
.temp {
  font-weight: bold;
  font-size: 80px;
}

.location {
  font-size: 30px;
}
</style>