<template>
  <div id="SeaSigns">
    <h1>Nearby Nodes for {{ myBearing }}°</h1>
    <p v-if="overpass===null">Loading Nodes...</p>
    <div
      v-for="position in overpass" v-bind:key="position.id"
      class="position"
    >
      <div v-show=isNearby(position)>
        <sea-sign-poi class="sea-sign-poi"
                      :poiDir="withDirection(position.lat, position.lon) - myBearing"
                      :poiId="position.id"
                      v-on:open-poi-info="openInfo"></sea-sign-poi>
      </div>
    </div>
    <!--<p>DEBUG: {{ overpass }}</p> -->
  </div>
</template>

// This component will show SeaMarks nodes near you.

// DEBUG: if devMode is enabled, myPostition will be Lake Constance
// [out:json][timeout:60];nwr["seamark:type"~"^light"](around:20000.0,47.603550,9.424400);out center;
// Lake Constance is 47.603550, 9.424400

<script>
import axios from 'axios'
import { getRhumbLineBearing } from 'geolib'
import SeaSignPoi from './SeaSignPoi.vue'

export default {
  name: 'SeaSigns',
  components: {
    SeaSignPoi
  },
  props: {
    myBearing: {
      type: Number,
      required: true,
      default: 0
    }
  },
  data () {
    return {
      overpass: null,
      range: 20000.0,
      precision: 30,
      myPosition: null,
      devMode: true
    }
  },
  mounted () {
    this.startGps()
  },
  methods: {
    startGps: function () {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition((position) => {
          this.gpsSuccess(position)
        })
      } else {
        console.log('Error: cannot access geolocation API, using default position')
        this.myPosition = { coords: { latitude: 47.603550, longitude: 9.424400 } }
        this.querySeamarks()
      }
    },
    gpsSuccess: function (position) {
      this.myPosition = position
      if (this.devMode) {
        this.myPosition = { coords: { latitude: 47.603550, longitude: 9.424400 } }
      }
      this.querySeamarks()
    },
    querySeamarks: function () {
      const baseUrl = 'http://overpass-api.de/api/interpreter'
      const filter = 'nwr["seamark:type"~"^light"]'
      const area = '(around:' + this.range + ',' + this.myPosition.coords.latitude + ',' + this.myPosition.coords.longitude + ')'
      const query = '?data=[out:json][timeout:60];' + filter + area + ';out center;'
      axios
        .get(baseUrl + query)
        .then(response => (this.overpass = response.data.elements))
        .catch(error => console.log(error))
    },
    withDirection: function (lat, lon) {
      return getRhumbLineBearing({ latitude: this.myPosition.coords.latitude, longitude: this.myPosition.coords.longitude }, { lat, lon })
    },
    isNearby: function (pos) {
      const diffInDegree = this.myBearing - this.withDirection(pos.lat, pos.lon)
      return (Math.abs(diffInDegree) < this.precision || Math.abs(diffInDegree) > (360 - this.precision))
    },
    openInfo: function (id) {
      // TODO:
      console.log('open info- id: ' + id)
    }
  }
}
</script>

<style scoped>
#SeaSigns{
}
.sea-sign-poi{
}
</style>
