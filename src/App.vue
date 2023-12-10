<template>
  <v-app id="fullpage">
    <v-app-bar app color="blue-grey" dark flat>
      <v-toolbar-title>Github Repository Analyzer</v-toolbar-title>
      <v-row align="center" justify="space-between">
            <v-spacer></v-spacer>
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>

        <v-col cols="3">
          <v-text-field v-model="user" prepend-icon="mdi-account" label="user ID" hide-details :loading="loading"></v-text-field>
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
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>
        <v-col cols="2">
          <v-switch v-model="forked" label="Show forked repo" hide-details></v-switch>
        </v-col>
        <v-col cols="2">
          <v-switch v-model="showZero" label="Show zero value" hide-details></v-switch>
        </v-col>
        <v-btn icon @click="share">
          <v-icon>mdi-share</v-icon>
        </v-btn>
        <v-btn icon @click="openGithub">
          <v-icon>mdi-github</v-icon>
        </v-btn>
      </v-row>
    </v-app-bar>

    <v-main>
      <v-container class="fill-height" fluid>
        <Sunburst :user="user" :forked="forked" :showZero="showZero" :valueType="valueType" />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Sunburst from "@/components/SunBurst.vue";
// import { useRoute } from 'vue-router';

export default {
  name: "App",
  mounted() {
    let urlParams = new URLSearchParams(window.location.search);
    let user = urlParams.get('user');
    let valueType = urlParams.get('type');
    let forked = urlParams.get('forked');
    let showZero = urlParams.get('showzero');

    this.user = (user !== null) ? user : this.user;
    this.valueType = (valueType !== null) ? valueType : this.valueType;
    this.forked = (forked !== null) ? (forked == "true") : this.forked;
    this.showZero = (showZero !== null) ? (showZero == "true") : this.showZero;
  },
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
    forked: false,
    showZero: true,
    loading: false,
    user: "gitqwerty777",
  }),
  computed: {
    valueTypeHint: function () {
      return this.valueTypeHints[this.valueTypes.indexOf(this.valueType)];
    },
  },
  methods: {
    share: function () {
      let url = window.location.href.split('?')[0];

      url += "?user=" + this.user;
      url += "&type=" + this.valueType;
      url += "&forked=" + this.forked;
      url += "&showzero=" + this.showZero;

      navigator.clipboard.writeText(url);
      window.alert("URL copied to clipboard!"); //TODO: 預設可以從網址輸入參數
    },
    openGithub: function () {
      window.open("https://github.com/gitqwerty777");
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
