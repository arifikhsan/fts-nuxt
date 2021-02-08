<template>
  <div class="max-w-xl mx-auto">
    <div class="p-4 text-center">
      <h1 class="text-3xl text-indigo-600 font-bold">
        Peramalan Kasus Positif Covid 19 di Indonesia
      </h1>
    </div>
    <div>
      <line-chart ></line-chart>
    </div>
  </div>
</template>

<script>
import LineChart from "@/components/line-chart";

export default {
  components: {
    'line-chart': LineChart
  }
};
</script>
