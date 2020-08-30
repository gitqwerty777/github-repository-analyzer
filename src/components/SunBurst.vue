<template>
  <div>
    Chart Value:
    <select v-model="selected">
      <option selected>Stars</option>
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
  mounted: function () {
    // 基於準備好的dom，初始化echarts實例
    var myChart = echarts.init(document.getElementById("chart"));
    // 指定圖表的配置項和數據
    let data;
    //const jsonURL = "/skill.json";
    const jsonURL = `https://api.github.com/users/${this.user}/repos`;
    const title = `Github repository analyze: User ${this.user}`;

    fetch(jsonURL, {
      method: "get",
    })
      .then(function (response) {
        return response.json();
      })
      .then(function (jsonData) {
        data = jsonData;
        data = jsonData.map(function (element) {
          return {
            name: element.name,
            link: element["html_url"], //TODO: nodeclick=link?
            target: "_blank",
            description: element.description,
            language: element.language,
            forks: element.forks,
            stars: element.stargazers_count,
            value: element.stargazers_count + 1,
            tooltip: `<h4>${element.name}</h4>${element.forks} forks<br/>${
              element.stargazers_count
            } stars<br/>${element.description || "No description"}`,
          };
        });

        var totalObj = {};
        data.forEach(function (newObj) {
          newObj.language = newObj.language || "Unknown";
          if (Object.prototype.hasOwnProperty.call(totalObj, newObj.language)) {
            //https://juejin.im/post/6844903881437085709
            totalObj[newObj.language].push(newObj);
          } else {
            totalObj[newObj.language] = [newObj];
          }
        });
        data = totalObj;
        console.log(data);
        data = Object.keys(data).map(function (key) {
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
            nodeClick: "link",
            data: data,
            radius: [0, "100%"],
            sort: null,
            animationDelayUpdate: function (idx) {
              // 越往后的数据延迟越大
              return idx * 70;
            },
            label: {
              minAngle: 5,
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
                r0: "40%",
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
      })
      .catch(function (err) {
        console.log(err);
      });
  },
};
</script>

<style scoped>
#chart {
  height: 100vh;
  width: 100vw;
}
</style>