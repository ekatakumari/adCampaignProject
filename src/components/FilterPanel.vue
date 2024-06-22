<template>
  <div class="bg-white p-4 rounded-lg shadow-md mb-5">
    <h2 class="text-xl font-semibold mb-4">Filters Campaign</h2>
    <form @submit.prevent="applyFilter">
      <div class="mb-4">
        <label
          for="campaignSelect"
          class="block text-sm font-medium text-gray-700"
          >Select Campaign:</label
        >
        <select
          id="campaignSelect"
          v-model="filters.campaignName"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
          @change="updateDeviceTypes"
        >
          <option value="">Select a campaign</option>
          <!-- Use v-for to loop through the campaignNames computed property -->
          <option
            v-for="(name, index) in campaignNames"
            :key="index"
            :value="name"
          >
            {{ name }}
          </option>
        </select>
      </div>
      <div class="mb-4">
        <label class="block text-sm font-medium text-gray-700"
          >Date Range</label
        >
        <input
          v-model="filters.startDate"
          type="date"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
        />
        <input
          v-model="filters.endDate"
          type="date"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
        />
      </div>
      <div class="mb-4 mt-2">
        <label
          for="deviceTypeSelect"
          class="block text-sm font-medium text-gray-700"
          v-if="deviceTypes.length > 0"
          >Select Device Type:</label
        >
        <select
          v-if="deviceTypes.length > 0"
          id="deviceTypeSelect"
          v-model="filters.deviceType"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
        >
          <option
            v-for="(deviceType, index) in deviceTypes"
            :key="index"
            :value="deviceType"
          >
            {{ deviceType }}
          </option>
        </select>

        <div
          v-if="selectedCampaign && deviceTypes.length === 0"
          class="mt-2 text-red-600"
        >
          No device types available for this campaign.
        </div>
      </div>
      <button
        type="submit"
        class="w-full bg-blue-500 text-white py-2 rounded-lg mt-6"
      >
        Apply Filters
      </button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedCampaign: "",
      filters: {
        campaignName: "",
        startDate: "",
        endDate: "",
        deviceType: "",
      },
      deviceTypes: [],
    };
  },
  props: ["campaigns"],
  computed: {
    campaignNames() {
      console.log(this.campaigns);
      return this.campaigns.map((data) => data.campaignName);
    },
  },
  methods: {
    applyFilter() {
      this.$emit("applyFilter", this.filters);
    },
    updateDeviceTypes() {
      // Clear current deviceTypes array
      this.deviceTypes = [];

      // Filter campaigns based on selectedCampaign
      const selectedCampaignData = this.campaigns.filter(
        (campaign) => campaign.campaignName === this.filters.campaignName
      );

      // Extract unique device types
      const uniqueDeviceTypes = [
        ...new Set(selectedCampaignData.map((item) => item.deviceType)),
      ];

      // Update deviceTypes array with unique device types
      this.deviceTypes = uniqueDeviceTypes;

      // Reset selected device type when campaign changes
      this.filters.deviceType = "";
    },
  },
};
</script>
<style scoped>
@import url("../main.css");
</style>
