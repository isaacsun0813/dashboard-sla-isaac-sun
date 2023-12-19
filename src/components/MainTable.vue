<template>
    <table>
      <thead>
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
            <td class="width1" :class="getRowColor(status)" :rowspan="calstatusRowspan(data)">
              {{ status }}
            </td>
          </tr>

          <template v-for="cores in Object.keys(data)">
            <!-- cores -->
            <tr>
              <td class="width1" :class="getRowColor(status)" :rowspan="Object.keys(data[cores]).length + 1">
                {{ cores }}
              </td>
            </tr>
            <tr v-for="(v, k) in data[cores]">
                <!-- product -->
                <td :class="getRowColor(status)">{{ v.Product }}</td>
                <!-- Lithography -->
                <td :class="getRowColor(status)" >{{ v.Lithography }}</td>
                <!-- Threads -->
                <td>
                  <div class="innerCells">
                    <input :class="getRowColor(status)" :value="v.Threads" :disabled="true" type="text" />
                  </div>
                </td>
                <!-- Base Freq -->
                <td>
                  <div class="innerCells">
                    <input :class="getRowColor(status)" :value="v.Base_Freq" :disabled="true" type="text" />
                  </div>
                </td>
                <!-- Max Turbo Freq -->
                <td>
                  <div class="innerCells">
                    <input :class="getRowColor(status)" :value="v.Max_Turbo_Freq" type="text" :disabled="true" />
                  </div>
                </td>
              </tr>  
          </template>
        </template>
      </tbody>
    </table>
  </template>
  
  <script>
  import { defineComponent,toRefs } from 'vue';

  export default defineComponent({
    props: {
      wwData: {
        type: String,
        required: true
      },
      displayData: {
        type: Object,
        required: true
      }
    },
    setup(props) {
    // Define getRowColor function using Composition API
    const {displayData} = toRefs(props);
    console.log(displayData.value);
    function calstatusRowspan(data){
          if (!data) {
            return 0; // Return a default value if data is undefined
          }
          let sum = Object.keys(data).length + 1;
          for (const cores in data) {
              sum += Object.keys(data[cores]).length;
          }
          return sum;
    };
    const getRowColor = (status) => {
      switch (status) {
        case 'Announced':
          return 'row-Announced';
        case 'Discontinued':
          return 'row-Discontinued';
        case 'Launched':
          return 'row-Launched';
        case 'LaunchedW':
          return 'row-LaunchedW';
        default:
          return '';
      }
    };
    
    // Expose to template
    return {
      getRowColor,
      calstatusRowspan,
    };
  }
});
</script>
  
  <!-- MainTable.css -->
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
  .width1 {
  width: 1%;
  /* white-space: nowrap !important; */
  }
  
  /* Vue's specific Deep combinator which can penetrate component boundaries */
  ::v-deep .row-Announced input:disabled,
  ::v-deep .row-Discontinued input:disabled,
  ::v-deep .row-Launched input:disabled,
  ::v-deep .row-LaunchedW input:disabled {
    background-color: inherit !important; /* This should inherit from parent */
    color: inherit !important;
    border: none !important;
  }
  
  /* Ensure the row background colors are applied */
  ::v-deep .row-Announced {
    background-color: #FFD580 !important;
  }
  
  ::v-deep .row-Discontinued {
    background-color: #ADD8E6 !important;
  }
  
  ::v-deep .row-Launched {
    background-color: #90ee90 !important;
  }
  ::v-deep .row-LaunchedW {
    background-color: #060303 !important;
  }
  </style>
  