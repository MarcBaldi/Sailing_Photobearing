<template>
  <div id="SeaSignPoi">
    <img id="poi-img" src="../assets/seaSign.png" alt="a poi"
         v-bind:style="poiStyle()"
         v-on:click="$emit('selected-poi', poiId)">
  </div>
</template>

// This component will display a single image, moved to the left or right relatively to its dir (direction) value.

<script>
export default {
  name: 'SeaSignPoi',
  props: {
    poiDir: {
      type: Number,
      required: true
    },
    poiId: {
      type: Number,
      required: true
    }
  },
  methods: {
    poiStyle: function () {
      const offset = this.poiDir < 180 ? this.poiDir : this.poiDir - 360
      return {
        // fixme: magic number, might be different on other screens
        right: offset * 5 + '%'
      }
    }
  }
}
</script>

<style scoped>
#SeaSignPoi{
  position: absolute;
  height: 100vh;
  width: 100vw;
  object-fit: cover;
  overflow: hidden;
}
#poi-img{
  width: 2em;
  height: 2em;
  position: relative;
  top:30%;
}
</style>
