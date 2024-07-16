<template>
    <div class="timer">
      <p v-if="this.toFocus">集中!</p>
      <p v-else>休憩!</p>
      <p>集中{{ this.focusTime }}分</p>
      <p>休憩{{ this.isBreak }}分</p>

     <h1>{{ formattedTime }}</h1>
      <div>
        <input >
        <input >
      </div>
      <button @click="startTimer" :disabled="running">Start</button>
      <button @click="stopTimer" :disabled="!running">Stop</button>
      <button @click="resetTimer">Reset</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        focusTime: 10,
        isBreak: 5,
        time: 0,
        interval: null,
        running: false,
        toFocus: true,
        minToSec: 60 // デバッグ中は1にすると楽
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
        console.log("startTimer")
        console.log(this.toFocus)
        console.log(this.running)
        if (!this.running && this.time > 0) {
            this.running = true;
            this.interval = setInterval(() => {
            if (this.time > 0) {
                console.log("startTimer_--")
                this.time--;
            } else {
                console.log("startTimer_stop")
                //this.time = this.isBreak * 60;
                if( this.toFocus ){
                    this.time = this.isBreak;
                } else {
                    this.time = this.focusTime;
                }
                this.toFocus = !this.toFocus;
                this.stopTimer();
            }
            }, 1000);
        }
      },
      stopTimer() {
        console.log("stopTimer")
        console.log(this.toFocus)
        if (this.toFocus) {
            if (this.running) {
                this.running = false;
                clearInterval(this.interval);
            }
        }
      },
      resetTimer() {
        console.log("resetTimer")
        console.log(this.toFocus)
        this.stopTimer();
        //this.time = this.focusTime * 60;
        this.time = this.focusTime;
      },
    },
    beforeDestroy() {
      this.stopTimer();
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
  