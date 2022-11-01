<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''">
    <div class="unit-measure-btn">
      <button @click="unitMeasure='Celsius'">°C</button>
      <button @click="unitMeasure='Fahrenheit'">°F</button>
    </div>
    <main>
      <div class="search-box">
        <input
            type="text"
            class="search-bar"
            placeholder="Search weather for your location..."
            v-model="query"
            @keypress="fetchWeatherBySearch"
        />
        <span class="get-my-location" @click="getGeolocation">
          <svg width="24px" height="24px" viewBox="0 0 24 24" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
              <!-- Uploaded to SVGRepo https://www.svgrepo.com -->
              <title>ic_fluent_my_location_24_regular</title>
              <desc>Created with Sketch.</desc>
              <g id="🔍-Product-Icons" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                  <g id="ic_fluent_my_location_24_regular" fill="#212121" fill-rule="nonzero">
                      <path d="M12,2 C12.3796958,2 12.693491,2.28215388 12.7431534,2.64822944 L12.75,2.75 L12.7490685,4.53770881 L12.7490685,4.53770881 C16.292814,4.88757432 19.1124257,7.70718602 19.4632195,11.2525316 L19.5,11.25 L21.25,11.25 C21.6642136,11.25 22,11.5857864 22,12 C22,12.3796958 21.7178461,12.693491 21.3517706,12.7431534 L21.25,12.75 L19.4616558,12.7490368 L19.4616558,12.7490368 C19.1124257,16.292814 16.292814,19.1124257 12.7474684,19.4632195 L12.75,19.5 L12.75,21.25 C12.75,21.6642136 12.4142136,22 12,22 C11.6203042,22 11.306509,21.7178461 11.2568466,21.3517706 L11.25,21.25 L11.2509632,19.4616558 L11.2509632,19.4616558 C7.70718602,19.1124257 4.88757432,16.292814 4.53678051,12.7474684 L4.5,12.75 L2.75,12.75 C2.33578644,12.75 2,12.4142136 2,12 C2,11.6203042 2.28215388,11.306509 2.64822944,11.2568466 L2.75,11.25 L4.53770881,11.2509315 L4.53770881,11.2509315 C4.88757432,7.70718602 7.70718602,4.88757432 11.2525316,4.53678051 L11.25,4.5 L11.25,2.75 C11.25,2.33578644 11.5857864,2 12,2 Z M12,6 C8.6862915,6 6,8.6862915 6,12 C6,15.3137085 8.6862915,18 12,18 C15.3137085,18 18,15.3137085 18,12 C18,8.6862915 15.3137085,6 12,6 Z M12,8 C14.209139,8 16,9.790861 16,12 C16,14.209139 14.209139,16 12,16 C9.790861,16 8,14.209139 8,12 C8,9.790861 9.790861,8 12,8 Z" id="🎨-Color"></path>
                  </g>
              </g>
          </svg>
        </span>
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ temperatureCalculate }}° {{ unitMeasure }}</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      api_key: 'be705611798054ea6e2377559fe6518f',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      unitMeasure: 'Celsius',
      weather: {},
      latitude: '',
      longitude: ''
    }
  },
  created() {
    let vm = this;

    vm.getGeolocation();
  },

  methods: {
    fetchWeatherBySearch(e) {
      if (e.key === "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
            .then(res => {
              return res.json();
            }).then(this.setResults);
      }
    },
    fetchWeatherByGeolocation() {
      fetch(`${this.url_base}weather?lat=${this.latitude}&lon=${this.longitude}&limit=5&units=metric&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
    },
    setResults(results) {
      this.weather = results;
    },
    dateBuilder() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
    getGeolocation() {
      let vm = this;

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            position => {
              console.log("position ", position)
              vm.latitude = position.coords.latitude;
              vm.longitude = position.coords.longitude;

              console.log("lati ", vm.latitude);
              console.log("longi ", vm.longitude);

              vm.fetchWeatherByGeolocation();
            },
            error => {
              console.log(error.message);
            });
      } else {
        console.log("Your browser does not support geolocation API ")
      }
    }
  },

  computed: {
    temperatureCalculate() {
      let vm = this,
          temperature = Math.round(vm.weather.main.temp);

      if (vm.unitMeasure === "Fahrenheit") {
        temperature = Math.round((Math.round(vm.weather.main.temp) * 1.8) + 32);
      }

      return temperature;
    }
  }
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  display: flex;
  font-family: 'montserrat', sans-serif;
  justify-items: center;
}

#app {
  display: flex;
  flex-wrap: wrap;
  background-image: url('./assets/snow-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
  width: 100%;

  .unit-measure-btn {
    display: block;
    position: absolute;
    top: 0;
    right: 0;
    padding: 15px 10px;
    gap: 10px;

    button {
      width: 30px;
      height: 30px;
      font-size: 20px;
      box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
      border-radius: 100%;
      background: rgba(255, 255, 255, 0.5) none;
    }
  }

  main {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    min-height: 100vh;
    width: 100%;
    padding: 25px;
    background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));

    .search-box {
      display: flex;
      width: 100%;
      align-items: center;
      padding: 0 10px 0 0;
      margin: auto;
      gap: 10px;

      box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
      background: rgba(255, 255, 255, 0.5) none;
      border-radius: 16px;

      .get-my-location {
        display: block;
        width: 28px;
      }

      .search-bar {
        display: block;
        width: 100%;
        padding: 15px;

        color: #313131;
        font-size: 20px;
        appearance: none;
        border: none;
        outline: none;
        box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
        background: rgba(255, 255, 255, 0.5) none;
        border-radius: 16px;
        transition: 0.4s;
      }
    }



    .location-box {
      .location {
        color: #FFF;
        font-size: 32px;
        font-weight: 500;
        text-align: center;
        text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
      }

      .date {
        color: #FFF;
        font-size: 20px;
        font-weight: 300;
        font-style: italic;
        text-align: center;
        padding: 15px;
      }
    }

    .weather-box {
      text-align: center;

      .temp {
        display: inline-block;
        padding: 10px 25px;
        color: #FFF;
        font-size: 60px;
        font-weight: 900;
        text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
        background-color: rgba(255, 255, 255, 0.25);
        border-radius: 16px;
        margin: 30px 0;
        box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
      }

      .weather {
        color: #FFF;
        font-size: 48px;
        font-weight: 700;
        font-style: italic;
        text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
      }
    }
  }

}

#app.warm {
  background-image: url('./assets/hot-bg.jpg');
}

</style>