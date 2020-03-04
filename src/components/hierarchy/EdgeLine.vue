<template>
  <path fill="none" stroke="red" stroke-width=".25" :d="lineGen(nodes)" />
</template>

<script>
import { radialLine, curveBundle } from 'd3';
export default {
  props: {
    nodes: {
      type: Array,
      required: true
    }
  },
  computed: {
      lineGen() {
          return radialLine()
          .angle(d => d.x * Math.PI / 180)
          .radius(d => d.y)
          .curve(curveBundle.beta(.9))
      }
  }
};
</script>

<style>
</style>