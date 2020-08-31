<template>
  <v-app id="fullpage">
    <v-app-bar app color="blue-grey" dark flat>
      <v-toolbar-title>Github Repository Analyzer</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-row align="center" justify="space-around">
        <v-text-field
          v-model="user"
          prepend-icon="mdi-account"
          label="user ID"
          hide-details
          class="ma-2"
        ></v-text-field>
        <v-spacer></v-spacer>
        <v-select
          v-model="valueType"
          :items="valueTypes"
          label="Value Types"
          hide-details
          class="ma-2"
        ></v-select>
        <v-spacer></v-spacer>
        <v-switch v-model="noForked" label="noFork" hide-details></v-switch>
      </v-row>
    </v-app-bar>

    <v-main>
      <v-container class="fill-height" fluid>
        <Sunburst :user="user" :noForked="noForked" :valueType="valueType" />
      </v-container>
    </v-main>

    <v-footer app color="blue-grey" class="white--text">
      <span>
        gitqwerty777
        <a href="https://github.com/gitqwerty777">
          <i class="fab fa-github"></i>
        </a>
      </span>
      <v-spacer></v-spacer>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import Sunburst from "@/components/SunBurst.vue";

export default {
  name: "App",
  components: {
    Sunburst,
  },
  data: () => ({
    drawer: false,
    right: false,
    left: false,
    source: "",
    valueTypes: ["Stars", "Forks", "Equals"],
    valueType: "Stars",
    noForked: true,
    user: "gitqwerty777",
  }),
};
</script>

<style>
#fullpage {
  font-family: Segoe UI, Roboto, Consolas;
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
