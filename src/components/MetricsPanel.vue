<template>
    <div class="bg-white p-4 rounded-lg shadow-md mb-5">
      <h2 class="text-xl font-semibold mb-4">Metrics</h2>
      <draggable v-model="metrics" :item-key="metric =>metric" group="metrics" @end="onDragEnd" >
        <template #item="{ element }">
          <div class="bg-gray-200 p-2 rounded-md mb-2 cursor-move ">
            {{ element }}
          </div>
        </template>
      </draggable>
    </div>
  </template>
  
  <script>
  import draggable from 'vuedraggable';
  
  export default {
    components: {
      draggable,
    },
    data() {
      return {
        metrics: ['Impressions', 'Clicks', 'Conversions', 'Cost'],
      };
    },
    methods: {
      onDragEnd(event) {
        const metricElement = event.item.innerText;
        this.$emit('metricSelected', metricElement);
      },
    },
  };
  </script>
  
    <style scoped>
   @import url("../main.css");
  .draggable {
    cursor: move;
  }
  .drag-item {
  background-color: #e5e7eb; /* Example background color */
  padding: 8px; /* Adjust padding or other styles as needed */
  margin-bottom: 5px; /* Adjust margin if necessary */
  border-radius: 0.375rem; /* Rounded corners */
  cursor: move; /* Change cursor on hover */
}
  
  </style>
  