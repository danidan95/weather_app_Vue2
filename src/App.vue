<template>
  <div id="app" :class="getWeatherCondition">
    <main>
      <div class="search-box">
        <input
            type="text"
            class="search-bar"
            placeholder="Search weather for your city..."
            v-model="query"
            @keypress="fetchWeatherBySearch"
        />
        <span class="get-my-location" @click="getGeolocation">
          <svg width="24px" height="24px" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
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
          <div class="unit-measure-btn">
            <button @click="unitMeasure='Celsius'">°C</button>
            <button @click="unitMeasure='Fahrenheit'">°F</button>
          </div>
          <div class="temp">{{ temperatureCalculate }}° {{ unitMeasure }}</div>
          <div class="weather-type">{{ weatherCondition }}</div>
          <div class="humidity"> Humidity: {{ weather.main.humidity }} %</div>
          <div class="wind"> Wind: {{ weather.wind.speed }} M/S</div>
          <div class="sunrise"> Sunrise: {{ unixToTime(weather.sys.sunrise) }} </div>
          <div class="sunset"> Sunset: {{ unixToTime(weather.sys.sunset) }} </div>
        </div>
      </div>

      <div v-else class="error-msg"> ~ City not found ~ </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      api_key: 'be705611798054ea6e2377559fe6518f',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      unitMeasure: 'Celsius',
      weather: {},
      latitude: '',
      longitude: '',
      weatherCondition: '',
    }
  },
  created() {
    let vm = this;

    //Get geolocation on creation of Vue App and on callback get weather for that location.
    vm.getGeolocation();
  },

  methods: {
    //API call for weather by search input
    fetchWeatherBySearch(e) {
      if (e.key === "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
            .then(res => {
              return res.json();
            }).then(this.setResults);
      }
    },

    //API call for weather by Geolocation
    fetchWeatherByGeolocation() {
      fetch(`${this.url_base}weather?lat=${this.latitude}&lon=${this.longitude}&limit=5&units=metric&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
    },

    setResults(results) {
      this.weather = results;
    },

    //Builds up the date as shown on interface
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

    //Gets the time in UNIX and converts it into TIME in the timezone that we are searching. (for current location)
    unixToTime(s) {
      let timeZone = Intl.DateTimeFormat().resolvedOptions().timeZone;

      const dtFormat = new Intl.DateTimeFormat('en-GB', {
        timeStyle: 'medium',
        timeZone: timeZone
      });

      return dtFormat.format(new Date(s * 1e3));
    },

    //Gets current location and on success callback fetch the weather for this location.
    getGeolocation() {
      let vm = this;

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            position => {
              vm.latitude = position.coords.latitude;
              vm.longitude = position.coords.longitude;

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
    //Computes weather type so it will change background according to it
    getWeatherCondition() {
      let vm = this,
          weather = vm.weather;

      if (weather.weather !== undefined) {
        weather.weather.forEach(function (wtr) {
          vm.weatherCondition = wtr.main;
        })
      }

      // weatherCondition = weather.weather[0].main;

      return vm.weatherCondition;
    },

    //Calculates temperature on toggle buttons for Celsius or Fahrenheit
    temperatureCalculate() {
      let vm = this,
          temperature = Math.round(vm.weather.main.temp);

      if (vm.unitMeasure === "Fahrenheit") {
        temperature = Math.round((Math.round(vm.weather.main.temp) * 1.8) + 32);
      }

      return temperature;
    },
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
  overflow: hidden;
}

#app {
  display: flex;
  flex-wrap: wrap;
  background-image: url('./assets/snow-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
  width: 100%;

  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    width: 100%;
    margin: auto;
    padding: 25px;
    background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));

    .search-box {
      display: flex;
      width: 100%;
      max-width: 600px;
      align-items: center;
      padding: 0 10px 0 0;
      margin: 0 20px;
      gap: 10px;

      box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
      background: rgba(255, 255, 255, 0.5) none;
      border-radius: 16px;

      .get-my-location {
        display: block;
        cursor: pointer;
        position: relative;
        top: 1px;

        svg {
          transition: all 0.4s ease-in-out;

          &:hover {
            width: 28px;
            height: 28px;
          }
        }


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
      padding: 20px;
      margin: 40px 0;

      .location {
        color: #FFF;
        font-size: 38px;
        font-weight: 500;
        text-align: center;
        text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
      }

      .date {
        color: #FFF;
        font-size: 24px;
        font-weight: 300;
        font-style: italic;
        text-align: center;
        padding: 15px 0 0;
      }
    }

    .weather-box {
      text-align: center;
      background-color: rgba(255, 255, 255, 0.25);
      border-radius: 16px;
      box-shadow: 2px 4px 4px rgba(0, 0, 0, 0.25);
      padding: 20px;

      .unit-measure-btn {
        display: flex;
        gap: 10px;
        justify-content: flex-end;
        height: 40px;

        button {
          width: 30px;
          height: 30px;
          line-height: 30px;
          font-size: 17px;
          box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
          border-radius: 100%;
          background: rgba(255, 255, 255, 0.5) none;
          border: none;
          cursor: pointer;
          transition: all 0.3s ease-out;

          &:hover {
            width: 35px;
            height: 35px;
            line-height: 35px;
          }
        }
      }

      .temp {
        display: inline-block;
        color: #FFF;
        font-size: 60px;
        font-weight: 900;
        text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
        padding: 10px 0;
      }

      .weather-type, .humidity, .wind, .sunrise, .sunset {
        color: #FFF;
        font-size: 20px;
        font-weight: 650;
        font-style: normal;
        text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
        padding: 1px 0;
      }
    }

    .error-msg {
      display: flex;
      padding: 30px;
      color: #FFF;
      font-size: 38px;
      font-weight: 500;
      text-align: center;
      text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
    }
  }
}

//Weather condition codes - according to openWeatherMap
#app.Thunderstorm {
  background-image: url('./assets/thunderstorm-bg.jpg');
}
#app.Drizzle {
  background-image: url('./assets/drizzle-bg.jpg');
}
#app.Rain {
   background-image: url('./assets/rain-bg.jpg');
 }
#app.Snow {
  background-image: url('./assets/snow-bg.jpg');
}
#app.Mist {
  background-image: url('./assets/mist-bg.jpg');
}
#app.Smoke {
  background-image: url('./assets/smoke-bg.jpg');
}
#app.Haze {
  background-image: url('./assets/haze-bg.jpg');
}
#app.Dust {
  background-image: url('./assets/dust-bg.jpg');
}
#app.Fog {
  background-image: url('./assets/fog-bg.jpg');
}
#app.Sand {
  background-image: url('./assets/sand-bg.jpg');
}
#app.Ash {
  background-image: url('./assets/ash-bg.jpg');
}
#app.Squall {
  background-image: url('./assets/squall-bg.jpg');
}
#app.Tornado {
  background-image: url('./assets/tornado-bg.jpg');
}
#app.Clear {
  background-image: url('./assets/sunny-bg.jpg');
}
#app.Clouds {
  background-image: url('./assets/clouds-bg.jpg');
}


</style>