<template>
  <div id="app">
    <!-- <router-link to="/">Go to Home</router-link><br> -->
    <!-- <router-link to="/about">Go to About</router-link> -->
    <router-view></router-view>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: arial,sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #222222;
  margin-top: 40px;
  padding: 0 20vw 0;
}
</style>
