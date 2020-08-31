<template>
  <div id="page">
    User:
    <input type="text" v-model="user" @change="onChangeUser" />
    Chart Value:
    <select v-model="selected" @change="DrawChart">
      <option>Stars</option>
      <option>Forks</option>
      <option>Equal</option>
    </select>
    No forked repositories:
    <input type="checkbox" v-model="noForked" @change="DrawChart" />
    <!-- 為ECharts準備一個具備大小（寬高）的Dom -->
    <div id="chart"></div>
  </div>
</template>

<script>
import echarts from "echarts";

export default {
  name: "Sunburst",
  data: function () {
    return {
      selected: "Stars",
      fixedData: "",
      user: "gitqwerty777",
      noForked: true,
    };
  },
  mounted: function () {
    this.onChangeUser();
  },
  methods: {
    onChangeUser: function () {
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
            const forked = element.fork ? "Forked  " : "";
            const tooltip =
              `<p class="tooltip-title">${element.name}</p>` +
              forked +
              `${element.forks} <i class="fas fa-code-branch"></i> ` +
              `${element.stargazers_count} <i class='fas fa-star'></i>` +
              `<br/>${element.description || "No description"}` +
              "<br/><br/>Click to open this repository";

            return {
              name: element.name,
              link: element["html_url"], //TODO: nodeclick=link?
              target: "_blank",
              description: element.description,
              language: element.language,
              forks: element.forks,
              forked: element.fork,
              stars: element.stargazers_count,
              value: element.stargazers_count + 1,
              tooltip: tooltip,
            };
          });

          that.DrawChart();
        })
        .catch(function (err) {
          console.log(err);
        });
    },
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
      myChart.on("click", function (params) {
        // 在用戶點擊後控制台打印數據的名稱
        event.preventDefault();
        console.log(params);
        if (Object.prototype.hasOwnProperty.call(params.data, "link")) {
          params.event.stop();
          window.open(params.data.link);
        }
      });

      const jsonURL = `https://api.github.com/users/${this.user}/repos`;
      const title = `Github repository analyze: User ${this.user}`;
      var totalObj = {};
      this.fixedData.forEach(function (newObj) {
        newObj.language = newObj.language || "Unknown";
        newObj.value = this.GetValue(newObj);
        if (newObj.forked && this.noForked) {
          //do nothing
        } else if (
          Object.prototype.hasOwnProperty.call(totalObj, newObj.language)
        ) {
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
              r: "40%",
              itemStyle: {
                borderWidth: 2,
              },
              label: {
                align: "left",
              },
            },
            {
              r0: "40%",
              r: "43%",
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