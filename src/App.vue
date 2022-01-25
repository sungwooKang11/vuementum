<template>
  <div class="all">  
    <div class="right">
      <div class="Rheader"></div>
      <div class="Rmiddle"></div>
      <div class="Rfooter"></div>
    </div>
    <div class="left">
      <div class="Lheader"></div>
      <div class="Lmiddle"></div>
      <div class="Lfooter"></div>
    </div>
  </div>  
  <div class="w_container">
    <div class="upper">
      
    </div>
    <div class="down">
      <div class="dayWeather" v-for="(day, i) in weather" :key="i">
        <span>{{day.weather[0].date}}</span><br>
        <span>{{day.weather[0].main}}</span><br>
        <span>{{(((day.temp.min) + (day.temp.max)) /2).toFixed(1)}}°</span>
      </div>
    </div>
  </div>

  <form class="searchForm" action="https://www.google.com/search" method="GET">
    <input class="search" name="q" placeholder="search">
  </form>

  <form class="bookmarkForm" @submit="handleSubmit">
    <input class="linkInput" v-model="linkValue" required placeholder="Please write your link">
    <input class="explainInput" v-model="explainValue" required placeholder="Write your link name">
    <input class="submitBtn" type="submit">
  </form>
  <div class="bmListDiv1">
    <div class="bmListDiv2" v-for="bookmark in bookMarks" :key="bookmark.id" :id="bookmark.id">
      <span class="bmExplain">{{bookmark.explain}}</span><br>
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

.all {
  display: flex;
}

.right {
  display: grid;
  grid-template-columns: 20px 1200px 20px ;
  grid-template-rows: 20px 95px 20px 490px 20px 380px 20px;
  background-color: #5BD68F;
}


.Rheader {
  grid-column: 2/ 3;
  grid-row: 2/ 3;
  background-color: white;
  border-radius: 15px;
  
}

.Rmiddle {
  grid-column: 2/ 3;
  grid-row: 4/ 5;
  background-color: white;
  border-radius: 15px;
}

.Rfooter {
  grid-column: 2/ 3;
  grid-row: 6/ 7;
  background-color: white;
  border-radius: 15px;
}

.left {
  display: grid;
  grid-template-columns: 640px 20px;
  grid-template-rows: 20px 220px 20px 490px 20px 260px 20px;
  background-color: #5BD68F;
}

.Lheader {
  grid-column: 1/ 2;
  grid-row: 2/ 3;
  background-color: white;
  border-radius: 15px;
}

.Lmiddle {
  grid-column: 1/ 2;
  grid-row: 4/ 5;
  background-color: white;
  border-radius: 15px;
}

.Lfooter {
  grid-column: 1/ 2;
  grid-row: 6/ 7;
  background-color: white;
  border-radius: 15px;
}

.w_container {
  width: 800px;
  height: 372px;
  border: 1px solid black;
  border-radius: 30px;
  background-color: #5bd68e4d;
  display: none;
}

.upper {
  width: 800px;
  height: 186px;
}

.down {
    width: 800px;
    height: 186px;
    display: flex;
}

.dayWeather {
  padding: 5px;
  width: 100%;
}

.searchForm {
  display: none;
}

.bookmarkForm {
  display: none;
  flex-direction: column;
}

.linkInput {
  width: 200px;
}

.explainInput {
    width: 200px;
}

.submitBtn {
  width: 50px;
}
</style>

