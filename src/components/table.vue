<template>
  <div>
    <tableCheckBox :statuses="productDataBystatus.status" :hidestatus="hidestatus" @toggleStatus="onToggleStatus" @hideShowAll="onHideShowAll"/>    <!-- Main Table Design -->
    <SearchBar @apply-filters="applyFilters"/>
    <MainTable :wwData="wwData" :displayData="filteredDisplayData || displayData" />
    <PaginationControls :pageNumber="pageNumber" :totalPages="totalPages" @change-page="setPage"/>
    </div>
</template>
<script>
import tableCheckBox from './tableCheckBox.vue';
import MainTable from './MainTable.vue';
import PaginationControls from './PaginationControls.vue';
import {reactive, computed, onMounted,toRefs} from 'vue';
import data from "../assets/data.json";
import SearchBar from './SearchBar.vue';
export default{
  components: {
    tableCheckBox,
    MainTable,
    PaginationControls,
    SearchBar,
  },
  setup(){
      //All const variables 
      const state = reactive({
          hidestatus: [],
          allCheckBox: [],
          wwInfo: {},
          hidden: false,
          UIData: data,
          pageNumber: 0,
          totalPages: 0,
          numberOfPages: 100,
          filteredDisplayData: null,
      });
      const wwData = computed(() => `${state.wwInfo.year}WW${state.wwInfo.workweek}.${state.wwInfo.numofday}`);
      const productDataBystatus = computed(() => {
        // Structures our data so that it is readable by our template 
        // This returns an object, not an array. Important for pagination purposes. 
          console.log('hidestatus in computed property:', state.hidestatus);
          let tmp = {};
          let statusSet = new Set();
          console.log("UI DATA", state.UIData)
          state.UIData.forEach((element) => {
              let status = element.Status;
              let cores = element.Cores;
              statusSet.add(status);

              if (state.hidestatus.includes(status)) 
                  return;

              if (!tmp[status]) 
                  tmp[status] = {};

              if (!tmp[status][cores]) 
                  tmp[status][cores] = [];

              tmp[status][cores].push(element);
          });

          const sortedStringsArray = Array.from(statusSet).sort();
          statusSet = new Set(sortedStringsArray);
          return {
              status: Array.from(statusSet),
              data: tmp,
          };
      });
      const calculateTotalPages = computed(() =>{
        let total = 0;
        for (const status in productDataBystatus.value.data){
          for (const val in productDataBystatus.value.data[status]){
            total += productDataBystatus.value.data[status][val].length;
          }
        }
        return Math.ceil(total/state.numberOfPages);
      });
      const displayData = computed(() => {
      // Flatten the data
        const flatData = [];
        for (const status of Object.keys(productDataBystatus.value.data)) {
          for (const core of Object.keys(productDataBystatus.value.data[status])) {
            for (const product of productDataBystatus.value.data[status][core]) {
              if (state.hidestatus.includes(status)) continue; // Skip if status is hidden
                flatData.push({ status, core, ...product });
              }
            }
          }
          // Paginate
        const startIndex = state.pageNumber * state.numberOfPages;
        const endIndex = startIndex + state.numberOfPages-1;
        const paginatedItems = flatData.slice(startIndex, endIndex);
        // Reformat for the template
        const templateData = {};
        for (const item of paginatedItems) {
          if (!templateData[item.status]) {
            templateData[item.status] = {};
          }
          if (!templateData[item.status][item.core]) {
            templateData[item.status][item.core] = [];
          }
          templateData[item.status][item.core].push(item);
        }
        state.totalPages = calculateTotalPages.value;
        return templateData;
      });
      
      const onToggleStatus = (status) => {
        const index = state.hidestatus.indexOf(status);
        if (index > -1) {
        // If the status is already in the array, remove it
          state.hidestatus.splice(index, 1);
        } else {
        // Otherwise, add the status to the array
          state.hidestatus.push(status);
        }
        // Since we're modifying the array directly, we need to ensure the change is reactive
        state.hidestatus = [...state.hidestatus];
        };
      const onHideShowAll = () => {
        if (state.hidestatus.length === productDataBystatus.value.status.length) {
          state.hidestatus = [];
        } else {
          state.hidestatus = [...productDataBystatus.value.status];
        }
      };
      //All Functions
      function processDisplayData(data) {
        const transformedData = {};
        let total = 0;
        for (const item of data) {
          const status = item.Status;
          const cores = item.Cores.toString(); // Make sure cores is a string if it's being used as a key
          if (!transformedData[status]) {
            transformedData[status] = {};
          }
          if (!transformedData[status][cores]) {
            transformedData[status][cores] = [];
          }
          transformedData[status][cores].push(item);
          total++;
        }
        state.totalPages = 1;
        return transformedData;
      }
      
      function applyFilters(filters) {
        console.log('Filters received in parent:', filters); // This should log the filters object
        state.pageNumber = 0;
        // Perform filtering but if it is not included we assume true
        const filteredData = state.UIData.filter(item => {
          const statusMatch = !filters.status || item.Status === filters.status;
          const coresMatch = filters.cores === null || item.Cores === filters.cores;
          const productMatch = !filters.product || item.Product.toLowerCase().includes(filters.product.toLowerCase());
          const lithographyMatch = !filters.lithography || parseInt(item.Lithography) === parseInt(filters.lithography);
          const threadsMatch = filters.threads === null || item.Threads === filters.threads;
          const baseFreqMatch = filters.baseFreq === null || parseFloat(item.BaseFreq) === filters.baseFreq;  
          let maxFreqMatch = true;
          if (filters.freqCheckbox && filters.maxFreq !== null) {
            maxFreqMatch = parseFloat(item.MaxFreq) <= filters.maxFreq;
          } else if (filters.maxFreq !== null) {
            maxFreqMatch = parseFloat(item.MaxFreq) === filters.maxFreq;
        }
      return statusMatch && coresMatch && productMatch && lithographyMatch & threadsMatch && baseFreqMatch && maxFreqMatch;
      });
      state.filteredDisplayData = processDisplayData(filteredData);
    }

      function setPage(page){
        state.pageNumber = page;
      }
      function getWWFromDate(date = null){
          let currentDate = date || new Date();
          let startDate = new Date(currentDate.getFullYear(), 0, 1);
          let days = Math.floor((currentDate - startDate) / (24 * 60 * 60 * 1000));
          return {
              year: currentDate.getFullYear(),
              workweek: Math.ceil(days / 7),
              numofday: currentDate.getDay(),
          };
      }


    onMounted(() => {
      state.wwInfo = getWWFromDate(); // Ensure getWWFromDate is defined or imported
    });

      return {
          ...toRefs(state), //StackOverflow - suppsed to make properties reactive
          // Computations
          productDataBystatus,
          wwData,
          // Methods
          getWWFromDate,
          displayData,
          applyFilters,
          processDisplayData,
          calculateTotalPages,
          setPage,
          onToggleStatus,
          onHideShowAll,
      };
  },

};

</script>


