<template>

  <div class="all"> 
    <div class="left">
      <div class="Lheader">
        <a class="logoContainer">
          <img class="logo" src="./image/VuementumLogo.jpg">
        </a>  
        <!--검색 폼-->
        <form class="searchForm" action="https://www.google.com/search" method="GET">
          <input class="search" name="q" placeholder="search">
        </form>
      </div>
      <div class="Lmiddle"> 
        <div class="clockDiv" @click="switchOnOff">
          <!-- 시계 -->
          <div class="clock">
            <h1 class="clockNum"> {{afbf}} {{hours}} : {{minutes}}</h1>
          </div>
        </div>
        <div class="loginDiv">
          <!-- 로그인폼 -->
          <form class="loginForm" @submit="Login" v-bind:class="{ hide : LoginFormHidden}">
            <input 
            class="loginInput"
            required
            maxlength="15"
            type="text"
            placeholder="What is your name?"
            v-model="id"
            />
          </form>
          <!-- 로그인 인사-->
          <h1 id="greeting" v-bind:class="{ hide : GreetingHidden}"> Hi! {{id}} </h1>
        </div>
      </div>
      <div class="Lfooter">  
        <div class="upper">
          <!-- 오늘 날씨 -->
          <div class="dailyWeather">
            <span class="day">{{day}}</span><br>
            <div class="locate">
              <span class="city">{{city}}</span><i class="fas fa-location-arrow"></i>
              
            </div>
            <!-- <span >{{(((weather2[0].temp.min) + (weather2[0].temp.max)) /2).toFixed(1)}}</span> -->
          </div>
        </div>
        <div class="line"></div>
        <div class="down">
          <!--일주일 날씨-->
          <div class="weeklyWeather" v-for="(day,i) in weather" :key="i">
            <span>{{day.weather[0].date}}</span>
            <span>{{day.weather[0].main}}</span><br>
            <span>{{(((day.temp.min) + (day.temp.max)) /2).toFixed(1)}}</span>
          </div>
        </div>
      </div>
      <div class="Lfooter2">
                <!-- 날씨 배경 -->
        <img class="img" :src="require(`./images/${img}.jpg`)">
        <!-- 명언 -->
        <div class="quoteDiv">
          <p class="quote"> {{quote}} </p>
          <p class="author"> {{author}} </p>
        </div>
      </div>
    </div>
      
    <div class="right">
      <div class="Rheader">
        <!-- 북마크  폼-->
        <form class="bmForm" @submit="bookmarkAdd">
          <input class="linkValue" v-model="linkValue" required placeholder="Link">
          <input class="explainValue" v-model="explainValue" required placeholder="Link name">
          <input class="submitBtn" type="submit" placeholder="submit">
        </form>
        <!-- 북마크 -->
        <div class="bmListDiv0">
          <div class="bmListDiv1">
            <div class="bmListDiv2" v-for="bookmark in bookMarks" :key="bookmark.id" :id="bookmark.id">
              <a class="link" v-bind:href="bookmark.link">{{bookmark.explain}}</a>
              <button class="deleteBtn" @click="bookmarkDelete">delete</button> 
            </div>
          </div>
        </div>
      </div>
      <div class="Rmiddle">
        <!-- todo 폼 -->
        <form @submit="todoAdd">
        <input v-model="todoText" class="todo" required type="text" placeholder="Write To Do"/>
        </form>
        <!-- todo -->
        <div class="todoDiv">
          <ul class="todoUL">
            <li class="todoLi" v-for="todo in todos" :key="todo.id" :id="todo.id" >
              <span class="todoContent"> {{todo.text}} </span> 
              <button class="todoBtn" @click="todoDelete">delete</button> 
            </li>
          </ul>
        </div>
      </div>
      <div class="Rfooter">
        <!--메모-->
        <div class="note">
          <textarea v-model="noteValue" class="noteinput">
        </textarea>
        <button class="memoSave" @click="saveNote">save</button>
        </div>
      </div>
    </div>
  </div> 

</template>

<script>

