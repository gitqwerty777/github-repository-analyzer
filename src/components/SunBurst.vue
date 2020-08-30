<template>
  <div id="page">
    Chart Value:
    <select v-model="selected" @change="DrawChart">
      <option>Stars</option>
      <option>Forks</option>
      <option>Equal</option>
    </select>
    <!-- 為ECharts準備一個具備大小（寬高）的Dom -->
    <div id="chart"></div>
  </div>
</template>

<script>
import echarts from "echarts";

export default {
  name: "Sunburst",
  props: {
    user: String,
  },
  data: function () {
    return {
      selected: "Stars",
      fixedData: "",
    };
  },
  mounted: function () {
    // 基於準備好的dom，初始化echarts實例
    var that = this;
    const jsonURL = `https://api.github.com/users/${this.user}/repos`;
    fetch(jsonURL, {
      method: "get",
    })
      .then(function (response) {
        return response.json();
      })
      .then(function (jsonData) {
        that.fixedData = jsonData.map(function (element) {
          return {
            name: element.name,
            link: element["html_url"], //TODO: nodeclick=link?
            target: "_blank",
            description: element.description,
            language: element.language,
            forks: element.forks,
            stars: element.stargazers_count,
            value: element.stargazers_count + 1,
            tooltip: `<p class="tooltip-title">${element.name}</p>${
              element.fork ? "forked<br/>" : ""
            }${element.forks} forks<br/>${element.stargazers_count} stars<br/>${
              element.description || "No description"
            }`,
          };
        });

        that.DrawChart();
      })
      .catch(function (err) {
        console.log(err);
      });
  },
  methods: {
    GetValue: function (obj) {
      switch (this.selected) {
        case "Forks":
          return obj.forks + 1;
        case "Stars":
          return obj.stars + 1;
        case "Equal":
          return 1;
      }
      return 0;
    },
    DrawChart: function () {
      var myChart = echarts.init(document.getElementById("chart"));
      const jsonURL = `https://api.github.com/users/${this.user}/repos`;
      const title = `Github repository analyze: User ${this.user}`;

      var totalObj = {};
      this.fixedData.forEach(function (newObj) {
        newObj.language = newObj.language || "Unknown";
        newObj.value = this.GetValue(newObj);
        if (Object.prototype.hasOwnProperty.call(totalObj, newObj.language)) {
          //https://juejin.im/post/6844903881437085709
          totalObj[newObj.language].push(newObj);
        } else {
          totalObj[newObj.language] = [newObj];
        }
      }, this);

      let data = totalObj;
      data = Object.keys(totalObj).map(function (key) {
        return { name: key, children: data[key] };
      });

      var option = {
        title: {
          text: title,
          textStyle: {
            fontSize: 20,
            align: "left",
          },
          subtext: `Source: ${jsonURL}`,
          subtextStyle: {
            align: "center",
          },
          sublink: jsonURL,
        },
        tooltip: {},
        series: {
          type: "sunburst",
          highlightPolicy: "descendant",
          data: data,
          radius: [0, "100%"],
          sort: null,
          animationDelayUpdate: function (idx) {
            // 越往后的数据延迟越大
            return idx * 30;
          },
          levels: [
            {},
            {
              r0: "15%",
              r: "35%",
              itemStyle: {
                borderWidth: 2,
              },
              label: {
                align: "left",
              },
            },
            {
              r0: "35%",
              r: "45%",
              label: {
                position: "outside",
                padding: 3,
                silent: false,
              },
              itemStyle: {
                borderWidth: 3,
              },
            },
          ],
        },
      };
      // 使用剛指定的配置項和數據顯示圖表。
      myChart.setOption(option);
    },
  },
};
</script>

<style scoped>
#page {
  height: 100vh;
  width: 100vw;
}
#chart {
  height: 95vh;
  width: 100vw;
}
</style>