<template>
  <div id="SeaSigns">
    <h1>Nearby Nodes</h1>
    <div
      v-for="position in info" v-bind:key="position.id"
      class="position"
    >
      id: {{ position.id }}
      lat: <span class="lighten">{{ position.lat }}</span>
      lon: <span class="lighten">{{ position.lon }}</span>
      Dir: <span class="lighten">{{ withDirection(position.lat, position.lon) }}</span>
    </div>
    <!--<p>DEBUG: {{ info }}</p> -->
  </div>
</template>

// This component will show SeaMarks nodes near you.

// middle of Bodensee 47.603550, 9.424400
// geolib.getRhumbLineBearing({ latitude: 52.518611, longitude: 13.408056 },{ latitude: 51.519475, longitude: 7.46694444 });

<script>
import axios from 'axios'
import { getRhumbLineBearing } from 'geolib'

export default {
  name: 'SeaSigns',
  props: {
    myBearing: {
      type: Number,
      required: true,
      default: 0
    }
  },
  data () {
    return {
      info: null
    }
  },
  mounted () {
    // TODO: parameterize query
    axios
      .get('http://overpass-api.de/api/interpreter?data=[out:json][timeout:60];area[name="Bodensee"];nwr["seamark:type"~"^light"](area);out center;')
      .then(response => (this.info = response.data.elements))
      .catch(error => console.log(error))
  },
  methods: {
    withDirection: function (lat, lon) {
      // TODO: position of the user
      return getRhumbLineBearing({ latitude: 47.603550, longitude: 9.424400 }, { lat, lon })
    }
  }
}
</script>

<style scoped>
#SeaSigns{
  background-color: antiquewhite;
}
</style>
