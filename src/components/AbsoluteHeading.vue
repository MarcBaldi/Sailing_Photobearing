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
  ,
  RelativeOrientationSensor
} from 'motion-sensors-polyfill'

export default {
  name: 'AbsoluteHeading'
}
/*
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
*/
const params = new URLSearchParams(new URL(window.location.href).search.slice(1))
const relative = !!Number(params.get('relative'))
const coordinateSystem = params.get('coord')

// let container, sensor, camera, scene, renderer, model;
let sensor

// initScene();
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

// renderScene();
/*
  function initScene() {
    container = document.createElement('div');
    document.body.appendChild(container);

    camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 1, 200);
    camera.position.z = 10;

    scene = new THREE.Scene();

    var ambientLight = new THREE.AmbientLight(0x404040, 6);
    scene.add(ambientLight);

    var manager = new THREE.LoadingManager();
    var mtlLoader = new THREE.MTLLoader(manager);
    mtlLoader.setTexturePath('resources/');
    mtlLoader.load('resources/phone.mtl', materials => {
      materials.preload();
      var objLoader = new THREE.OBJLoader(manager);
      objLoader.setMaterials(materials);
      objLoader.load('resources/phone.obj', object => {
        model = object;
        scene.add(model);
      });
    });

    renderer = new THREE.WebGLRenderer({ alpha: true });
    renderer.setPixelRatio(window.devicePixelRatio);
    renderer.setSize(window.innerWidth, window.innerHeight);
    container.appendChild(renderer.domElement);

    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    }, false);

    document.addEventListener('mousedown', () => document.documentElement.requestFullscreen());
    document.addEventListener('fullscreenchange', () => {
      if (document.fullscreenElement != null) {
        screen.orientation.lock("natural")
      }
    });
  }
*/
function initSensor () {
  const options = { frequency: 60, coordinateSystem }
  console.log(JSON.stringify(options))
  sensor = relative ? new RelativeOrientationSensor(options) : new AbsoluteOrientationSensor(options)
  sensor.onreading = () => console.log('TEST.')
  sensor.onerror = (event) => {
    if (event.error.name === 'NotReadableError') {
      console.log('Sensor is not available.')
    }
  }
  sensor.addEventListener('reading', function (e) {
    const q = e.target.quaternion
    let heading = Math.atan2(2 * q[0] * q[1] + 2 * q[2] * q[3], 1 - 2 * q[1] * q[1] - 2 * q[2] * q[2]) * (180 / Math.PI)

    let html = 'Heading in degrees: ' + heading
    if (heading < 0) heading = 360 + heading
    html += '<br>Adjusted:   ' + heading
    status.innerHTML = html
  })
  sensor.start()
}
/*
  function renderScene() {
    requestAnimationFrame(renderScene);
    camera.lookAt(scene.position);
    renderer.render(scene, camera);
  }
*/
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
