<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="camera">
      <video id="video">Video stream not available.</video>
    </div>
    <canvas id="canvas">
    </canvas>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a {
  color: #42b983;
}
</style>
