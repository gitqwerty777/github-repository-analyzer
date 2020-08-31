<template>
  <v-app id="fullpage">
    <v-app-bar app color="blue-grey" dark flat>
      <v-toolbar-title>Github Repository Analyzer</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-row align="center" justify="space-around">
        <v-col cols="4">
          <v-text-field v-model="user" prepend-icon="mdi-account" label="user ID" hide-details></v-text-field>
        </v-col>
        <v-col cols="2">
          <v-select
            v-model="valueType"
            :items="valueTypes"
            label="Value Types"
            prepend-icon="mdi-database"
            :hint="valueTypeHint"
            hide-details
          ></v-select>
        </v-col>
        <v-col cols="2">
          <v-switch v-model="noForked" label="No Forked Repo" hide-details></v-switch>
        </v-col>
      </v-row>
    </v-app-bar>

    <v-main>
      <v-container class="fill-height" fluid>
        <Sunburst :user="user" :noForked="noForked" :valueType="valueType" />
      </v-container>
    </v-main>
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
    valueTypes: ["Stars", "Forks", "Equal"],
    valueTypeHints: ["", "", "All repositories count as 1"],
    valueType: "Stars",
    noForked: true,
    user: "gitqwerty777",
  }),
  computed: {
    valueTypeHint: function () {
      return this.valueTypeHints[this.valueTypes.indexOf(this.valueType)];
    },
  },
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
