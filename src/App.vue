<template>
  <div class="w_container">
    <div v-for="(day, i) in weather" :key="i">
      <span>{{day.weather[0].description}}</span><br>
    </div>
  </div>
  
</template>

<script>
const API_KEY = "40fdd308ac2317c9f38271955e6f8e0d";

export default {
  name: 'App',
  data() {
    return {
     weather: "",
    }
  },

  components: {
    
  },
  methods: {
    geoGood(position) {
      const lat = position.coords.latitude; //위도 설정
      const lon = position.coords.longitude; //경도 설정
      let url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=current,minutely,hourly,alerts&appid=${API_KEY}&units=metric`;
      //37.580725449950116, 127.21733011159394
      fetch(url)
          .then(response => response.json())//여기도 추가공부...
          .then(data => {
            this.weather = data.daily;
 
            console.log(url);
          })
    },
    geoError() {
      alert("Where are you?");
    }
  },
  mounted() {
    navigator.geolocation.getCurrentPosition(this.geoGood, this.geoError);
    },
}
</script>

<style>
body {
  width: 100%;
}
</style>
