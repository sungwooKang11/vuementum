<template>
  <div class="all"> 
    <div class="right">
      <div class="Rheader">
        <!--검색 폼-->
        <form class="searchForm" action="https://www.google.com/search" method="GET">
          <input class="search" name="q" placeholder="search">
        </form>
      </div>
      <div class="Rmiddle"> 
        <!-- 날씨 배경 -->
        <img class="img" :src="require(`./images/${img}.jpg`)">
        <div class="clockDiv">
          <!-- 토글스위치 -->
          <div class="switch-component-wrapper">
            <div class="class switch-wrapper" @click="switchOnOff" :class="{'on' : onOff, 'off': !onOff }">
              <div class="circle">
              </div>
            </div>
          </div>
          <!-- 시계 -->
          <div class="clock">
            <h1> {{afbf}} {{hours}} : {{minutes}}</h1>
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
        <!-- 명언 -->
        <p> {{quote}} </p>
        <p> {{author}} </p>
      </div>
      <div class="Rfooter">  
        <div class="upper">
          <!-- 오늘 날씨 -->
          <div class="dailyWeather">
            <span class="day">{{day}}</span> <br>
            <span class="city">{{city}}</span>
          </div>
        </div>
        <div class="down">
          <!--일주일 날씨-->
          <div class="weeklyWeather" v-for="(day,i) in weather" :key="i">
            <span>{{day.weather[0].date}}</span>
            <span>{{day.weather[0].main}}</span><br>
            <span>{{(((day.temp.min) + (day.temp.max)) /2).toFixed(1)}}</span>
          </div>
        </div>
      </div>
    </div>
      
    <div class="left">
      <div class="Lheader">
        <!-- 북마크  폼-->
        <form class="bmForm" @submit="bookmarkAdd">
          <input class="linkValue" v-model="linkValue" required placeholder="Link">
          <input class="explainValue" v-model="explainValue" required placeholder="Link name">
          <input class="submitBtn" type="submit" placeholder="submit">
        </form>
        <!-- 북마크 -->
        <div class="bmListDiv1">
            <div class="bmListDiv2" v-for="bookmark in bookMarks" :key="bookmark.id" :id="bookmark.id">
              <span class="bmExplain">{{bookmark.explain}}</span>
              <a class="link" v-bind:href="bookmark.link">{{bookmark.link}}</a>
              <button class="deleteBtn" @click="bookmarkDelete">delete</button> 
            </div>
        </div>
      </div>
      <div class="Lmiddle">
        <!-- todo 폼 -->
        <form @submit="todoAdd">
        <input v-model="todoText" class="todo" required type="text" placeholder="Write To Do"/>
        </form>
        <!-- todo -->
        <div class="todoDiv">
          <ul>
            <li v-for="todo in todos" :key="todo.id" :id="todo.id" >
              <span> {{todo.text}} </span> 
              <button @click="todoDelete">delete</button> 
            </li>
          </ul>
        </div>
      </div>
      <div class="Lfooter">
        <!--메모-->
        <h1 @click="NoteTextHide" v-bind:class="{ hide : NoteTextHidden}">노트</h1>
        <div v-bind:class="{ hide : NoteHidden}" class="note">
          <h6 @click="NoteHide" class="shutdown">닫기</h6>
          <textarea v-model="noteValue" class="noteinput">
        </textarea>
        </div>
      </div>
    </div>
  </div> 







</template>

<script>
const API_KEY = "40fdd308ac2317c9f38271955e6f8e0d";
import quoteData from "./data/quoteData";
//명언 데이터
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
    img: "nothing", //이미지 소스
    NoteTextHidden:false, //노트 버튼 초기 보여줌
    NoteHidden:true, //노트 초기 숨겨짐
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
      this.saveTodos();
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
     //오늘 날씨 불러와주는 함수
    todaysWeather(position){
    const lat = position.coords.latitude;
    const lon = position.coords.longitude;
     
    let url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=current,minutely,hourly,alerts&appid=${API_KEY}&units=metric`;
  
    fetch(url)
    .then((response) => response.json())
    .then((data) => {
       this.weather = data.daily;
    }); 
    }, 
        //일주일 날씨 %온도 가져오는 함수
    weekWeather(position){
    const lat = position.coords.latitude;
    const log = position.coords.longitude;
     
    const url = `http://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${log}&appid=${API_KEY}`;
    fetch(url)
    .then((response) => response.json())
    .then((data) => {
       
       this.day = data.weather[0].main;
       this.city = data.name;
       this.img = data.weather[0].main;
      
    }); 
    }, 
    geoError() {
      alert("Where are you?");
    },
        //노트 버튼 클릭시 노트를 보여주는 함수
    NoteTextHide() {
       this.NoteTextHidden = true;
       this.NoteHidden = false;
    },
        //닫기 버튼 클릭시 노트 버튼을 보여주는 함수
    NoteHide() {
      this.NoteTextHidden = false;
      this.NoteHidden = true;
      localStorage.setItem('note', this.noteValue);
    
    },
        //스토레지에서 미리 작성된 노트의 내용을 가져와주는 함수
    getNote() {
      this.noteValue = localStorage.getItem('note');
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
  width: 640px;
  height: 420px;
}
.todoDiv::-webkit-scrollbar {
  display: none;
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
  position: relative;
  bottom: 450px;
  display: flex;
  justify-content: center;
}

