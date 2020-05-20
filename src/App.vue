<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <CameraStream/>
  </div>
</template>

<script>
import CameraStream from './components/CameraStream.vue'

export default {
  name: 'App',
  components: {
    CameraStream
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 10px;
}
img {
  z-index: 3;
}
</style>
