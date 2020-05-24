<template>
  <div id="app">
    <img id="vue-logo-example" alt="Vue logo" src="./assets/logo.png">
    <CameraStream/>
    <absolute-heading/>
  </div>
</template>

<script>
import CameraStream from './components/CameraStream.vue'
import AbsoluteHeading from './components/AbsoluteHeading'

export default {
  name: 'App',
  components: {
    CameraStream,
    AbsoluteHeading
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
#vue-logo-example {
  z-index: 3;
}
</style>