const API_KEY = "40fdd308ac2317c9f38271955e6f8e0d";
import quoteData from "./data/quoteData"; //명언 데이터
export default {
  name: 'App',
  data() {
    return {
    afbf:"", //오전 오후 나타냄
    hours:"00", //시간
    minutes:"00", //분
    onOff: false, //토글 스위치 onOff
    id:"", //사용자 이름
    savedUsername:"", //스토레지에 저장되는 유저 이름
    GreetingHidden:true, //로그인 인사 메시지 초기 숨겨짐
    LoginFormHidden:false, //로그인 폼 초기 보여줌
    quote:"", //명언 내용
    author:"", //명언 저자
    todos: [], //투두들이 담긴 어레이
    todoText:"", //투두 내용
    day:"", //오늘 날씨
    city: "", // 사용자의 위치
    weather:"", //일주일 날씨 데이터를 담는 부분
    weather2:"",
    img: "nothing", //이미지 소스
    noteValue:"", //노트에 쓴 내용
    linkValue:"", //북마크 링크
    explainValue: "", //북마크 설명
    bookMarks:[], //북마크들이 담긴 어레이
    }
  },

  components: {
    
  },
  methods: {
      //시계 호출 함수
    getClock(){
      const date = new Date(); 
      const ampm = date.getHours();
      if(this.onOff === false) { //토글 시계가 꺼져있으면 12시간 기준
        if(ampm >= 13) {
          this.afbf = "P.M."
          this.hours = String(date.getHours() - 12).padStart(2,"0")
          this.minutes = String(date.getMinutes()).padStart(2, "0");
        } else if (ampm == 12) {
          this.afbf = "P.M.";
          this.hours = String(date.getHours()).padStart(2,"0")
          this.minutes = String(date.getMinutes()).padStart(2, "0");
        } else {
          this.afbf = "A.M.";
          this.hours = String(date.getHours()).padStart(2,"0")
          this.minutes = String(date.getMinutes()).padStart(2, "0");
        } 
      } else {      //토글 시계가 켜져있으면 24시간 기준
          this.afbf = "P.M."
          this.hours = String(date.getHours()).padStart(2,"0")
          this.minutes = String(date.getMinutes()).padStart(2, "0");
          }
        },
    //로그인
    Login(a) {
      a.preventDefault();
      this.GreetingHidden = false;
      this.LoginFormHidden = true;
      localStorage.setItem("username", this.id);
    },
    //스토레지에 저장된 유저이름을 불러오는 함수
    getSavedUserName() {
      this.savedUsername = localStorage.getItem("username");
    },
    //랜덤 명언 설정
    getQuote() {
      const todaysQuote = quoteData[Math.floor(Math.random() * quoteData.length)];
      this.quote = todaysQuote.quote;
      this.author = todaysQuote.Author;
    },
        //투두 추가
     todoAdd(e){
       e.preventDefault();
       const todo = {
         text:this.todoText,
         id:Date.now(),
       }
      this.todos.push(todo);
      this.todoText = ""; 
      this.todoSave();
     },
          //투두 삭제
     todoDelete(e) {
       const li = e.target.parentElement;
       this.todos= this.todos.filter((todo) => todo.id !== parseInt(li.id));
       this.todoSave();
     },
     //투두 저장
     todoSave() {
       localStorage.setItem('todo', JSON.stringify(this.todos));
     },
          //스토레지에 저장된 투두들 가져와서 보여주는 함수
     getSavedTodo() {
       const savedToDos = localStorage.getItem("todo");
       if(savedToDos) {
         const parsedTodos = JSON.parse(savedToDos);
         this.todos = parsedTodos;
       }
     },
          //토글 스위치 on off 함수
     switchOnOff() {
     this.onOff = !this.onOff;
     },
     //일주일 날씨 불러와주는 함수
    todaysWeather(position){
    const lat = position.coords.latitude;
    const lon = position.coords.longitude;
     
    let url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=current,minutely,hourly,alerts&appid=${API_KEY}&units=metric`;
  
    fetch(url)
    .then((response) => response.json())
    .then((data) => {
       this.weather = data.daily;
       this.weather2 = data.daily;
       console.log(this.weather);
    }); 
    }, 
        //하루 날씨 %온도 가져오는 함수
    weekWeather(position){
    const lat = position.coords.latitude;
    const log = position.coords.longitude;
     
    const url = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${log}&appid=${API_KEY}`;
    fetch(url)
    .then((response) => response.json())
    .then((data) => {
       
       this.day = data.weather[0].main;
       this.city = data.name;
       this.img = data.weather[0].main;
      console.log(this.city);
    }); 
    }, 
    geoError() {
      alert("Where are you?");
    },
        //스토레지에서 미리 작성된 노트의 내용을 가져와주는 함수
    getNote() {
      this.noteValue = localStorage.getItem('note');
    },
    saveNote() {
      if(this.noteValue !== null) {
        localStorage.setItem('note', this.noteValue) 
      } else {
        return;
      }
    },
    //북마크 추가
    bookmarkAdd(a) {
      a.preventDefault();
      const bookmark = {
        link:this.linkValue,
        explain:this.explainValue,
        id: Date.now(),
      }
      this.bookMarks.push(bookmark);
      this.linkValue = "";
      this.explainValue = "";
      this.bookmarkSave();
    },
    //북마크 삭제
    bookmarkDelete(e) {
      const div = e.target.parentElement;
      this.bookMarks= this.bookMarks.filter((bm) => bm.id !== parseInt(div.id));
      this.bookmarkSave();
    },
    //북마크 저장
    bookmarkSave() {
      localStorage.setItem('bookmark' ,JSON.stringify(this.bookMarks));
    },
    //스토레지에서 북마크를 가져오는 함수
    getSavedBookmark() {
      const savedBookmark = localStorage.getItem('bookmark');
      if(savedBookmark) {
        const parsedBookmark = JSON.parse(savedBookmark);
        this.bookMarks = parsedBookmark;
      }
    }
  },
  mounted() {
    //0.1초 간격으로 시계가 업데이트 됨
     setInterval(this.getClock,100);
    //스토레지에 저장된 유저이름 불러오는 함수 호출 
     this.getSavedUserName(); 
     //저장된 유저이름이 없다면 로그인 폼을 보여줌
     if( this.savedUsername === null ) {
       this.LoginFormHidden = false;
       this.GreetingHidden = true;
       this.id = "";
       //저장된 유저이름이 있다면 자동 로그인 해줌
     } else {
       this.GreetingHidden = false;
       this.LoginFormHidden  = true;
       this.id = this.savedUsername;
     }
     //랜덤 명언 함수를 호출
     this.getQuote();
     //스토레지에 저장된 투두 불러오는 함수 호출
     this.getSavedTodo();
     //자신의 위치를 받아와서 API를 호출하는 함수
     navigator.geolocation.getCurrentPosition(this.todaysWeather, this.weatherError);
     navigator.geolocation.getCurrentPosition(this.weekWeather, this.weatherError);
     this.getNote();
     this.getSavedBookmark();
    },
}

