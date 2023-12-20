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
  },
  setup(props, { emit }) {
    // Convert props to refs
    const { pageNumber, totalPages} = toRefs(props);

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

    const displayPageNumber = computed(() => {
        if (totalPages == 0){
          return 0 ;
        }
        return pageNumber.value+1;
      });
    return {
      pageNumber,
      displayPageNumber,
      nextPage,
      previousPage
    };
  }
};
</script>
