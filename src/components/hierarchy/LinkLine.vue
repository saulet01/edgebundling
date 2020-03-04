<template>
  <path fill="none" stroke="black" stroke-width="1" :d="lineGen([link.source, link.target])" />
</template>

<script>
import { lineRadial } from 'd3'
export default {
    props: {
        link: {
            type: Object,
            required: true
        }
    },

    computed: {
        lineGen() {
            return lineRadial()
            .angle(d => d.x * Math.PI / 180)
            .radius(d => d.y)
        }
    }
}
</script>

<style>

</style>