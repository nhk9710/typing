<script setup>
import {onMounted, ref} from 'vue'
import data from '../words/data.json'

let words = data.Korean;
//언어 체크
const chkLang = ref(false)

let newObj = [];

let plusTop = new Array(words.length);
for(let i = 0; i< plusTop.length; i++) {
  plusTop[i] = 0;
};
let wordContent = ''
onMounted(() => {
  wordContent = document.getElementById('wordContent');
})

//게임 시작
let gameCount = ref(false);
//게임 종료
let gameOver = ref(false);

let gameWord = ref('');

//단어 크기
const WORDWIDTH = 150;
const WORDHEIGHT = 30;

//단어 생성 옵션
const DRAWTIME = 1500;
const DOWNTIME = ref(500);

//목숨
let life = ref(3);
//점수
let score = ref(0);
//남은 단어 수
let wordCount = ref(words.length);
// words index
let idx = 0;
//난이도
let difficult = ref(1);

// words drawing
function drawWords() {
  let randomText = 0;
  let tmp = null;

  for(let i = 0; i< words.length; i++){
    randomText = Math.round(Math.random() * (words.length - 1));
    tmp = words[randomText];
    words[randomText] = words[i];
    words[i] = tmp;
  }

  let drawInterval = setInterval(function() {
    let leftWidth = Math.round(Math.random() * 1200);
    let wordDiv = document.createElement("div");
    wordDiv.style.width = WORDWIDTH + "px";
    wordDiv.style.height = WORDHEIGHT + "px";
    wordDiv.style.position = "absolute";
    wordDiv.style.textAlign = "center";
    wordDiv.style.color = "#927a5d";
    wordDiv.style.fontSize = "1.5em"
    wordDiv.innerHTML = words[idx++];
    wordContent.appendChild(wordDiv);
    if (leftWidth + WORDWIDTH >= wordContent.offsetWidth) {
      wordDiv.style.left = (leftWidth - WORDWIDTH) + "px";
    } else {
      wordDiv.style.left = leftWidth + "px";
    }
    newObj.push(wordDiv);
    wordCount.value -- ;
    if(life.value===0){
      clearInterval(drawInterval);
      gameOver.value = true;
    }
    if(newObj.length === words.length){
      clearInterval(drawInterval);
    }
  },DRAWTIME);
}

//words go down
function wordDown(){
  let downInterval =
  setInterval(() => {
    for(let i=0; i< words.length; i++){
      if(i < newObj.length){
        newObj[i].style.top = plusTop[i] + "px";
        if (plusTop[i] + WORDHEIGHT >= wordContent.offsetHeight) {
          if(wordContent.contains(newObj[i])){
            wordContent.removeChild(newObj[i]);
            life.value--;

            if(life.value ===0){
              clearInterval(downInterval)
              gameOver.value = true;
            }
            if(newObj.length === words.length){
              if(!wordContent.hasChildNodes()){
                clearInterval(downInterval)
                gameOver.value = true;
              }
            }
          }
        }
        plusTop[i] += 30;
        }
    }
  }, DOWNTIME.value);
}

function chkWords(e){
  if(e.keyCode === 13){
    for(let i=0; i< newObj.length; i++){
      if(gameWord.value === newObj[i].innerHTML){
        wordContent.removeChild(newObj[i]);
        score.value += 100;

        if(newObj.length === words.length){
          if(!wordContent.hasChildNodes()){
            gameOver.value = true;
          }
        }
      }
    }
    gameWord.value = '';
  }
}

function startGame(){
  gameCount.value = true;
  drawWords();
  wordDown();
}
function switchLanguage(){
  if(chkLang.value===false){
    words = data.English;
    chkLang.value = true;
  }else{
    words = data.Korean;
    chkLang.value = false
  }
}

//게임 재시작
function restartGame(){
  location.reload();
}
</script>

<template>
<div class="allBox">
  <div class="centerBox">
  <div class="infoBox">
    <div class="txtDomino">SCORE : {{ score }}</div>
    <div class="txtDomino">남은 단어 : {{ wordCount }}</div>
    <div class="txtDomino">LIFE : {{ life }}</div>
  </div>
  </div>

  <!-- 게임이 종료되었을때 화면 -->
  <div v-if="gameOver">
    <p>게임 종료!</p>
    <button @click="restartGame">재도전</button>
  </div>

  <div v-if="!gameOver" id="wordContent"></div>

  <div class="centerBox">
    <template v-if="!gameCount">
      <div style="display: flex; width: 90%; justify-content: space-around; margin: 0 auto">
        <div style="display: flex; align-items: center; width: 12%">
          <span style="margin-right: 5%" class="txtDomino">난이도 : {{ difficult }}</span>
          <button class="txtDomino diffBtn" style="margin-right: 3%">+</button>
          <button class="txtDomino diffBtn">-</button>
        </div>
      <button class="btnBox" @click="startGame">START</button>
      <button class="txtDomino diffBtn" @click="switchLanguage">{{ chkLang ? 'ENGLISH' : '한국어' }}</button>
      </div>
    </template>
    <template v-else>
      <input class="textBox" @keydown="(e) => chkWords(e)" type="text" v-model="gameWord">
    </template>
  </div>
</div>
</template>

<style scoped>
.allBox{
  width: 100vw;
  height: 100vh;
  background: #f8f0e8;
  align-items: center;
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
}
.centerBox{
  width: 100%;
  align-items: center;
}
.infoBox{
  display: flex;
  justify-content: space-between;
  margin: 0 auto;
  width: 90%;
}
.txtDomino{
  color: #927a5d;
  font-weight: bolder;
  font-size: 1.5em;
}
.btnBox{
  display: flex;
  width: 10%;
  height: 100%;
  border: 3px solid #927a5d;
  margin: 0 auto;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  font-weight: bolder;
  background: #d5c5b9;
  color: #927a5d;
  cursor: pointer;
  font-size: 1.2em;
}
.textBox{
  display: flex;
  border: none;
  width: 15%;
  height: 100%;
  color: #927a5d;
  font-weight: bolder;
  font-size: 1.5em;
  background: #d5c5b9;
  margin: 0 auto;
  border-radius: 10px;
}
.diffBtn{
  background: none;
  border: none;
  cursor: pointer;
}
.textBox:focus { outline: none}
#wordContent{ width: 100%; height: 75%; position: relative; border-top: 3px solid #927a5d; border-bottom: 3px solid #927a5d}
</style>
