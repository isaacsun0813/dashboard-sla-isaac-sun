<template>
    <table>
      <thead>
        <TableHeader :wwData="wwData" :colspan="12" />
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
  import TableHeader from './TableHeader.vue';
  
  export default {
    components: { TableHeader },
    props: ['wwData', 'displayData']
  };
  </script>
  
  <!-- MainTable.css -->
  <style scoped>
  /* ... CSS specific to this component ... */
  </style>
  