<template>
  <div class = "search-bar-container">
    <h2 class = "search-header"> Search Function</h2>
    <div class="search-bar">
      <input v-model="filters.status" placeholder="Filter by Status" />
      <input v-model.number="filters.cores" placeholder="Filter by Cores" type="number" />
      <input v-model="filters.product" placeholder="Filter by Product" />
      <input v-model="filters.lithography" placeholder="Filter by Lithography" />
      <input v-model.number="filters.threads" placeholder="Filter by Threads" type="number" />
      <div class="frequency-range">
        <label>Base Frequency Range</label>
        <input v-model.number="filters.baseFreqMin" placeholder="Min" type="number" />
        <input v-model.number="filters.baseFreqMax" placeholder="Max" type="number" />
      </div>
      <div class="frequency-range">
        <label>Max Frequency Range</label>
        <input v-model.number="filters.maxFreqMin" placeholder="Min" type="number" />
        <input v-model.number="filters.maxFreqMax" placeholder="Max" type="number" />
      </div>
      <button @click="applyFilters">Filter</button>
      <button @click="resetFilters">Reset</button>
    </div>
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
        emit('apply-filters', filters.value);
      };
      const resetFilters = () => {
        emit('reset-filters');
      };

      return {
        filters,
        applyFilters,
        resetFilters
      };
    }
  };
  </script>
  
  <style scoped>
  .search-bar-container {
    border: 1px solid #ddd; /* Light grey border */
    padding: 20px;
    border-radius: 5px; /* Rounded corners */
    margin: 20px auto; /* Center the box */
    max-width: 1000px; /* Maximum width of the box */
    box-shadow: 0 2px 5px rgba(0,0,0,0.1); /* Subtle shadow for depth */
    background: #fff; /* White background */
  }
  
  .search-header {
    text-align: center;
    margin-bottom: 20px;
    color: #333; /* Dark grey color for the text */
    font-size: 24px; /* Larger font size for the header */
  }
  
  .search-bar {
    display: flex;
    flex-wrap: wrap; /* Allow items to wrap to next line on small screens */
    gap: 10px; /* Spacing between elements */
    justify-content: center; /* Center elements horizontally */
  }
  
  .search-bar input,
  .search-bar button {
    padding: 10px;
    border: 1px solid #ccc; /* Light grey border for inputs and buttons */
    border-radius: 5px; /* Rounded corners for inputs and buttons */
    margin-bottom: 10px; /* Spacing below inputs and buttons */
  }
  
  .frequency-range {
    display: flex;
    align-items: center;
    gap: 10px; /* Spacing between label and input */
  }
  
  button {
    background-color: #5CDB95; /* A green background for the buttons */
    color: white; 
    cursor: pointer; 
    border: none; 
  }
  
  button:hover {
    background-color: #45a049; /* Darker green background on hover */
  }
  
  @media (max-width: 768px) {
    .search-bar {
      flex-direction: column; /* Stack elements vertically on small screens. Media responsiveness could be added to the other sections too if desired. */
    }
  }
  </style>