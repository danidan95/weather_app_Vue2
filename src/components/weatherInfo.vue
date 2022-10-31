<template>
  <div class="weather-container">
      <div class="search-box">
        <input
            class="search-bar"
            placeholder="Search..."
            type="text"
        >
      </div>

      <div class="weather-wrap">
        <div class="location-box">
          <div class="location"> Alba Iulia, RO </div>
          <div class="date"> Thursday 20 September 2022 </div>
        </div>

        <div class="weather-box">
          <div class="temperature"> 9 ° {{unitMeasure}} </div>
          <div class="weather"> Cold </div>
        </div>
      </div>
  </div>
</template>

<script>
export default {
  name: 'weatherInfo',
  props: {
    msg: String,
    unitMeasure: {
      type: String,
      default: 'Celsius'
    },
    query: {
      type: String,
    },
  }
}
</script>

<style>
.weather-container {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
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
  border-radius: 0 16px 0 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0 16px 0;
}

.location-box .location {
  color: #FFF;
  font-size: 35px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temperature {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 100px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 50px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

</style>
