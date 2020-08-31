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
#page {
  font-family: Segoe UI;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
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
