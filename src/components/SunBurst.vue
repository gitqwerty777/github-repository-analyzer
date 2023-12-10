<template>
  <div id="sunburst">
    <!-- 為ECharts準備一個具備大小（寬高）的Dom -->
    <div id="chart"></div>
  </div>
</template>

//TODO: select value type description


<script>
import echarts from "echarts";
import _ from "lodash";

export default {
  name: "Sunburst",
  props: {
    forked: Boolean,
    showZero: Boolean,
    user: String,
    valueType: String,
  },
  data: function () {
    return {
      myChart: Object,
    };
  },
  created: function () {
    this.ChangeUserDebounced = _.debounce(this.onChangeUser, 1000);
  },
  mounted: function () {
    this.myChart = echarts.init(document.getElementById("chart"));
    this.myChart.on("click", function (params) {
      // 在用戶點擊後控制台打印數據的名稱
      event.preventDefault();
      console.log(params);
      if (Object.prototype.hasOwnProperty.call(params.data, "link")) {
        params.event.stop();
        window.open(params.data.link);
      }
    });
    this.onChangeUser();
  },
  watch: {
    showZero: function () {
      this.DrawChart();
    },
    forked: function () {
      this.DrawChart();
    },
    user: function () {
      this.ChangeUserDebounced();
    },
    valueType: function () {
      this.DrawChart();
    },
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
              link: element["html_url"],
              target: "_blank",
              description: element.description,
              language: element.language,
              forks: element.forks,
              forked: element.fork,
              stars: element.stargazers_count,
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
      let addOne = 0;
      if (this.showZero) {
        addOne = 1;
      }
      switch (this.valueType) {
        case "Forks":
          return obj.forks + addOne;
        case "Stars":
          return obj.stars + addOne;
        case "Equal":
          return 1;
      }
      return 0;
    },
    DrawChart: function () {
      var totalObj = {};
      this.fixedData.forEach(function (newObj) {
        newObj.language = newObj.language || "Unknown";
        newObj.value = this.GetValue(newObj);
        if (newObj.forked && !this.forked) {
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
      this.myChart.setOption(option);
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