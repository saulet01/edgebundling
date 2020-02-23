<template>
    <div id="app">
        <EdgeBudnling />
    </div>
</template>

<script>
    import EdgeBudnling from "./components/EdgeBundling";

    export default {
        name: "App",
        components: {
            EdgeBudnling
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
</style>
