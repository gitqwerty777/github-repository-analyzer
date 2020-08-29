<template>
  <Sunburst msg="Hello Vue 3.0 + Vite" />
</template>

<script>
import Sunburst from "@/components/SunBurst.vue";
export default {
  name: "App",
  components: {
    Sunburst,
  },
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

body {
  height: 100%;
  width: 100%;
}
</style>
