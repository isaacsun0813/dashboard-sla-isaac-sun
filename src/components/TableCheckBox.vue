<template>
  <div class="hideBar">
    <div class="title-container">
      <h1 class="dashboard-title">Dashboard Data</h1>
      <label class="hideLabel">Hide</label>
    </div>
    <div class="checkbox-container">
      <div class="checkbox">
        <input id="all-statuses" type="checkbox" class="styled" @change="hideShowAll" />
        <label for="all-statuses">All statuses</label>
      </div>
      <div class="statuses">
        <div v-for="status in statuses" :key="status" class="status-checkbox">
          <input :id="`status-${status}`" type="checkbox" class="styled" :checked="hidestatus.includes(status)" @change="() => toggleStatus(status)" />
          <label :for="`status-${status}`">{{ status }}</label>
        </div>
      </div>
    </div>
  </div>
</template>

  
<script>
import { defineComponent, ref, watch } from 'vue';

export default defineComponent({
    props: {
      statuses: {
        type: Array,
        default: () => []
      },
      hidestatus: {
        type: Array,
        default: () => []
      }
    },
    emits: ['hideShowAll', 'toggleStatus'],
    setup(props, { emit }) {
      // Define a ref to track changes in the status list
      const localHideStatus = ref([...props.hidestatus]);

      // Watch the prop for changes and update the local ref
      watch(() => props.hidestatus, (newVal) => {
        localHideStatus.value = [...newVal];
      });

      // Methods
      const hideShowAll = () => {
        console.log('Emitting hideShowAll');
        emit('hideShowAll');
      };

      const toggleStatus = (status) => {
        console.log('Toggling status:', status);
        emit('toggleStatus', status);
      };

      return {
        localHideStatus,
        hideShowAll,
        toggleStatus
      };
    }
});
</script>

  
  <!-- StatusCheckbox.css -->
  <style scoped>
  .hideBar {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }
  
  .title-container {
    margin-bottom: 10px; /* Adjust as needed */
  }
  
  .dashboard-title {
    font-size: 1.5em; /* Adjust the size as you like */
    margin: 0;
  }
  
  .hideLabel {
    margin-bottom: 10px; /* Adjust as needed */
  }
  
  .checkbox-container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .statuses {
  display: flex;
  justify-content: space-around; 
  flex-wrap: wrap; 
  width: 100%; 
}

.status-checkbox {
  margin: 5px; 
}
    </style>
  