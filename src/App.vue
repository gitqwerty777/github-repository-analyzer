<template>
  <Sunburst user="gitqwerty777" />
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
  margin: 0;
}

.tooltip-title {
  font-size: 18px;
  margin: 0;
  padding: 0;
}
</style>
