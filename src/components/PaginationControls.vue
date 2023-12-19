<template>
    <div class="pagination-controls">
      <button @click="previousPage" :disabled="pageNumber <= 0">Previous</button>
      <button @click="nextPage" :disabled="pageNumber >= totalPages - 1">Next</button>
      <span>Page {{ displayPageNumber }} of {{ totalPages }}</span>
    </div>
</template>

<script>
import { toRefs,computed } from 'vue';

export default {
  props: {
    pageNumber: {
      type: Number,
      required: true
    },
    totalPages: {
      type: Number,
      required: true
    },
    productDataBystatus: { // New prop to receive the structured data for pagination
      type: Object,
      required: true
    },
    numberOfPages: { // Number of items per page
      type: Number,
      required: true
    }
  },
  setup(props, { emit }) {
    // Convert props to refs
    const { pageNumber, totalPages,productDataBystatus,numberOfPages} = toRefs(props);

    // Methods
    const nextPage = () => {
        console.log("CALLEd")
      if (pageNumber.value < totalPages.value - 1) {
        emit('change-page', pageNumber.value + 1);
      }
    };

    const previousPage = () => {
      if (pageNumber.value > 0) {
        emit('change-page', pageNumber.value - 1);
      }
    };
    
    const calculateTotalPages = computed(() =>{
        let total = 0;
        for (const status in productDataBystatus.value.data){
          for (const val in productDataBystatus.value.data[status]){
            total += productDataBystatus.value.data[status][val].length;
          }
        }

        return Math.ceil(total/numberOfPages.value);
      });
    const displayPageNumber = computed(() => {
        if (calculateTotalPages.value == 0){
          return 0 ;
        }
        return pageNumber.value+1;
      });
    return {
      pageNumber,
      totalPages,
      displayPageNumber,
      calculateTotalPages,
      nextPage,
      previousPage
    };
  }
};
</script>
