<template>
  <div id="app">
    <h1>Nearby Nodes</h1>
    {{ info }}
    <p>DEBUG:</p>
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
      .get('http://overpass-api.de/api/interpreter?data=area[name="Bodensee"];nwr["seamark:type"~"^light"](area);out center;')
      .then(response => (this.info = response.data))
      .catch(error => console.log(error))
  },
  filters: {
    currencydecimal (value) {
      return value.toFixed(2)
    }
  }
}
</script>

//  area[name="Bodensee"];nwr["seamark:type"~"^light"](area);out center;
// overpass-api.de/api/

<style scoped>

</style>
