<template>
  <div>
    <div  v-if="showModal" @click.self="closeDialog"
      class="fixed left-0 top-0 z-[1055] inset-0 flex items-center justify-center overflow-auto bg-gray-800 bg-opacity-50">
      <div
        class="relative w-full max-w-md p-6 bg-white rounded-lg shadow-md">
        <div class="flex justify-between items-center pb-4">
          <h5 class="text-xl font-medium leading-normal text-gray-900">
            Select Column to select
          </h5>
          <button
            type="button"
            @click="closeDialog"
            class="text-gray-500 hover:text-gray-700 focus:outline-none">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"
              xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12">
              </path>
            </svg>
          </button>
        </div>         
        <div class="relative p-4">
        <div class="column-list ">
        <label class="pr-2" v-for="(column, index) in availableColumns" :key="index">
          <input type="checkbox" v-model="selectedColumns" :value="column"> {{ snakeToCamelCase(column) }}
        </label>
      </div>
        </div>
        <div class="flex justify-end space-x-4">
          <button
            type="button"
            @click.self="closeDialog"
            class="px-4 py-2 bg-gray-200 text-gray-700 rounded-md hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-500">
            Close
          </button>
          <button
          @click="confirmSelection"
            type="button"
            class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500">
            Save changes
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
    props: {
    title: String,
    showModal: Boolean,
    availableColumns: Array,
    initiallySelectedColumns: Array
  },
  data() {
    return {
      selectedColumns: [...this.initiallySelectedColumns],
    };
  },
  methods: {
    closeModal() {
      this.$emit('close-dialog');
    },
     closeDialog() {
      this.$emit('close');
    },
     confirmSelection() {
      this.$emit('columns-selected', this.selectedColumns);
    },
      snakeToCamelCase(str) {
      return str.replace(/_/g, ' ').replace(/\b\w/g, match => match.toUpperCase());
    },
  }
};
</script>

<style>
.column-list {
  display: flex;
  flex-direction: column;
  max-height: 300px;
  overflow-y: auto;
  flex-wrap: wrap;
}

</style>
