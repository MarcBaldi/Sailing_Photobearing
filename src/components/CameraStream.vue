<template>
  <div class="camera-container">
    <div class="video-container">
      <video id="video" ref="video" v-on:canplay="startStream" v-on:click="takeSnapshot">Your browser does not support the video tag.</video>
    </div>
    <div class="canvas-container" v-show="showCanvas">
      <canvas id="canvas" ref="canvas" v-on:click="hideSnapshot"></canvas>
    </div>
  </div>
</template>

// This component streams the environment camera of the device as a background on the screen

<script>
export default {
  name: 'CameraStream',
  data () {
    return {
      // width: screen.height < screen.width ? screen.availWidth : screen.availHeight,
      // height: screen.height > screen.width ? screen.availWidth : screen.availHeight,
      width: screen.availWidth,
      height: screen.availHeight,
      streaming: false,
      video: null,
      canvas: null,
      showCanvas: false
    }
  },
  mounted () {
    this.video = document.getElementById('video')
    this.canvas = document.getElementById('canvas')
    this.initCamera()
    this.initCanvas()
  },
  methods: {
    initCamera () {
      navigator.mediaDevices.getUserMedia({
        audio: false,
        video: {
          width: { ideal: this.width },
          height: { ideal: this.height },
          facingMode: { ideal: 'environment' }
        }
      })
        .then(function (stream) {
          this.video.srcObject = stream
          this.video.play()
        }.bind(this))
        .catch(function (err) {
          if (err === 'NotAllowedError') {
            console.log('Maybe not in secure context(HTTPS)? Error: ' + err)
          } else {
            console.log('An error occurred: ' + err)
          }
        })
    },
    initCanvas () {
      this.canvas.display = 'none'
      this.canvas.width = screen.availWidth
      this.canvas.height = screen.availHeight
    },
    startStream () {
      if (!this.streaming) {
        this.video.setAttribute('width', this.width)
        this.video.setAttribute('height', this.height)
        this.streaming = true
      }
    },
    takeSnapshot () {
      this.canvas.getContext('2d').drawImage(this.video, 0, 0, this.canvas.width, this.canvas.height)
      this.showCanvas = true
    },
    hideSnapshot () {
      this.showCanvas = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.camera-container{
  position: absolute;
}
.video-container{
  position: absolute;
  z-index: 0;
  background-color: lightgray;
}
.canvas-container{
  position: absolute;
  z-index: 10;
  background-color: lightgray;
}
#video #canvas{
  height: 100vh;
  width: 100vw;
  object-fit: cover;
  overflow: hidden;
}
</style>
