<template>
  <div class="w_container">
    <span>{{weather}}</span><br>
    <span>{{city}}</span><br>
    <span>{{temper}}</span>
  </div>
  
</template>

<script>
const API_KEY = "20d3ea9ebdb399b0edf375ee7d5085c0";

export default {
  name: 'App',
  data() {
    return {
     weather: "",
     city: "",
     temper: "",

    }
  },
  components: {
    
  },
  methods: {
    geoGood(position) {
      const lat = position.coords.latitude; //위도 설정
      const lon = position.coords.longitude; //경도 설정
      const url = `https://api.openweathermap.org/data/2.5/forecast/daily?lat=${lat}&lon=${lon}&cnt=7&appid=${API_KEY}&units=metric`;
                //16daily = api.openweathermap.org/data/2.5/forecast/daily?lat=${lat}&lon=${lon}&cnt=7&appid=${API_KEY}
                //one call = https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=daily&appid=${API key}
                //original = https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric
      fetch(url)
          .then(response => response.json())//여기도 추가공부...
          .then(data => {
                  this.weather = data.daily;
                  this.city = data.name;
                  this.temper = data.temp;
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
