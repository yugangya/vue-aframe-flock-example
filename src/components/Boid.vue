<template>
  <a-cone
    :position="bData.px + ' ' + bData.py + ' ' + bData.pz"
    :rotation="bData.rx + ' ' + bData.ry + ' ' + bData.rz"
    radius-bottom="0.2"
    height="0.4"
    :color="bData.colour"
  ></a-cone>
</template>

<script>
import aframe from "aframe";
export default {
  name: "Boid",
  props: {
    bData: {
      id: Number,
      px: Number,
      py: Number,
      pz: Number,
      rx: Number,
      ry: Number,
      rz: Number,
      targetPx: Number,
      targetPy: Number,
      targetPz: Number,
      targetRx: Number,
      targetRy: Number,
      targetRz: Number,
      colour: String,
    },
  },

  mounted() {},
};
</script>

<!-- 



Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