</script>

<style>
/*대현 코드*/
.hide {
  display: none;
}
.tododiv {
  display: flex;
  justify-content: center;
}

.todo {
  border-radius: 8px;
  width:605px;
  height: 58px;
  background-color: rgba(196,196,196, 0.5);
  border: none;
  padding-left: 10px;
  margin-top: 10px;
  font-size: 20px;
}
.todo::placeholder {
  color:rgb(78, 78, 78);
  font-size: 20px;
}
.todo:focus {
  outline: none;
}
.todoDiv {
  overflow: scroll;
  width: 635px;
  height: 370px;
  font-size: 20px;
  display: flex;
  justify-content: center;
}
.todoDiv::-webkit-scrollbar {
  display: none;
}

.todoUL {
  display: flex;
  flex-direction: column;
  padding: 0;
}

.todoLi {
  font-family: 'Asap', sans-serif;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 10px;
  background-color: #afe7c778;
  flex: 0 0 50px;
  width: 500px;
  padding-right: 10px;
  padding-left: 10px;
  border-radius: 10px;
}

.todoContent {
  margin-right: 15px;
}

.todoBtn {
  font-family: 'Asap', sans-serif;
  border: none;
  border-radius: 5px;
  width: 50px;
  height: 25px;
  background-color: #7fbb9985;
}

