<template>
  <div class="max-w-3xl pt-4 pb-16 mx-auto">
    <div class="p-4 text-center">
      <h1 class="text-3xl font-bold text-indigo-600">
        Peramalan Kasus Positif Covid 19 di Indonesia
      </h1>
    </div>
    <client-only v-if="covid">
      <div class="mt-6 text-center">
        <h2 class="text-2xl font-bold text-indigo-500">Bulan Januari 2021</h2>
        <p class="text-sm italic text-indigo-400">
          Fuzzy Time Series Metode Chen
        </p>
        <line-chart class="mt-4" :chart-data="chartdata"></line-chart>
      </div>
      <div class="px-4 mt-6 text-center">
        <h2 class="text-2xl font-bold text-indigo-500">Tabel</h2>
        <table class="w-full mt-4 border border-collapse border-indigo-800 table-auto">
          <thead>
            <tr class="bg-indigo-200">
              <td class="border border-indigo-600">Tanggal</td>
              <td class="border border-indigo-600">Positif</td>
              <td class="border border-indigo-600">Prediksi</td>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, i) in covid.data.train" :key="item">
              <td class="border border-indigo-600">{{ i + 1}}</td>
              <td class="border border-indigo-600">{{ item }}</td>
              <td class="border border-indigo-600">{{ forecast[i] }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </client-only>
  </div>
</template>

<script>
import LineChart from "@/components/line-chart";
import { range } from "lodash";

export default {
  components: {
    "line-chart": LineChart
  },
  head() {
    return {
      title: "Fuzzy Time Series"
    };
  },
  data() {
    return {
      covid: null,
      chartdata: null,
      series: null,
    };
  },
  computed: {
    forecast() {
      if (this.covid) {
        let newForecast = [...this.covid.data.forecast];
        newForecast.unshift(0);

        return newForecast
      } else {
        return []
      }
    }
  },
  created() {
    // this.predictCovid();
    this.getSeries();
  },
  methods: {
    async getSeries() {
      try {
        let res = await this.$axios.get('https://data.covid19.go.id/public/api/update.json')
        this.series = res.data.update.harian.reverse().splice(30)
        // console.log(this.series)
      } catch (e) {
        console.log(e)
      }
    },
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
