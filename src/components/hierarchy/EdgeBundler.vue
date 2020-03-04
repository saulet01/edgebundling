
<template>
  <svg
    preserveAspectRatio="xMidYMid meet"
    viewBox="-400 -325 800 650"
    class="svg-content-responsive"
  >
    <g class="nodes">
      <leaf-node v-for="(node, i) in hierarchy.leaves()" :key="i" :node="node" />
    </g>

    <!-- This is actually the hierarchy path without curvature -->
    <!-- <g class="links">
      <link-line v-for="(link, i) in hierarchy.links()" :key="'link_' + i" :link="link" />
    </g>-->

    <g class="edges">
      <edge-line v-for="(edge, i) in edges" :key="'edge_' + i" :nodes="edge" />
    </g>
  </svg>
</template>

<script>
/* eslint-disable vue/no-unused-components */
import { json } from "d3-fetch";
import * as h from "d3-hierarchy";
import LeafNodeVue from "./LeafNode.vue";
import LinkLineVue from "./LinkLine.vue";
import EdgeLineVue from "./EdgeLine.vue";
// import * as d3 from "d3";
export default {
  data: () => ({
    data: null,
    hierarchy: null,
    edges: null
  }),
  components: {
    LeafNode: LeafNodeVue,
    LinkLine: LinkLineVue,
    EdgeLine: EdgeLineVue
  },
  methods: {
    async fetchData() {
      this.data = await json("./flare.json");
    },

    // Lazily construct the package hierarchy from class names.
    packageHierarchy(classes) {
      var map = {};

      function find(name, data) {
        var node = map[name],
          i;
        if (!node) {
          node = map[name] = data || { name: name, children: [] };
          if (name.length) {
            node.parent = find(name.substring(0, (i = name.lastIndexOf("."))));
            node.parent.children.push(node);
            node.key = name.substring(i + 1);
          }
        }
        return node;
      }

      classes.forEach(function(d) {
        find(d.name, d);
      });

      return h.hierarchy(map[""]);
    },

    // Return a list of imports for the given array of nodes.
    packageImports(nodes) {
      var map = {},
        imports = [];

      // Compute a map from name to node.
      nodes.forEach(function(d) {
        map[d.data.name] = d;
      });

      // For each import, construct a link from the source to target node.
      nodes.forEach(function(d) {
        if (d.data.imports)
          d.data.imports.forEach(function(i) {
            imports.push(map[d.data.name].path(map[i]));
          });
      });

      return imports;
    }
  },
  watch: {
    /**
     * Watch for when data is changed, reconstruct the hierarchy
     */
    data(val) {
      if (val) {
        // Create the hierarchy first
        this.hierarchy = this.packageHierarchy(this.data).sum(d => d.size);

        // Then apply a packing layout to the hierarchy
        h.cluster().size([360, 250])(this.hierarchy);

        // Assign edges
        this.edges = this.packageImports(this.hierarchy.leaves());
      }
    }
  },

  computed: {
    // hierarchy() {
    //   if (this.data) {
    //     return this.packageHierarchy(this.data).sum(d => d.size);
    //   }
    //   return null;
    // }
  },

  async mounted() {
    // Initially fetch the data
    await this.fetchData();

    console.log("data loaded", this.hierarchy);
  }
};
</script>

<style>
.svg-content-responsive {
  width: 100%;
  height: 100%;
}
</style>