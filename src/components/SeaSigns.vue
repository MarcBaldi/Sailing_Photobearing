<template>
  <div id="SeaSigns">
    <h1>Nearby Nodes</h1>
    <div
      v-for="position in info" v-bind:key="position.id"
      class="position"
    >
      <div v-show=shouldShow(position)>
        id: {{ position.id }}
        lat: <span class="lighten">{{ position.lat }}</span>
        lon: <span class="lighten">{{ position.lon }}</span>
        Dir: <span class="lighten">{{ withDirection(position.lat, position.lon) }}</span>
      </div>
    </div>
    <!--<p>DEBUG: {{ info }}</p> -->
  </div>
</template>

// This component will show SeaMarks nodes near you.

// DEBUG:
// [out:json][timeout:60];area[name="Bodensee"];nwr["seamark:type"~"^light"](area);out center;
// middle of Bodensee is 47.603550, 9.424400

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
    },
    shouldShow: function (pos) {
      const distance = this.myBearing - getRhumbLineBearing({ latitude: 47.603550, longitude: 9.424400 }, { latitude: pos.lat, longitude: pos.lon })
      console.log(distance)
      return Math.abs(distance) < 90
    }
  }
}
</script>

<style scoped>
#SeaSigns{
  background-color: antiquewhite;
}
</style>
