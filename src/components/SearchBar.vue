<template>
    <div class="search-bar">
      <input v-model="filters.status" placeholder="Filter by Status" />
      <input v-model.number="filters.cores" placeholder="Filter by Cores" type="number" />
      <input v-model="filters.product" placeholder="Filter by Product" />
      <input v-model="filters.lithography" placeholder="Filter by Lithography" />
      <input v-model.number="filters.threads" placeholder="Filter by Threads" type="number" />
      <div class="base-frequency">
        <label>
          <input type="checkbox" v-model="filters.freqCheckbox" />
          Frequency less than or equal to
        </label>
        <input v-model.number="filters.baseFreq" placeholder="Base Frequency" type="number" />
      </div>
      <div class="frequency-filter">
        <label>
          <input type="checkbox" v-model="filters.freqCheckbox" />
          Frequency less than or equal to
        </label>
        <input v-model.number="filters.maxFreq" placeholder="Max Frequency" type="number" />
      </div>
      <button @click="applyFilters">Filter</button>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  
  export default {
    name: 'SearchBar',
    setup(_, { emit }) {
      const filters = ref({
        status: '',
        cores: null,
        product: '',
        lithography: '',
        threads: null,
        baseFreq: null,
        maxFreq: null,
        freqCheckbox: false
      });
  
      const applyFilters = () => {
        console.log("applied")
        emit('apply-filters', filters.value);
      };
  
      return {
        filters,
        applyFilters
      };
    }
  };
  </script>
  
  <style scoped>
  .search-bar {
  }
  .frequency-filter {
    display: flex;
    align-items: center;
  }
  .base-frequency {
    display: flex;
    align-items: center;
  }
  </style>
  