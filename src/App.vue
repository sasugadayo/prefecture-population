<template>
  <div id="app">
    <Header />
    <div class="container">
      <h2>都道府県</h2>
      <Prefecture @onAddSeries="addSeries" @onRemoveSeries="removeSeries" />
      <Highchart :options="options" />
    </div>
  </div>
</template>

<script>
import { Chart } from "highcharts-vue";
import Header from "./components/PrefectureHeader.vue";
import Prefecture from "./components/PrefectureList.vue";

export default {
  name: "App",
  components: {
    Header,
    Prefecture,
    Highchart: Chart,
  },
  data() {
    return {
      /* Highchartsのプロパティ */
      options: {
        /* seriesにオブジェクトを追加するとグラフに描画される */
        series: [],
        title: {
          style: {
            display: "none",
          },
        },
        legend: {
          align: "right",
          verticalAlign: "top",
          layout: "vertical",
        },
        xAxis: {
          title: {
            text: "年度",
          },
          categories: [
            1960, 1965, 1970, 1975, 1980, 1985, 1990, 1995, 2000, 2005, 2010,
            2015, 2020, 2025, 2030, 2035, 2040, 2045,
          ],
        },
        yAxis: {
          title: {
            text: "人口数",
          },
        },
      },
    };
  },
  methods: {
    /* seriesに追加 */
    addSeries: function (id, name, population) {
      this.options.series.push({
        id: id,
        name: name,
        data: population,
      });
    },
    /* seriesから削除 */
    removeSeries: function (id) {
      this.options.series = this.options.series.filter((val) => val.id !== id);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.container {
  max-width: 1020px;
  margin: 0px auto;
}

h2 {
  margin-left: 5px;
}
</style>
