<template>
    <div class="abs-heading">
      <div id="status">Status: </div>
      <div id="console">...</div>
      <div id="permissionsButton">
        <button v-on:click=reqPermissions>Request permissions / Start sensors</button>
      </div>
      <SeaSigns v-bind:myBearing="bearingDegree"></SeaSigns>
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
      bearingDegree: 20
    }
  },
  methods: {
    reqPermissions () {
      // request permissions from user;
      if (navigator.permissions) {
        // https://w3c.github.io/orientation-sensor/#model
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
      const reqButton = document.querySelector('#permissionsButton')
      reqButton.remove()
      const sensor = new AbsoluteOrientationSensor(options)
      sensor.onreading = () => {
        const q = sensor.quaternion
        let heading = Math.atan2(2 * q[0] * q[1] + 2 * q[2] * q[3], 1 - 2 * q[1] * q[1] - 2 * q[2] * q[2]) * (180 / Math.PI)
        if (heading < 0) heading = 360 + heading
        const html = 'Heading in degrees: ' + heading.toFixed(1)
        console.log(html)
        this.bearingDegree = heading.toFixed(1)
      }
      sensor.onerror = (event) => {
        if (event.error.name === 'NotReadableError') {
          console.log('Sensor is not available.')
        }
      }
      sensor.start()
    }
  }
}

console.log('From now on: copying console output to screen for mobile users.')
const log = console.log
console.log = (message, ...rest) => {
  const div = document.querySelector('#console')
  div.innerText = message
  log.call(console, message, ...rest)
}

</script>

<style scoped>
.abs-heading {
  z-index: 3;
  background-color: aqua;
}
</style>
