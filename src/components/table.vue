<template>
  <div>
    <!-- Hide By status Bar -->
    <tableCheckBox :statuses="productDataBystatus.status" :hidestatus="hidestatus"/>
    <!-- Main Table Design -->
    <MainTable :wwData="wwData" :displayData="displayData" />
    <!-- End of Table Design -->
    <PaginationControls :pageNumber="pageNumber" :totalPages="calculateTotalPages" @change-page="setPage"/>  </div>
</template>
<script>
import tableCheckBox from './tableCheckBox.vue';
import MainTable from './MainTable.vue';
import PaginationControls from './PaginationControls.vue';
import {reactive, computed, onMounted,toRefs} from 'vue';
import data from "../assets/data.json";

export default{
  components: {
    tableCheckBox,
    MainTable,
    PaginationControls,
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
          numberOfPages: 100,
      });
      const { hidestatus } = toRefs(state);
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
        return templateData;
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
      const displayPageNumber = computed(() => {
        if (calculateTotalPages.value == 0){
          return 0 ;
        }
        return state.pageNumber+1;
      })
      //All Functions

      function setPage(page){
        //We will need to do error handling here
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
          setPage,
          calculateTotalPages,
          displayPageNumber,
      };
  },

};

</script>


