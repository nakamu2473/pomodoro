<script>
import { createApp } from 'vue';
import { RouterLink, RouterView } from 'vue-router'
import Timer from './components/Timer.vue'
export default {
  name: 'App',
  components: {
    Timer,
   }
}
</script>

<template>
  <div>ポモドーロタイマー</div>

  <div id="app">
    <Timer />
  </div>
</template>

<style>

</style>
