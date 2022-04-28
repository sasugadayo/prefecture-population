<template>
  <div class="caitainer">
    <div
      v-for="prefecture in prefectures"
      :key="prefecture.id"
      class="checkbox"
    >
      <label :for="prefecture.id">
        <input
          type="checkbox"
          :id="prefecture.id"
          :checked="prefecture.isChecked"
          @click="
            switchChart(prefecture.id, prefecture.name, prefecture.isChecked)
          "
        />
        {{ prefecture.name }}
      </label>
    </div>
  </div>
</template>

<script>
import axios from "axios";
// import api from "./Apikey.js";

export default {
  data() {
    return {
      prefectures: [],
    };
  },
  mounted() {
    this.drawPrefectures();
  },
  methods: {
    // APIから県庁所在地データ取得
    fetchApi(path) {
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/${path}`,
        {
          // headers: { "X-API-KEY": 'api.key' },
          headers: { "X-API-KEY": process.env.VUE_APP_API_KEY },
        }
      );
      return response;
    },
    // 県庁所在地を動的に表示
    drawPrefectures: async function () {
      const path = "prefectures";
      try {
        const response = await this.fetchApi(path);
        this.prefectures = response.data.result.map((val) => {
          return {
            id: val["prefCode"],
            name: val["prefName"],
            isChecked: false,
          };
        });
      } catch (error) {
        console.log(error.massage);
      }
    },
    /* グラフを描画 */
    drawChart: async function (id, name) {
      const path = `population/composition/perYear?cityCode=-&prefCode=${id}`;
      try {
        const response = await this.fetchApi(path);
        const population = response.data.result.data[0].data.map(
          (val) => val["value"]
        );
        this.$emit("onAddSeries", id, name, population);
        this.prefectures[id - 1].isChecked = true;
      } catch (error) {
        console.error(error.message);
      }
    },
    /* グラフを削除 */
    deleteChart: function (id) {
      this.$emit("onRemoveSeries", id);
      this.prefectures[id - 1].isChecked = false;
    },
    /* グラフの表示非表示を切り替え */
    switchChart: function (id, name, isChecked) {
      if (isChecked) {
        this.deleteChart(id);
      } else {
        this.drawChart(id, name);
      }
    },
  },
};
</script>

<style scoped>
.cantainer {
  display: flex;
  justify-content: center;
}
.checkbox {
  display: inline-block;
  margin: 5px 7px;
}
</style>
