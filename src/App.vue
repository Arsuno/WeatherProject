<template>
  <div class="card-container">
    <div class="card">
    <div class="search">
      <input v-model="cityName" type="text" class="search-bar" placeholder="Search">
      <button v-on:click="getCityCoordinates">
        <svg stroke="currentColor" fill="currentColor" stroke-width="0" viewBox="0 0 1024 1024" height="1.5em"
          width="1.5em" xmlns="http://www.w3.org/2000/svg">
          <path
            d="M909.6 854.5L649.9 594.8C690.2 542.7 712 479 712 412c0-80.2-31.3-155.4-87.9-212.1-56.6-56.7-132-87.9-212.1-87.9s-155.5 31.3-212.1 87.9C143.2 256.5 112 331.8 112 412c0 80.1 31.3 155.5 87.9 212.1C256.5 680.8 331.8 712 412 712c67 0 130.6-21.8 182.7-62l259.7 259.6a8.2 8.2 0 0 0 11.6 0l43.6-43.5a8.2 8.2 0 0 0 0-11.6zM570.4 570.4C528 612.7 471.8 636 412 636s-116-23.3-158.4-65.6C211.3 528 188 471.8 188 412s23.3-116.1 65.6-158.4C296 211.3 352.2 188 412 188s116.1 23.2 158.4 65.6S636 352.2 636 412s-23.3 116.1-65.6 158.4z">
          </path>
        </svg>
      </button>
    </div>
    <div v-bind:class="{visible: weatherReady, loaded: weatherReady}" class="weather loading">
      <h2 class="city">Weather in {{ cityNameTest }}</h2>
      <h1 class="temp">{{ (Math.round(cityWeatherTemp / 10)) }} °C</h1>
      <div class="flex">
        <img src="https://openweathermap.org/img/wn/04n.png" alt="" class="icon" />
        <div class="description">{{ this.cityWeather }}</div>
      </div>
      <div class="humidity">Humidity: {{ cityWeatherHumidity }}%</div>
      <div class="wind">Wind speed: {{cityWeatherWind}} km/h</div>
    </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios';

  export default {
    name: "App",

    data() {
      return {
        cityName: '',
        cityNameTest: '',
        cityWeatherTemp: '',
        cityWeatherHumidity: '',
        cityWeatherWind: '',
        cityWeather: '',
        coordsData: null,
        weatherData: null,
        weatherReady: false,
      }
    },
    methods: {

        getCityCoordinates() {
          this.cityNameTest = this.cityName;
          axios({
            url: `http://api.openweathermap.org/geo/1.0/direct`,
            params: {
              q: this.cityName,
              limit: 1,
              appid: 'b6a03ee98a074d5a17cd607cafb2d553',
            }
          })
          .then(({data}) => {
            this.coordsData = data[0];
            this.getCityWeatherData(data[0].lat, data[0].lon);
          })
        },
      
        getCityWeatherData(latCoordinates, lonCoordinates) {
          axios({
            url: `https://api.openweathermap.org/data/2.5/weather`,
            params: {
              lat: latCoordinates,
              lon: lonCoordinates,
              appid: `b6a03ee98a074d5a17cd607cafb2d553`,
            }
          })
          .then(({data}) => {
            this.weatherData = data;
            this.weatherReady = true;
            this.cityWeatherTemp = this.weatherData.main.temp;
            this.cityWeather = this.weatherData.weather[0].main;
            this.cityWeatherHumidity = this.weatherData.main.humidity;
            this.cityWeatherWind = this.weatherData.wind.speed;
          })
        },
      }
    }
</script>

<style>
  .card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  font-family: 'Open Sans', sans-serif;
  background: #222;
  background-image: url('https://source.unsplash.com/1920x1080/?landscape');
  font-size: 120%;
}

.visible {
  visibility: visible !important;
}

.card {
  
  background: #000000d0;
  color: white;
  padding: 2em;
  border-radius: 30px;
  width: 100%;
  max-width: 420px;
  margin: 1em;
}

.search {
  display: flex;
  align-items: center;
  justify-content: center;
}

button {
  margin: 0.5em;
  border-radius: 50%;
  border: none;
  height: 44px;
  width: 44px;
  outline: none;
  background: #7c7c7c2b;
  color: white;
  cursor: pointer;
  transition: 0.2s ease-in-out;
}

input.search-bar {
  border: none;
  outline: none;
  padding: 0.4em 1em;
  border-radius: 24px;
  background: #7c7c7c2b;
  color: white;
  font-family: inherit;
  font-size: 105%;
  width: calc(100% - 100px);
}

button:hover {
  background: #7c7c7c6b;
}

h1.temp {
  margin: 0;
  margin-bottom: 0.4em;
}

.flex {
  display: flex;
  align-items: center;
}

.description {
  text-transform: capitalize;
  margin-left: 8px;
}

.weather.loading {
  visibility: hidden;
  height: 20px;
  position: relative;
}

.loaded {
  height: 230px !important;
}
</style>