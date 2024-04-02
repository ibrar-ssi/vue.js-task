<template>
  <div class="container mx-auto p-4">
        <h1 class="text-2xl font-bold mb-4">Crypto List</h1>
    <Table />
  </div>
</template>

<script>
import Table from './components/DataTable.vue';

export default {
  components: {
    Table,
  },
};
</script>

<style>
</style>