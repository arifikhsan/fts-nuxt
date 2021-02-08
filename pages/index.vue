<template>
  <div class="max-w-3xl pb-16 mx-auto">
    <div class="p-4 text-center">
      <h1 class="text-3xl font-bold text-indigo-600">
        Peramalan Kasus Positif Covid 19 di Indonesia
      </h1>
    </div>
    <div v-if="covid" class="mt-6 text-center">
      <h2 class="text-2xl font-bold text-indigo-500">Bulan Januari 2021</h2>
      <span class="text-sm italic text-indigo-400">Metode Fuzzy Time Series Chen</span>
      <line-chart class="mt-4" :chart-data="chartdata"></line-chart>
    </div>
  </div>
</template>

<script>
import LineChart from "@/components/line-chart";
import { range } from "lodash";

export default {
  components: {
    "line-chart": LineChart
  },
  data() {
    return {
      covid: null,
      chartdata: null
    };
  },
  created() {
    this.predictCovid();
  },
  methods: {
    async predictCovid() {
      let train = [
        751270,
        758473,
        765350,
        772103,
        779548,
        788402,
        797723,
        808340,
        818386,
        828026,
        836718,
        846765,
        858043,
        869600,
        882418,
        896642,
        907929,
        917015,
        927380,
        939948,
        951651,
        965283,
        977474,
        989262,
        999256,
        1012350,
        1024298,
        1037993,
        1051795,
        1066313
      ];
      try {
        this.covid = await this.$axios.post(
          "https://django-fts.herokuapp.com/fts/predict",
          {
            train,
            test: [...train],
            mode: "chen"
          }
        );

        let date = range(1, 31);

        this.chartdata = {
          labels: date,
          datasets: [
            {
              label: "Kasus Positif",
              data: this.covid.data.train,
              backgroundColor: "transparent",
              borderColor: "#FF6384"
            },
            {
              label: "Prediksi",
              data: this.covid.data.forecast,
              backgroundColor: "transparent",
              borderColor: "#36A2EB"
            }
          ]
        };
      } catch (error) {
        console.log(error);
      }
    }
  }
};
</script>