.font {
  font-size: 24px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.clockDiv {
  display: flex;
  justify-content: center;
  align-items: center;
}

.clock {
  font-family: 'Asap', sans-serif;
  font-size: 35px;
  color: #73dd9f;
}

.clockNum {
  margin: 0;
}

.bmForm {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.linkValue {
  width: 270px;
  height: 58px;
  border-radius: 10px;
  border: none;
  background-color: rgba(196,196,196, 0.5);
  padding-left: 10px;
  margin-right: 5px;
}

.linkValue:focus {
  outline: none;
}

.linkValue::placeholder {
  color:rgb(78, 78, 78);
  font-size: 20px;
}

.explainValue {
  width: 270px;
  height: 58px;
  background-color: rgba(196,196,196, 0.5);
  border-top-left-radius: 10px;
  border-bottom-left-radius: 10px;
  border: none;
  padding-left: 10px;
}

.explainValue::placeholder {
  color:rgb(78, 78, 78);
  font-size: 20px;
}

.explainValue:focus {
  outline: none;
}

.submitBtn {
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
  border: none;
  border-left: 1px solid #c4c4c4;
  background-color: rgba(196,196,196, 0.5);
}

.submitBtn::placeholder {
  color:rgb(78, 78, 78);
  font-size: 20px;
}

li {
  list-style: none;
}

.bmListDiv0 {
  display: flex;
  justify-content: center;
}

.bmListDiv1::-webkit-scrollbar {
   display: none;
}

.bmListDiv1 {
  overflow-x: hidden;
  overflow-y: scroll;
  display: flex;
  flex-direction: column;
  align-content: center;
  height: 125px;
}

.bmListDiv2{
  font-family: 'Asap', sans-serif;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #afe7c778;
  flex: 0 0 50px;
  width: 500px;
  margin-top: 10px;
  padding-right: 10px;
  padding-left: 10px;
  border-radius: 10px;
}

.bmExplain {
  margin-left: 10px;
  border: 0.5px solid #c4c4c4;
  border-radius: 5px;
}

.link {
  border-radius: 5px;
  text-decoration: none;
}

.deleteBtn {
    font-family: 'Asap', sans-serif;
  margin-right: 10px;
  border: none;
  border-radius: 5px;
  width: 50px;
  height: 25px;
  background-color: #7fbb9985;
}

.note {
  width: 620px;
  height: 245px;
  border:1px solid black;
  margin-top: 10px;
  padding-left: 10px;
  padding-right: 10px;
  border:none;  
}
.noteinput {
  font-family: 'Asap', sans-serif;
  width: 100%;
  height: 85%;
  border:none;
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
  padding: 0;
  padding-top: 5px;
  font-size: 15px;
  overflow-y:scroll;
  resize: none;
}
.noteinput::-webkit-scrollbar {
  width:10px;
}
.noteinput::-webkit-scrollbar-thumb {
  width:10px;
  background-color: #73dd9f;
  border-radius: 10px;
 
}
.noteinput::-webkit-scrollbar-track {
  width:10px;
  background-color: #73dd9f54;
  border-radius: 10px;
  box-shadow: inset 0px 0px 5px white;
 
}
.noteinput:focus {
  outline: none; 
}

.memoSave {
  font-family: 'Asap', sans-serif;
  border: none;
  border-radius: 5px;
  width: 50px;
  height: 25px;
  background-color: #7fbb9985;
}

.shutdown {
  margin: 0;
  display: flex;
  justify-content: center;
}

.img {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 15px;
  z-index: 1;
}

/* .loginDiv {

}

.loginForm {

} */

.loginInput {
  border: none;
  border-radius: 15px;
  width: 230px;
  height: 60px;
  font-family: 'Asap', sans-serif;
  font-size: 25px;
  text-align: center;
  background-color: rgba(255, 255, 255, 0.979);
}
/* rgba(255, 255, 255, 0.781); */
.loginInput:focus {
  outline: none;
}

.loginInput::placeholder {
  font-size: 25px;
  text-align: center;
}

#greeting {
  font-family: 'Asap', sans-serif;
  color: #73dd9f;
}
/* 성우 코드 */
body {
  margin: 0;
}

.all {
  display: flex;
  width: 100vw;
  height: 100vh;
  margin: 0;
}

.logoContainer {
  width: 412px;
  display: flex;
  align-items: center;
}

.logo {
  width: 412px;
  height: 67px;
}

.left {
  display: grid;
  grid-template-columns: 30px 745px 25px 430px 25px ;
  grid-template-rows: 20px 95px 20px 470px 20px 340px 20px;
  background-color: #5bd68eaf;
}

.Lheader {
  grid-column: 2/ 5;
  grid-row: 2/ 3;
  background-color: white;
  border-radius: 15px;
  display: flex;
  justify-content: space-between;
}

.Lmiddle {
  grid-column: 2/ 5;
  grid-row: 4/ 5;
  background-color: white;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.Lfooter {
  grid-column: 2/ 3;
  grid-row: 6/ 7;
  background-color: white;
  border-radius: 15px;
}
.Lfooter2 {
  grid-column: 4/ 5;
  grid-row: 6/ 7;
  background-color: white;
  border-radius: 15px;
}

.quoteDiv {
  position: relative;
  z-index: 1;
  bottom: 250px;
  
}

.quote {
  font-family: 'Asap', sans-serif;
  color: rgb(255, 255, 255);
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 0;
  padding-left: 10px;
  padding-right: 10px;
}

.author {
  font-family: 'Asap', sans-serif;
  color: #fff;
  margin-top: 15px;
}

.right {
  display: grid;
  grid-template-columns: 635px 30px;
  grid-template-rows: 20px 200px 20px 450px 20px 255px 20px;
  background-color: #5bd68eaf;
}

.Rheader {
  grid-column: 1/ 2;
  grid-row: 2/ 3;
  background-color: white;
  border-radius: 15px;
}

.Rmiddle {
  grid-column: 1/ 2;
  grid-row: 4/ 5;
  background-color: white;
  border-radius: 15px;
}

.Rfooter {
  grid-column: 1/ 2;
  grid-row: 6/ 7;
  background-color: white;
  border-radius: 15px;
}


.weeklyWeather {
  font-family: 'Asap', sans-serif;
  font-size: 20px;
  display: flex;
  flex-direction: column;
  padding: 10px;
  justify-content: center;
  align-content: center;
}

.upper {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  width: 745px;
  height: 50%;
}

.line {
  width: 725px;
  height: 1px;
  background-color: #c4c4c4;
  margin-left: 10px;
  margin-right: 10px;
}

.down {
  width: 745px;
  height: 50%;
  display: flex;
  justify-content: space-evenly;
}

.dailyWeather {
  font-family: 'Asap', sans-serif;
  width: 725px;
  margin-left: 20px;
  margin-right: 20px;
  display: flex;
  flex-direction: row-reverse;
  justify-content: space-between;
}

.day {
  font-size: 30px;
  margin-top: 70px;
}

.locate {
  display: flex;
}

.city {
  font-size: 20px;
  margin-right: 3px;
}

.searchForm {
  height: 100%;
  width: 725px;
  display: flex;
  justify-content: right;
  margin-top: 16px;
}

.search {
  font-family: 'Asap', sans-serif;
  width: 650px;
  height: 60px;
  font-size: 20px;
  border: none;
  border-radius: 10px;
  background-color: rgba(196,196,196, 0.5);
  margin-right: 70px;
}

.search:focus {
  outline: none;
}

.search::placeholder {
  font-size: 20px;
  padding-left: 15px;
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