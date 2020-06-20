<template>
  <div id="SeaSignPoi">
    <img id="poi-img" src="../assets/seaSign.png" alt="a poi"
         v-bind:style="poiStyle()"
         v-on:click="$emit('open-poi-info', poiId)">
  </div>
</template>

// This component will .

<script>
export default {
  name: 'SeaSignPoi',
  props: {
    poiDir: {
      type: Number,
      required: true,
      default: 0
    },
    poiId: {
      type: Number,
      required: true,
      default: -1
    }
  },
  methods: {
    poiStyle: function () {
      // fixme: magic number
      const offset = this.poiDir < 180 ? this.poiDir : this.poiDir - 360
      return {
        right: offset + '%'
      }
    }
  }
}
</script>

<style scoped>
#poi-img{
  width: 2em;
  height: 2em;
  position: relative;
}
</style>
