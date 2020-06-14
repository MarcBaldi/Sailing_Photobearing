<template>
  <div id="app">
    <camera-stream/>
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
}
</style>
