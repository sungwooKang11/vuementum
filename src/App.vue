<template>
  <div class="w_container">
    <span>{{weather}}</span><br>
    <span>{{temper}}</span><br>
  </div>
  
</template>

<script>
const API_KEY = "40fdd308ac2317c9f38271955e6f8e0d";

export default {
  name: 'App',
  data() {
    return {
     weather: "",

     temper: "",

    }
  },
  components: {
    
  },
  methods: {
    geoGood(position) {
      const lat = position.coords.latitude; //위도 설정
      const lon = position.coords.longitude; //경도 설정
      let url = `https://api.openweathermap.org/data/2.5/forecast/daily?lat=${lat}&lon=${lon}&cnt=7&appid=${API_KEY}&units=metric`;
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
