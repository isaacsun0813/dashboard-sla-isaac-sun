<template>
    <div class="pagination-controls">
      <button @click="previousPage" :disabled="pageNumber <= 1">Previous</button>
      <button @click="nextPage" :disabled="pageNumber >= totalPages">Next</button>
      <span>Page {{ pageNumber }} of {{ totalPages }}</span>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      pageNumber: {
        type: Number,
        required: true
      },
      totalPages: {
        type: Number,
        required: true
      }
    },
    methods: {
      nextPage() {

        console.log(this.pageNumber)
        console.log("total",this.totalPages)
        if (this.pageNumber < this.totalPages) {
          this.$emit('change-page', this.pageNumber + 1);
        }
      },
      previousPage() {
        if (this.pageNumber > 0) {
          this.$emit('change-page', this.pageNumber - 1);
        }
      }
    }
  };
  </script>
  
  <style scoped>
  </style>
  