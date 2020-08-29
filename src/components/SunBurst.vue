<template>
  <!-- 為ECharts準備一個具備大小（寬高）的Dom -->
  <div id="chart"></div>
</template>

<script>
export default {
  name: "Sunburst",
  props: {
    msg: String,
  },
  mounted: function () {
    // 基於準備好的dom，初始化echarts實例
    var myChart = echarts.init(document.getElementById("chart"));
    // 指定圖表的配置項和數據
    let data;
    const jsonURL = "/skill.json";
    //const jsonURL = "";

    fetch(jsonURL, {
      method: "get",
    })
      .then(function (response) {
        return response.json();
      })
      .then(function (jsonData) {
        data = jsonData;
        var option = {
          title: {
            text: `Github repository analyze`,
            subtext: `Source: ${jsonURL}`,
            textStyle: {
              fontSize: 20,
              align: "center",
            },
            subtextStyle: {
              align: "center",
            },
            sublink: jsonURL,
          },
          series: {
            type: "sunburst",
            highlightPolicy: "descendant",
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
        // Error :(
      });
  },
  data() {
    return {
      count: 0,
    };
  },
};
</script>

<style lang="sass" scoped>
#chart
  width: 100vw
  height: 100vh
</style>