<template>
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
</template>
  
<script>
  export default {
    name: 'TableHeader',
    props: {
      wwData: String,
      colspan: {
        type: Number,
        default: 12
      }
    }
  };
</script>
<style scoped>
.width1 {
width: 1%;
/* white-space: nowrap !important; */
}
</style>  