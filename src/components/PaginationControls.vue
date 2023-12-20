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
        if (totalPages.value == 0){
          return 0 ; //This is for the case when we HIDE All statuses and need to 
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
<style scoped>
.pagination-controls {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px; /* Spacing between elements */
  margin-top: 20px; /* Space above the pagination controls */
}

.pagination-controls button {
  padding: 10px 20px; /* Larger padding for bigger buttons */
  font-size: 16px;
  border: 1px solid #ccc; /* Light grey border */
  border-radius: 5px; /* Rounded corners */
  background-color: #5CDB95; /* A green background */
  color: white; /* White text */
  cursor: pointer; /* Cursor pointer to indicate a clickable button */
  transition: background-color 0.3s; /* Smooth background color transition */
}

.pagination-controls button:hover {
  background-color: #45a049; /* Darker green on hover */
}

.pagination-controls button:disabled {
  background-color: #ccc; /* Greyed out button when disabled */
  cursor: default; /* Regular cursor for disabled button */
}

.pagination-controls span {
  font-size: 16px; /* Matching font size for text */
}
</style>
