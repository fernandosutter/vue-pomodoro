<template>
  <div class="main">
    <h1 class="title">Pomodoro</h1>
    <p class="state" v-if="WorkOrBreak">{{WorkOrBreak}}</p>
    <p class="timerOnScreen">{{timerScreen}}</p>
    <div>
      <button class="startButton" @click="startPomodoro">Start Timer</button>
      <button class="stopButton" @click="stopTimer">Stop Timer</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const workTimeinMinutes = 25;
const breakTimeinMinutes = 5;

let timer = null;
let alreadyStarted = false;
let timerScreen = ref('00:00');
let WorkOrBreak = ref('');

const startPomodoro = async () => {
  WorkOrBreak.value = 'Work Time';
  await startTimer(workTimeinMinutes);
  WorkOrBreak.value = 'Break Time';
  await startTimer(breakTimeinMinutes);
  finishPomodoro();
}

const startTimer = (duration) => {
  if (alreadyStarted) {
    return;
  }
  let time = duration * 60;

  return new Promise((resolve, reject) => {
    timer = setInterval(() => {
      alreadyStarted = true;

      if (time <= 0) {
        clearInterval(timer);
        alreadyStarted = false;
        resolve();
        return;
      }

      time--;

      const minutes = formatTime(Math.floor(time / 60));
      const seconds = formatTime(time % 60);

      timerScreen.value = `${minutes}:${seconds}`;
    }, 1000);
  });
}

const stopTimer = () => {
  clearInterval(timer);
  timerScreen.value = '00:00';
  WorkOrBreak.value = '';
  alreadyStarted = false;
}

const finishPomodoro = () => {
  WorkOrBreak.value = '';
  timerScreen.value = '00:00';
}

const formatTime = (time) => {
  return time.toString().padStart(2, '0');
}

</script>

<style scoped>

.main{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.title, .timerOnScreen {
  font-size: 4rem;
  text-align: center;
  margin-bottom: 2rem;
  color: white;
}

.state {
  font-size: 2rem;
  text-align: center;
  margin-bottom: 2rem;
  color: white;
}

.startButton, .stopButton {
  transition-duration: 0.4s;
  cursor: pointer;  
  font-size: 1.75rem;
  text-align: center;
  margin: 0.75rem;
  padding: 0.75rem;
  border-radius: 0.5rem;
  border: none;
  color: white;
}

.startButton {
  background-color: #4CAF50;
}

.stopButton {
  background-color: #b01818;
}
</style>