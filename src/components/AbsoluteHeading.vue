<template>
    <div class="abs-heading">
      <div id="status"></div>
      <div id="compassContainer">
        <img id="compass" alt="compass WIP" src="../assets/logo2.png"/>
      </div>
    </div>
</template>

<script>

import {
  AbsoluteOrientationSensor
} from '../sensor-polyfills/motion-sensors.js'

export default {
  name: 'AbsoluteHeading'
}

const compass = document.getElementById('compass')
const status = document.getElementById('status')

if ('AbsoluteOrientationSensor' in window) {
  // compass.hidden = false
  const sensor = new AbsoluteOrientationSensor()
  sensor.addEventListener('reading', function (e) {
    const q = e.target.quaternion
    let heading = Math.atan2(2 * q[0] * q[1] + 2 * q[2] * q[3], 1 - 2 * q[1] * q[1] - 2 * q[2] * q[2]) * (180 / Math.PI)

    let html = 'Heading in degrees: ' + heading
    if (heading < 0) heading = 360 + heading
    html += '<br>Adjusted:   ' + heading
    status.innerHTML = html
    compass.style.Transform = 'rotate(' + heading + 'deg)'
    compass.style.WebkitTransform = 'rotate(' + heading + 'deg)'
    compass.style.MozTransform = 'rotate(' + heading + 'deg)'
  })
  sensor.start()
} else status.innerHTML = 'AbsoluteOrientationSensor not supported'

</script>

<style scoped>
.abs-heading {
  z-index: 3;
  background-color: aqua;
}
#compass{
  width:100%;max-width:400px;}
</style>
