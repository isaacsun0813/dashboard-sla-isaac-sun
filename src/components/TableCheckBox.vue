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
  