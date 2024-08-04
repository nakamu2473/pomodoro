<template>
    <div class="timer">
      <p v-if="this.toFocus">集中!</p>
      <p v-else>休憩!</p>
      <p>集中{{ this.focusTime }}分</p>
      <p>休憩{{ this.isBreak }}分</p>
      <p>{{this.pomodoroCount}}回目</p>

     <h1>{{ formattedTime }}</h1>
      <div>
        <input v-model="inputFocusTime" placeholder="集中する時間">
        <input v-model="inputIsBreak" placeholder="休憩する時間">
        <input v-model="inputPomodoroCount" placeholder="繰り返す回数">

      </div>
      <button @click="startTimer" :disabled="running">Start</button>
      <button @click="stopBtnTimer" :disabled="!running">Stop</button>
      <button @click="resetTimer">Reset</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        focusTime: 10,
        isBreak: 5,
        pomodoroCount: 1,
        inputFocusTime: "",
        inputIsBreak: "",
        inputPomodoroCount: null,
        time: 0,
        interval: null,
        running: false,
        toFocus: true,
        minToSec: 1 // デバッグ中は1にすると楽
      };
    },
    created() {
      this.time = this.focusTime * this.minToSec;
    },
    computed: {
      formattedTime() {
        const minutes = Math.floor(this.time / this.minToSec);
        const seconds = this.time % this.minToSec;
        return `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
      },
    },
    methods: {
      startTimer() {
        if (!this.running && this.time > 0) {
            this.running = true;
            this.interval = setInterval(() => {
                if (this.time > 0) {
                    this.time--;
                } else {
                    if( this.toFocus ){
                        this.time = this.isBreak;
                    } else {
                        this.time = this.focusTime;
                    }
                    this.toFocus = !this.toFocus;
                    this.pomodoroCompleted();
                }
            }, 1000);
        }
      },

      stopBtnTimer() {
        this.running = false;
        clearInterval(this.interval);
      },
      pomodoroCompleted() {
        if (this.toFocus && this.running) {
            // ポモドーロ1回分終了
            this.pomodoroCount++;
        }
        if (this.toFocus && this.running && this.inputPomodoroCount < this.pomodoroCount) {
            // ポモドーロ停止
            this.running = false;
            this.resetTimer()
        } else {
            // 集中終了
            this.startTimer();
        }
      },
      resetTimer() {
        // リセット
        this.time = this.focusTime;
        this.pomodoroCount = 1;
        this.inputFocusTime = null;
        this.inputIsBreak = null;
        this.inputPomodoroCount = null;
        this.running = false;
        clearInterval(this.interval);
      },
    },
    beforeDestroy() {
      this.pomodoroCompleted();
    },
  };
  </script>
  
  <style scoped>
  .timer {
    text-align: center;
  }
  button {
    margin: 5px;
  }
  </style>
  