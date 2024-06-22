<template>
  <div class="min-h-screen bg-gray-100 p-5">
    <h2 class=" p-4 text-3xl font-bold mb-5 rounded-lg shadow-md text-blue-800">
      AD CAMPAIGN REPORT BUILDER
    </h2>
    <FilterPanel @applyFilter="applyFilter"  :campaigns="campaigns"/>
    <div v-if="!errorMessage " class="flex">
      <MetricsPanel
        v-if="!showShimmer"
        @metricSelected="addMetric"
        class="w-30 m-5"
      />
      <ReportCanvas
        v-if="!showShimmer"
        :metrics="selectedMetrics"
        :filter="filter"
        class="w-full"
      />
      <div
        v-if="showShimmer"
        class=" animate-pulse bg-gray-300 h-40 rounded"
      ></div>
    </div>

    <div v-else class="text-red-600 text-center mt-5">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script>
import axios from "axios";
import FilterPanel from "./components/FilterPanel.vue";
import MetricsPanel from "./components/MetricsPanel.vue";
import ReportCanvas from "./components/ReportCanvas.vue";

export default {
  components: {
    FilterPanel,
    MetricsPanel,
    ReportCanvas,
  },
  data() {
    return {
      campaigns: [],
      selectedMetrics: [],
      filter: null,
      showShimmer: false,
      errorMessage: null,
    };
  },
  methods: {
    async fetchCampaignData() {
      const response = await axios.get("http://localhost:5000/api/campaigns");
      this.campaigns = response.data;
    },
    addMetric(metric) {
      if (!this.selectedMetrics.includes(metric)) {
        this.selectedMetrics.push(metric);
      }
    },
    applyFilter(filter) {
      if (!filter || !filter.campaignName || !filter.deviceType) {
        this.errorMessage = " Both campaignName and deviceType are required.";
        this.showShimmer = true;
        console.log("Error: Both campaignName and deviceType are required.");
        return false;
      } else {
        this.filter = filter;
        this.showShimmer = false;
        return true;
      }

      //console.log("Filter applied:", filter);
    },
  },
  mounted() {
    this.fetchCampaignData();
  },
};
</script>
