<template>
    <div id="absolute-heading">
      <div id="status" v-show="devMode">Status: </div>
      <div id="console" v-show="devMode">...</div>
      <sea-signs v-bind:myBearing="deviceRotation"></sea-signs>
    </div>
</template>

<script>
import { AbsoluteOrientationSensor } from 'motion-sensors-polyfill'
import SeaSigns from './SeaSigns.vue'

export default {
  name: 'AbsoluteHeading',
  components: {
    SeaSigns
  },
  data () {
    return {
      deviceRotation: 20,
      devMode: true
    }
  },
  mounted () {
    this.reqPermissions()
  },
  methods: {
    reqPermissions () {
      // request permissions from user;
      // code from the official wiki (which is still WIP)
      // https://w3c.github.io/orientation-sensor/#model
      if (navigator.permissions) {
        Promise.all([navigator.permissions.query({ name: 'accelerometer' }),
          navigator.permissions.query({ name: 'magnetometer' }),
          navigator.permissions.query({ name: 'gyroscope' })])
          .then(results => {
            if (results.every(result => result.state === 'granted')) {
              this.initSensor()
            } else {
              console.log('Permission to use sensor was denied.')
            }
          }).catch(err => {
            console.log('Integration with Permissions API is not enabled, still try to start app.')
            console.log(err)
            this.initSensor()
          })
      } else {
        console.log('No Permissions API, still try to start app.')
        this.initSensor()
      }
    },
    initSensor () {
      const options = { frequency: 30 }
      const sensor = new AbsoluteOrientationSensor(options)
      sensor.onreading = () => {
        const q = sensor.quaternion
        let heading = Math.atan2(2 * q[0] * q[1] + 2 * q[2] * q[3], 1 - 2 * q[1] * q[1] - 2 * q[2] * q[2]) * (180 / Math.PI)
        if (heading < 0) heading = 360 + heading
        this.deviceRotation = heading.toFixed(1)
      }
      sensor.onerror = (event) => {
        if (event.error.name === 'NotReadableError') {
          console.log('Sensor is not available.')
        } else {
          console.log('Undefined sensor error: ' + event.error.name)
        }
      }
      sensor.start()
    }
  }
}

// this will be removed after early development
console.log('From now on: copying console output to screen for mobile users.')
const log = console.log
console.log = (message, ...rest) => {
  const div = document.querySelector('#console')
  const node = document.createElement('P')
  const textNode = document.createTextNode(message)
  node.appendChild(textNode)
  div.appendChild(node)
  log.call(console, message, ...rest)
}
</script>

<style scoped>
#absolute-heading {
  position: absolute;
  width: 100vw;
  height: 100vh;
  z-index: 2;
}
</style>
