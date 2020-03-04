<template>
  <g class="leafnode" v-bind="transform">
      <!-- <text>
          {{node.data.key}}

          <title>This is a title</title>
      </text> -->

      <circle fill="red" cx="0" cy="0" r="5" />
  </g>
</template>

<script>
export default {
    props: {
        node: {
            type: Object,
            required: true
        }
    },
    computed: {
        transform() {
            const {
                x: degrees,
                y: radius
            } = this.node


            // radians
            const rads = degrees * Math.PI / 180

            const xpos = Math.cos(rads) * radius
            const ypos = Math.sin(rads) * radius

            return {
                transform: `translate(${xpos} ${ypos})`
            }
        }
    }
}
</script>

<style>

</style>