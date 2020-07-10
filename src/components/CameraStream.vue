<template>
  <div class="camera-container">
    <video id="video" ref="video" v-on:canplay="startStream">Your browser does not support the video tag.</video>
  </div>
</template>

// This component streams the environment camera of the device as a background on the screen

<script>
export default {
  name: 'CameraStream',
  data () {
    return {
      width: screen.height < screen.width ? screen.availWidth : screen.availHeight,
      height: screen.height > screen.width ? screen.availWidth : screen.availHeight,
      streaming: false,
      video: null
    }
  },
  mounted () {
    this.video = document.getElementById('video')
    this.initCamera()
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
    startStream () {
      if (!this.streaming) {
        this.video.setAttribute('width', this.width)
        this.video.setAttribute('height', this.height)
        this.streaming = true
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.camera-container {
  position: absolute;
  z-index: 0;
  background-color: lightgray;
}
#video {
  height: 100vh;
  width: 100vw;
  object-fit: cover;
  overflow: hidden;
}
</style>
