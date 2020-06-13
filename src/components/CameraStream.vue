<template>
  <div class="camera">
    <video id="video" ref="video" v-on:canplay="startStream">Your browser does not support the video tag.</video>
  </div>
</template>

<script>
export default {
  name: 'CameraStream',
  data () {
    return {
      width: 412, // Samsung Galaxy s10+
      height: 869, // Samsung Galaxy s10+
      streaming: false,
      video: null
    }
  },
  methods: {
    startUp () {
      // TODO: set width and height dynamically

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
        /* this.height = this.video.videoHeight / (this.video.videoWidth / this.width)

        // Firefox currently has a bug where the height can't be read from
        // the video, so we will make assumptions if this happens.

        if (isNaN(this.height)) {
          this.height = this.width / (4 / 3)
          console.log('Using Firefox? Assuming 4/3 Ratio')
        } */

        this.video.setAttribute('width', this.width)
        this.video.setAttribute('height', this.height)
        this.streaming = true
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
.camera {
  background-color: lightgray;
}
#video {
}
</style>
