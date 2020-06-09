<template>
    <div class="abs-heading">
      <div id="status">Status: </div>
      <div id="console">...</div>
      <div id="compassContainer">
        <img id="compass" alt="compass WIP" src="../assets/logo2.png"/>
      </div>
    </div>
</template>

<script>

import {
  AbsoluteOrientationSensor
} from 'motion-sensors-polyfill'

let sensor

export default {
  name: 'AbsoluteHeading'
}

// request permissions from user;
if (navigator.permissions) {
  // https://w3c.github.io/orientation-sensor/#model
  Promise.all([navigator.permissions.query({ name: 'accelerometer' }),
    navigator.permissions.query({ name: 'magnetometer' }),
    navigator.permissions.query({ name: 'gyroscope' })])
    .then(results => {
      if (results.every(result => result.state === 'granted')) {
        initSensor()
      } else {
        console.log('Permission to use sensor was denied.')
      }
    }).catch(err => {
      console.log('Integration with Permissions API is not enabled, still try to start app.')
      console.log(err)
      initSensor()
    })
} else {
  console.log('No Permissions API, still try to start app.')
  initSensor()
}

function initSensor () {
  const options = { frequency: 60, coordinateSystem: null }
  console.log(JSON.stringify(options))
  sensor = new AbsoluteOrientationSensor(options)
  sensor.onreading = () => {
    const q = sensor.quaternion
    let heading = Math.atan2(2 * q[0] * q[1] + 2 * q[2] * q[3], 1 - 2 * q[1] * q[1] - 2 * q[2] * q[2]) * (180 / Math.PI)
    if (heading < 0) heading = 360 + heading
    const html = 'Heading in degrees: ' + heading
    console.log(html)
  }
  sensor.onerror = (event) => {
    if (event.error.name === 'NotReadableError') {
      console.log('Sensor is not available.')
    }
  }
  /* sensor.addEventListener('reading', function (e) {
    const q = e.target.quaternion
    let heading = Math.atan2(2 * q[0] * q[1] + 2 * q[2] * q[3], 1 - 2 * q[1] * q[1] - 2 * q[2] * q[2]) * (180 / Math.PI)

    let html = 'Heading in degrees: ' + heading
    if (heading < 0) heading = 360 + heading
    html += '<br>Adjusted:   ' + heading
    DisplayResult = html
  }) */
  sensor.start()
}

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
#compass{
  width:100%;max-width:400px;}
</style>
