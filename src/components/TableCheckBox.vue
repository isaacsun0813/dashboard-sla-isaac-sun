<template>
    <div class="hideBar">
      <label class="hideLabel">Hide</label>
      <div class="checkbox">
        <input id="all-statuses" type="checkbox" class="styled" @change="hideShowALLstatus" />
        <label for="all-statuses">All statuses</label>
        <div v-for="status in statuses" :key="status">
          <input :id="`status-${status}`" type="checkbox" class="styled" :checked="hidestatus.includes(status)" @change="() => toggleStatus(status)" />
          <label :for="`status-${status}`">{{ status }}</label>
        </div>
      </div>
    </div>
</template>
  
<script>
  export default {
    props: {
      statuses: Array,
      hidestatus: Array
    },
    emits: ['hideShowAll', 'toggleStatus'],
    methods: {
      hideShowALLstatus() {
        this.$emit('hideShowAll');
      },
      toggleStatus(status) {
        this.$emit('toggleStatus', status);
      }
    }
  };
</script>
  
  <!-- StatusCheckbox.css -->
<style scoped>
.hideBar {
list-style: none;
display: flex;
}

.checkbox {
list-style: none;
display: flex;
}

</style>
  