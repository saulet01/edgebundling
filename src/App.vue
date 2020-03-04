<template>
  <div id="app">
    <label>
      <input type="checkbox" v-model="showNew" />
      <span>Toggle view</span>
    </label>
    <keep-alive>
      <EdgeBundling v-if="!showNew" />
      <edge-bundler v-else />
    </keep-alive>
  </div>
</template>

<script>
import EdgeBundling from "./components/EdgeBundling";
import EdgeBundlerVue from "./components/hierarchy/EdgeBundler.vue";

export default {
  name: "App",
  data: () => ({
    showNew: true
  }),
  components: {
    EdgeBundling,
    EdgeBundler: EdgeBundlerVue
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

label {
  user-select: none;
}
</style>
