<template>
  <div>
    <tableCheckBox :statuses="productDataBystatus.status" :hidestatus="hidestatus" @toggleStatus="onToggleStatus" @hideShowAll="onHideShowAll"/>    <!-- Main Table Design -->
    <SearchBar @apply-filters="applyFilters" @reset-filters="resetFilters"/>
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
          numberOfPages: 100, //This should be changed depending on what the specs are
          filteredDisplayData: null,
      });
      const wwData = computed(() => `${state.wwInfo.year}WW${state.wwInfo.workweek}.${state.wwInfo.numofday}`);
      const productDataBystatus = computed(() => {
        // Structures our data so that it is readable by our template 
        // This returns an object, not an array. Important for pagination purposes. 
          let tmp = {};
          let statusSet = new Set();
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
        //Calculates the number of pages of data we will render. This is rendered on user side 
        let total = 0;
        for (const status in productDataBystatus.value.data){
          for (const val in productDataBystatus.value.data[status]){
            total += productDataBystatus.value.data[status][val].length;
          }
        }
        return Math.ceil(total/state.numberOfPages);
      });
      const displayData = computed(() => {
      // This is passed to our MainTable Component. It is used to display the data
        const flatData = [];
        for (const status of Object.keys(productDataBystatus.value.data)) {
          for (const core of Object.keys(productDataBystatus.value.data[status])) {
            for (const product of productDataBystatus.value.data[status][core]) {
              if (state.hidestatus.includes(status)) continue; // Skip if status is hidden
                flatData.push({ status, core, ...product });
              }
            }
          }
          // Dictate what pages we want to use
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
        // Used to indicate which status we are trying to use in our Dashboard.
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
        //Proccesses the data into the object format that the MainTable component expects
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
        console.log("transformed filter data",transformedData)
        return transformedData;
      }
      
      function applyFilters(filters) {
        // Response to our SearchBar component; uses the filters to get the appropriate data 
        state.pageNumber = 0;
        // Perform filtering but if it is not included we assume true
        const filteredData = state.UIData.filter(item => {
          const statusMatch = !filters.status || item.Status === filters.status;
          const coresMatch = filters.cores === null || item.Cores === filters.cores;
          const productMatch = !filters.product || item.Product.toLowerCase().includes(filters.product.toLowerCase());
          const lithographyMatch = !filters.lithography || parseInt(item.Lithography) === parseInt(filters.lithography);
          const threadsMatch = filters.threads === null || item.Threads === filters.threads;
          const baseFreqMatch = (filters.baseFreqMin === undefined && filters.baseFreqMax === undefined)|| //if both undefined 
                          (filters.baseFreqMin === null && filters.baseFreqMax === null) || //if both null. This check may be unnecessary 
                          (item.Base_Freq >= (filters.baseFreqMin || -Infinity) && //Checks the range 
                           item.Base_Freq <= (filters.baseFreqMax || Infinity));
          const maxFreqMatch = (filters.maxFreqMin === undefined && filters.maxFreqMax === undefined) ||
                         (filters.maxFreqMin === null && filters.maxFreqMax === null) ||
                         (item.Max_Turbo_Freq >= (filters.maxFreqMin || -Infinity) &&
                          item.Max_Turbo_Freq <= (filters.maxFreqMax || Infinity));

        return statusMatch && coresMatch && productMatch && lithographyMatch & threadsMatch && baseFreqMatch && maxFreqMatch;
        });
        state.filteredDisplayData = processDisplayData(filteredData);

      }
      function resetFilters() {
        // Resets all of our filters. Another response function to SearchBar component
        state.filteredDisplayData = null; // This allows our program to go back onto the original displayData
        state.totalPages = calculateTotalPages.value;// This also resets out entire paging so that it will be rendered correctly
      }

      function setPage(page){
        // Sets the page we are one
        state.pageNumber = page;
      }
      function getWWFromDate(date = null){
        // Default provided function
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
          ...toRefs(state), 
          // Computations
          productDataBystatus,
          wwData,
          displayData,
          calculateTotalPages,
          // Methods
          getWWFromDate,
          applyFilters,
          processDisplayData,
          setPage,
          resetFilters,
          onToggleStatus,
          onHideShowAll,
      };
  },

};

</script>


