<template>
  <div>
    <!-- Hide By status Bar -->
    <div class="hideBar">
      <label class="hideLabel">Hide</label>
      <div class="checkbox">
        <!-- 'All statuses' checkbox -->
        <input id="all-statuses" type="checkbox" class="styled" @change="hideShowALLstatus" />
        <label for="all-statuses">All statuses</label>
        <!-- Dynamic status checkboxes -->
        <div v-for="status in productDataBystatus.status" :key="status">
          <input :id="`status-${status}`" type="checkbox" class="styled" :checked="hidestatus.includes(status)" @change="() => toggleStatus(status)" />
          <label :for="`status-${status}`">{{ status }}</label>
        </div>
      </div>
    </div>
    <!-- Main Table Design -->
    <table>
      <thead>
        <tr>
          <td :colspan="12">Dashboard SLA</td>
        </tr>
        <tr>
          <th colspan="3">{{ wwData }}</th>
          <th colspan="8">Product Info</th>
        </tr>
        <tr>
          <th>Status</th>
          <th>Cores</th>
          <th class="width1">Product</th>
          <th class="width1">Lithography</th>
          <th>Threads</th>
          <th>Base Freq</th>
          <th>Max Turbo Freq</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="(data, status, index) in displayData">
          <!-- status -->
          <tr>
            <td class="width1" :rowspan="calstatusRowspan(data)">
              {{ status }}
            </td>
          </tr>

          <template v-for="cores in Object.keys(data)">
            <!-- cores -->
            <tr>
              <td class="width1" :rowspan="Object.keys(data[cores]).length + 1">
                {{ cores }}
              </td>
            </tr>

            <tr v-for="(v, k) in data[cores]">
              <!-- product -->
              <td class="productColumn">{{ v.Product }}</td>

              <!-- Lithography -->
              <td>{{ v.Lithography }}</td>

              <!-- Threads -->
              <td>
                <div class="innerCells">
                  <input :value="v.Threads" :disabled="true" type="text" />
                </div>
              </td>

              <!-- Base Freq -->
              <td>
                <div class="innerCells">
                  <input :value="v.Base_Freq" :disabled="true" type="text" />
                </div>
              </td>

              <!-- Max Turbo Freq -->
              <td>
                <div class="innerCells">
                  <input :value="v.Max_Turbo_Freq" type="text" :disabled="true" />
                </div>
              </td>
            </tr>
          </template>
        </template>
      </tbody>
    </table>
    <!-- End of Table Design -->
    <div class="pagination-controls">
      <button @click="previousPage" :disabled="pageNumber <= 1">Previous</button>
      <button @click="nextPage" :disabled="pageNumber >= numberOfPages">Next</button>
      <span>Page {{ displayPageNumber }} of {{ calculateTotalPages}}</span>
    </div>
  </div>
</template>
<script>
import {reactive, computed, onMounted,toRefs, watchEffect} from 'vue';
import data from "../assets/data.json";

export default{
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
      function nextPage() {
        const totalPages = calculateTotalPages.value;
        // Increment page number if we are not on the last page. This functionality is critical because we have a limited data set now, 
        // but if we have a larger dataset it'll be important to see all of that data. 
        if (state.pageNumber < totalPages - 1) {
          state.pageNumber++;
        }
        console.log('Current page after increment:', state.pageNumber);
      }

      function previousPage(){
        if (state.pageNumber > 0){
          state.pageNumber--;
        }
      }
      function setPage(page){
        //We will need to do error handling here
        state.currentPage = page;
      }

      function toggleStatus(status) {
        const index = state.hidestatus.indexOf(status);
        if (index > -1) {
          state.hidestatus.splice(index, 1); // Remove if it exists
        } else {
          state.hidestatus.push(status); // Add if it doesn't exist
        }
        state.pageNumber = 0; // Reset the page number whenever a filter is toggled
      }
      //convert all of our methods into functions
      function calstatusRowspan(data){
          if (!data) {
            return 0; // Return a default value if data is undefined
          }
          let sum = Object.keys(data).length + 1;
          for (const cores in data) {
              sum += Object.keys(data[cores]).length;
          }
          return sum;
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
      const hideShowALLstatus = () => {
        if (state.hidestatus.length === productDataBystatus.value.status.length) {
          state.hidestatus.length = 0; // Clear the array to show all statuses
        } else {
          state.hidestatus = [...productDataBystatus.value.status]; // Hide all statuses
        }
        state.pageNumber = 0; // Reset the page number whenever 'All statuses' is toggled
      };

    onMounted(() => {
      state.wwInfo = getWWFromDate(); // Ensure getWWFromDate is defined or imported
    });

      return {
          ...toRefs(state), //StackOverflow - suppsed to make properties reactive
          // Computations
          productDataBystatus,
          wwData,
          // Methods
          calstatusRowspan,
          getWWFromDate,
          hideShowALLstatus,
          toggleStatus,
          displayData,
          nextPage,
          previousPage,
          setPage,
          calculateTotalPages,
          displayPageNumber,
      };
  },

};

</script>


<style scoped>
.fas.fa-times {
display: none;
}

.fas.fa-times.comment {
display: block;
}

.overWrittenCells:hover .fas {
display: block;
}

.innerCells {
display: flex;
flex-direction: row;
justify-content: center;
align-items: center;
}

.innerCells.comment {
display: flex;
flex-direction: row;
justify-content: center;
align-items: center;
gap: 15px;
}

table {
width: 100%;
white-space: nowrap !important;
}

table td {
position: relative;
}

i {
cursor: pointer;
}

.legendColorBox {
margin: 0.4%;
float: left;
height: 20px;
width: 30px;
border: 1px solid grey;
margin-right: 4%;
}

.inputBox {
position: absolute;
top: 0;
right: 0;
left: 0;
bottom: 0;
text-align: center;
border: 0;
text-transform: uppercase !important;
}

.inputBoxOverWritten {
top: 0;
right: 0;
left: 0;
bottom: 0;
text-align: center;
border: 0;
width: 80px;
text-transform: uppercase !important;
background: none !important;
}

.overWrittenCells {
border: 2px solid rgb(194, 1, 1);
}

.overWrittenCells input {
outline: 0;
}

input::placeholder {
color: black;
}

input:focus::-webkit-input-placeholder {
color: grey;
}

input[disabled] {
cursor: text;
background-color: inherit;
color: black;
}

.legend-labels {
white-space: nowrap !important;
padding: 0%;
display: flex;
list-style-type: none;
margin-bottom: 5px;
}

.legend-labels li {
font-size: small;
margin-right: 2%;
}

select {
position: absolute;
top: 0;
right: 0;
left: 0;
bottom: 0;
text-align: center;
border: 0;
}

table tr td:not(.skip),
table tr th {
text-align: center;
}

td,
th {
padding: 2px !important;
width: 100px;
border: 1px solid black;
}

.reference {
width: 1%;
background-color: #00b0f0;
}

.released {
width: 1%;
background-color: #7fff00;
}

.partial {
width: 1%;
background-color: #ffa500;
}

.tentative {
width: 1%;
background-color: #dcdcdc;
}

.planned {
width: 1%;
background-color: #82ffac;
}

.hideBar {
list-style: none;
display: flex;
}

.productColumn {
width: 1%;
background-color: white;
}

.checkbox {
list-style: none;
display: flex;
}

.checkbox label {
margin-left: 10px;
}

.redActual {
width: 1%;
color: red;
background-color: #dcdcdc;
}

.width1 {
width: 1%;
/* white-space: nowrap !important; */
}
</style>