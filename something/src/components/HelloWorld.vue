<script setup>
import {onMounted, ref} from 'vue'
import data from '../words/data.json'

const words = data.Korean;

let newObj = [];

let plusTop = new Array(words.length);
for(let i = 0; i< plusTop.length; i++) {
  plusTop[i] = 0;
};
let wordContent = ''
onMounted(() => {
  wordContent = document.getElementById('wordContent');
})

let gameCount = ref(false);
let gameOver = ref(false);

let gameWord = ref('');

//world style
const WORDWIDTH = 150;
const WORDHEIGHT = 30;

//draw
const DRAWTIME = 1500;
const DOWNTIME = 100;

//life
let life = ref(3);
//score
let score = ref(0);
// words index
let idx = 0;

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
    if(newObj.length === words.length){
      clearInterval(drawInterval);
    }
  },DRAWTIME);
}

//words go down
function wordDown(){
  setInterval(() => {
    for(let i=0; i< words.length; i++){
      if(i < newObj.length){
        newObj[i].style.top = plusTop[i] + "px";
        if (plusTop[i] + WORDHEIGHT >= wordContent.offsetHeight) {
          if(wordContent.contains(newObj[i])){
            wordContent.removeChild(newObj[i]);
            life.value--;

            if(life.value ===0){
              gameOver.value = true;

            }
            if(newObj.length === words.length){
              if(!wordContent.hasChildNodes()){
                gameOver.value = true;
              }
            }
          }
        }
        plusTop[i] += 30;
        }
    }
  }, DOWNTIME);
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
</script>

<template>
<div class="allBox">
  <div class="centerBox">
  <div class="infoBox">
    <div class="txtDomino">SCORE : {{ score }}</div>
    <div class="txtDomino">LIFE : {{ life }}</div>
  </div>
  </div>

  <div id="wordContent"></div>

  <div class="centerBox">
    <button v-if="!gameCount" class="btnBox" @click="startGame">START</button>
  <input v-else class="textBox" @keydown="(e) => chkWords(e)" type="text" v-model="gameWord">
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
.separator{
  width: 100%;
  height: 10%;
  background: #e8ded3;
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
.textBox:focus { outline: none}
#wordContent{ width: 100%; height: 75%; position: relative; border-top: 3px solid #927a5d; border-bottom: 3px solid #927a5d}
</style>
