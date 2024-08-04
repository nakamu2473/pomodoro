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
  // ファイル名
    const NAMES = {
        start: "pomodolo_start",
        stop: "pomodolo_stop",
        min1: "1",
        min2: "2",
        min3: "3",
        break_start: "break_start",
        break_stop: "break_stop",
    };

    // 音声再生構造体
    const Audios = {
        instance: null,
        constructor() {
            this.instance = new Audio();
            return this;
        },
        setPath(str) {
            this.instance.src = `/assets/${str}.mp3`;
            return this;
        },
        play() {
            if (this.instance !== undefined) {
                this.instance.play();
            }
        }
    };
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
                minToSec: 60, // デバッグ中は1にすると楽
                audio: null,
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
        mounted() {
            this.audio = Audios.constructor();
        },
        methods: {
            startTimer() {
                // ポモドーロタイマー開始
                console.log("this.pomodoroCount",this.pomodoroCount);
                if(this.pomodoroCount == 1 && this.toFocus) {
                    Audios.setPath(NAMES.start).play();
                } else {
                    if( this.toFocus ){
                        // 集中終了
                        Audios.setPath(NAMES.break_stop).play();
                    } else {
                        // 休憩終了
                        Audios.setPath(NAMES.break_start).play();
                    }
                }
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
                    console.log("this.pomodoroCount",this.pomodoroCount);
                    this.pomodoroCount++;
                }
                if (this.toFocus && this.running && this.inputPomodoroCount < this.pomodoroCount) {
                    // ポモドーロタイマー停止
                    this.running = false;
                    this.resetTimer()
                    Audios.setPath(NAMES.stop).play();
                } else {
                    this.startTimer();
                }
            },
            resetTimer() {
                // リセット
                this.time = this.focusTime * this.minToSec;
                this.pomodoroCount = 1;
                this.inputFocusTime = null;
                this.inputIsBreak = null;
                this.inputPomodoroCount = null;
                this.running = false;
                clearInterval(this.interval);
            },
        },
        beforeDestroy() {
            this.resetTimer();
        }
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
  