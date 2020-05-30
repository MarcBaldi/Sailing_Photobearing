<template>
  <div id="app">
    <h1>Nearby Nodes</h1>
    <div
      v-for="position in info.elements" v-bind:key="position.id"
      class="position"
    >
      id: {{ position.id }}
      lat: <span class="lighten">{{ position.lat}}</span>
      lon: <span class="lighten">{{ position.lon}}</span>
    </div>
    <p>DEBUG: {{ info }}</p>
  </div>
</template>

// This component will show SeaMarks nodes near you.

<script>
import axios from 'axios'

export default {
  name: 'SeaSigns',
  data () {
    return {
      info: null
    }
  },
  mounted () {
    axios
      .get('http://overpass-api.de/api/interpreter?data=[out:json][timeout:60];area[name="Konstanz"];nwr["seamark:type"~"^light"](area);out center;')
      .then(response => (this.info = response.data))
      .catch(error => console.log(error))
  }
}
</script>

//  area[name="Bodensee"];nwr["seamark:type"~"^light"](area);out center;
// overpass-api.de/api/

<style scoped>

</style>
