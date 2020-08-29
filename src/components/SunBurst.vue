<template>
  <!-- 為ECharts準備一個具備大小（寬高）的Dom -->
  <div id="chart"></div>
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
            link: element["html_url"],
            target: "_blank",
            description: element.description,
            language: element.language,
            forks: element.forks,
            stars: element.stargazers_count,
            value: element.stargazers_count + 1,
          };
        });

        var totalObj = {};
        data.forEach(function (newObj) {
          console.log(newObj);
          if (Object.prototype.hasOwnProperty.call(totalObj, newObj.language)) {
            //https://juejin.im/post/6844903881437085709
            //if (totalObj.hasOwnProperty(newObj.language)) {
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
            subtext: `Source: ${jsonURL}`,
            textStyle: {
              fontSize: 20,
              align: "left",
            },
            subtextStyle: {
              align: "center",
            },
            sublink: jsonURL,
          },
          series: {
            type: "sunburst",
            highlightPolicy: "descendant",
            nodeClick: "link",
            data: data,
            radius: [0, "100%"],
            sort: null,
            levels: [
              {},
              {
                r0: "15%",
                r: "35%",
                itemStyle: {
                  borderWidth: 2,
                },
                label: {
                  rotate: "tangential",
                },
              },
              {
                r0: "35%",
                r: "70%",
                label: {
                  align: "right",
                },
              },
              {
                r0: "70%",
                r: "72%",
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