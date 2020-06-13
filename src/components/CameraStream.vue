<template>
  <div class="camera-container">
    <video id="video" ref="video" v-on:canplay="startStream">Your browser does not support the video tag.</video>
  </div>
</template>

<script>
export default {
  name: 'CameraStream',
  data () {
    return {
      width: screen.availWidth,
      height: screen.availHeight,
      streaming: false,
      video: null
    }
  },
  methods: {
    startUp () {
      navigator.mediaDevices.getUserMedia({
        audio: false,
        video: {
          facingMode: 'environment'
        }
      })
        .then(function (stream) {
          this.video.srcObject = stream
          this.video.play()
        }.bind(this))
        .catch(function (err) {
          if (err === 'NotAllowedError') {
            console.log('Maybe not in secure context. Error: ' + err)
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
        // console.log('Stream: Width: ' + this.video.width + 'Height:' + this.video.height)
      }
    }
  },
  mounted () {
    this.video = document.getElementById('video')
    this.startUp()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.camera-container {
  background-color: lightgray;
}
#video {
}
</style>
