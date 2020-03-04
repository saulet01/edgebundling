<template>
  <svg
    preserveAspectRatio="xMidYMid meet"
    viewBox="-400 -325 800 650"
    class="svg-content-responsive"
  >
    <circle r="10" cx="0" cy="0" fill="green" />
  </svg>
</template>

<script>
import * as d3 from "d3";
export default {
  data: () => ({
    data: null
  }),
  methods: {
    async fetchData() {
      //   let data = await d3.json("./flare.json");
      this.data = await d3.json("./flare.json");
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

      return d3.hierarchy(map[""]);
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

  computed: {
    hierarchy() {
      if (this.data) {
        return this.packageHierarchy(this.data).sum(d => d.size);
      }

      return null;
    }
  },

  async mounted() {
   await this.fetchData()

   console.log('data loaded')
  }
};
</script>

<style>
.svg-content-responsive {
  width: 100%;
  height: 100%;
}
</style>