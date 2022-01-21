<template>
  <div class="w_container">
    <div class="dayWeather" v-for="(day, i) in weather" :key="i">
      <span>{{day.weather[0].date}}</span><br>
      <span>{{day.weather[0].main}}</span><br>
      <span>{{(((day.temp.min) + (day.temp.max)) /2).toFixed(1)}}</span>
    </div>
  </div>

  <form class="searchForm" action="https://www.google.com/search" method="GET">
    <input class="search" name="q" placeholder="search">
  </form>

  <form class="bookmarkForm" @submit="handleSubmit">
    <input class="link" v-model="linkValue" required placeholder="Please write your link">
    <input class="explain" v-model="explainValue" required placeholder="Write your link name">
    <input class="submitBtn" type="submit">
  </form>
  <div class="bmListDiv1">
    <div class="bmListDiv2" v-for="bookmark in bookMarks" :key="bookmark.id" :id="bookmark.id">
      <span class="bmExplain">{{bookmark.explain}}</span>
      <a class="link" v-bind:href="bookmark.link">{{bookmark.link}}</a>
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
     linkValue: "",
     explainValue: "",
     bookMarks: [],
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
    },
    handleSubmit(a) {
      a.preventDefault();
      const bookmark = {
        link: this.linkValue,
        explain: this.explainValue,
        id: Date.now(),
      }
      this.bookMarks.push(bookmark);//북마크 생성
      console.log(this.bookMarks);
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

.w_container {
  display: flex;
  flex-direction: row;
  border: 1px solid black;
  width: 415px;
}

.dayWeather {
  padding: 5px;
}
</style>

