<template>
  <div class="bg-white p-4 rounded-lg shadow-md">
    <h2 class="text-xl font-semibold mb-4">Report Canvas</h2>
    <draggable
      v-model="selectedMetrics"
      :item-key="(metric) => metric"
      group="metrics"
      @end="onDragEnd"
    >
      <template #item="{ element }">
        <div class="mb-4">
          <h3 class="text-lg font-medium">{{ element }}</h3>
          <div>
            <div class="mt-2">
              <button
                @click="setChartType(element, 'bar')"
                class="mr-2 bg-red-900 border border-white-900 text-white py-2 px-4 rounded"
              >
                Bar Chart
              </button>
              <button
                @click="setChartType(element, 'line')"
                class="mr-2 bg-blue-900 border border-white-900 text-white py-2 px-4 rounded"
              >
                Line Chart
              </button>
            </div>
            <canvas :id="element" class="w-full h-64"></canvas>
          </div>
        </div>
      </template>
    </draggable>
    <div v-if="!selectedMetrics.length">
      <p>Drag metrics here to create your report.</p>
    </div>
  </div>
</template>

<script>
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  LineController,
  BarController,
} from "chart.js";
import draggable from "vuedraggable";
import { nextTick } from "vue";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  LineController,
  BarController
);

export default {
  components: {
    draggable,
  },
  props: {
    filter: Object,
  },
  data() {
    return {
      selectedMetrics: [],
      chartTypes: {},
      filteredData: [],
      charts: {},
    };
  },
  watch: {
    filter: {
      handler() {
        this.filterData();
      },
      deep: true,
    },
    selectedMetrics: {
      handler() {
        this.renderCharts();
      },
      deep: true,
    },
  },
  methods: {
    filterData() {
      this.filteredData = this.$root.$data.campaigns.filter((campaign) => {
        let matches = true;
        if (this.filter.campaignName) {
          matches =
            matches && campaign.campaignName.includes(this.filter.campaignName);
        }
        if (this.filter.startDate) {
          matches =
            matches &&
            new Date(campaign.date) >= new Date(this.filter.startDate);
        }
        if (this.filter.endDate) {
          matches =
            matches && new Date(campaign.date) <= new Date(this.filter.endDate);
        }
        if (this.filter.deviceType) {
          matches = matches && campaign.deviceType === this.filter.deviceType;
        }
        return matches;
      });
      this.renderCharts();
    },
    async renderCharts() {
      await nextTick();
      Object.values(this.charts).forEach((chart) => chart.destroy());
      this.charts = {};

      this.selectedMetrics.forEach((metric) => {
        console.log(metric);
        const ctx = document.getElementById(metric)?.getContext("2d");
        if (!ctx) return;
        const chartType = this.chartTypes[metric] || "bar";
        const chartConfig = {
          type: chartType,
          data: {
            labels: this.filteredData.map((campaign) => campaign.date),
            datasets: [
              {
                label: metric,
                data: this.filteredData.map(
                  (campaign) => campaign[metric.toLowerCase()]
                ),
                backgroundColor: "rgba(75, 192, 192, 0.2)",
                borderColor: "rgba(75, 192, 192, 1)",
                borderWidth: 1,
              },
            ],
          },
          options: {
            scales: {
              y: {
                beginAtZero: true,
              },
            },
          },
        };
        this.charts[metric] = new ChartJS(ctx, chartConfig);
      });
    },
    onDragEnd() {
      this.renderCharts();
    },
    setChartType(metric, type) {
      this.chartTypes[metric] = type;
      this.renderCharts();
    },
  },
  mounted() {
    this.filterData();
  },
};
</script>

<style scoped>
@import url("../main.css");

canvas {
  max-width: 100%;
}
</style>