.clock {
  position: relative;
  
  z-index: 2;
  color: rgba(255, 255, 255, 0.781);
}

.switch-component-wrapper {
  position: relative;
  height: 26px;
  left: 3px;
  z-index: 2;
}
.switch-wrapper {
   width: 44px;
   min-height: 22px;
   display: flex;
   cursor: pointer;
   border-radius: 22px;
   align-items: center;
  padding: 2px;
  transition: all 0.5s;
  background: green;
}
.on {
  background: green;
  justify-content: flex-end;
}
.off {
  background: gray;
  justify-content: flex-start;
}
.circle {
  background: #fff;
  width: 18px;
  height: 18px;
  border-radius: 18px;
}

.bmForm {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.linkValue {
  width: 275px;
  height: 58px;
  border-top-left-radius: 10px;
  border-bottom-left-radius: 10px;
  border: none;
  background-color: rgba(196,196,196, 0.5);
  padding-left: 10px;
}

.linkValue:focus {
  outline: none;
}

.linkValue::placeholder {
  color:rgb(78, 78, 78);
  font-size: 20px;
}

.explainValue {
  width: 275px;
  height: 58px;
  background-color: rgba(196,196,196, 0.5);
  border: none;
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

.bmListDiv1 {
  margin-top: 10px;
}

.bmListDiv2{
  display: flex;
  justify-content: space-between;
  margin-top: 5px;
}

.bmExplain {
  margin-left: 10px;
  border: 0.5px solid #c4c4c4;
  border-radius: 5px;
}

.link {
  border-radius: 5px;
}

.deleteBtn {
  margin-right: 10px;
  border: none;
  border-radius: 5px;
}










.note {
  width: 640px;
  height: 260px;
  border:1px solid black;
  margin:0;
  border-radius: 14px;
}
.noteinput {
  width: 100%;
  border:none;
  padding: 0;
  height: 90%;
  overflow-y:scroll;
}
.noteinput::-webkit-scrollbar {
  width:10px;
}
.noteinput::-webkit-scrollbar-thumb {
  width:10px;
  background-color: skyblue;
  border-radius: 10px;
 
}
.noteinput::-webkit-scrollbar-track {
  width:10px;
  background-color: blanchedalmond;
  border-radius: 10px;
  box-shadow: inset 0px 0px 5px white;
 
}
.noteinput:focus {
  outline: none; 
  
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

.loginDiv {
  display: flex;
  justify-content: center;
}

.loginForm {
  position: relative;
  bottom: 465px;
  z-index: 2;
}

.loginInput {
  border: none;
  border-radius: 15px;
  width: 300px;
  height: 60px;
  font-size: 25px;
  background-color: rgba(255, 255, 255, 0.781);
}

.loginInput:focus {
  outline: none;
}

.loginInput::placeholder {
  font-size: 25px;
  text-align: center;
}

#greeting {
  position: relative;
  bottom: 470px;
  z-index: 2;
  color: rgba(255, 255, 255, 0.781);
}
/* 성우 코드 */
body {
  width: 100%;
}

.all {
  display: flex;
}

.right {
  display: grid;
  grid-template-columns: 20px 1195px 20px ;
  grid-template-rows: 20px 95px 20px 490px 20px 380px 20px;
  background-color: #5bd68eaf;
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
  grid-template-columns: 635px 20px;
  grid-template-rows: 20px 220px 20px 490px 20px 260px 20px;
  background-color: #5bd68eaf;
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

.weeklyWeatherCon {
  width: 800px;
  height: 372px;
  border: 1px solid black;
  border-radius: 30px;
  background-color: #5bd68e4d;
}

.weeklyWeather {
  display: flex;
  flex-direction: column;
  padding: 5px;
  margin-left: 40px;
  justify-content: center;
}

.upper {
  display: flex;
  width: 800px;
  height: 186px;
}

.down {
    width: 800px;
    height: 186px;
    border-top: 1px solid black;
    display: flex;
}

.dailyWeather {
  width: 50px;
  padding-top: 10px;
  padding-left: 10px;
  display: flex;
  flex-direction: column-reverse;
  justify-content: center;


}

.searchForm {
  height: 100%;
  display: flex;
  justify-content: right;
  margin-top: 16px;
  
  
}

.search {
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