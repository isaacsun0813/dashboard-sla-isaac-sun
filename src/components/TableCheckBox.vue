<template>
  <h1 class="dashboard-title" style="text-align: center; 
                                      margin-top: 20px; 
                                      margin-bottom: 20px; 
                                      font-size: 28px; 
                                      color: #333; 
                                      font-weight: bold; 
                                      border-bottom: 1px solid #ddd; 
                                      padding-bottom: 10px; 
                                      max-width: 1000px; 
                                      margin-left: auto; 
                                      margin-right: auto;">
    Intel Product Dashboard
  </h1>
  <div class="hideBar">
    <div class="title-container">
      <label class="hideLabel">Hide Function</label>
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

      // Methods
      const hideShowAll = () => {
        emit('hideShowAll');
      };

      const toggleStatus = (status) => {
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

<style scoped>
.hideBar {
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 5px;
  margin: 20px auto;
  max-width: 1000px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  background: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.title-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  margin-bottom: 20px;
  width: 100%;
}

.dashboard-title {
  font-size: 24px;
  color: #333;
  margin: 0;
  text-align: center;
}

.hideLabel {
  font-size: 24px;
  color: #333;
  cursor: pointer;
  font-weight: bold;
  margin-top: 10px;
}

.checkbox-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  flex-wrap: wrap;
  width: 100%;
}

.statuses {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 10px;
  width: 100%;
}

.status-checkbox {
  display: flex;
  align-items: center;
  margin: 5px;
}

.checkbox {
  display: flex;
  align-items: center;
  margin-right: 15px; /* Align the 'All statuses' checkbox with others */
}

.checkbox label,
.status-checkbox label {
  display: flex;
  align-items: center;
  margin-left: 5px;
  font-size: 16px;
  color: #333;
}

.styled {
  accent-color: #5CDB95; /* A green accent color for the checkboxes */
}

@media (max-width: 768px) {
  .title-container {
    flex-direction: column; /* This is just a bit of basic handling for responsive design */
  }
  .hideLabel {
    margin-top: 10px;
  }
  .checkbox-container {
    flex-direction: column;
    align-items: flex-start;
  }
  .checkbox,
  .status-checkbox {
    justify-content: flex-start;
  }
}
</style>
