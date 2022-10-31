<template>
  <div id="app">
    <weatherInfo
        :unitMeasure="unitMeasure"
        :query="query"
    >
    </weatherInfo>
  </div>


</template>

<script>
import weatherInfo from './components/weatherInfo.vue'

export default {
  name: 'app',
  components: {
    weatherInfo,
  },
  data() {
    return {
        api_key: "7f2cdcf51d784afe4f0c0a79c86d736e",
        url_base: "https://api.openweathermap.org/data/2.5/",
        query: '',
        weather: {},
        unitMeasure: '',
    }
  }
}
</script>

<style>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/hot-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition:0.4s;
}

</style>
