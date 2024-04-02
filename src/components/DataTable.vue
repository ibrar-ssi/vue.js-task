<template>
    <div class="overflow-x-auto">
     <div class="flex justify-end mb-4">
         <button @click="openColumnSelector" class="px-4 py-2 text-white bg-blue-500 rounded-md hover:bg-blue-600 focus:outline-none">Select Columns</button>
     </div>
      <column-selector-dialog
      :initiallySelectedColumns="displayedColumns"
      :availableColumns="availableColumns"
      :showModal="dialogVisible"
      @close="closeDialog"
      @columns-selected="updateDisplayedColumns"
    ></column-selector-dialog>

      <table class="table-auto w-full border-collapse border border-gray-200">
        <thead>
          <tr class="bg-gray-100">
            <th v-for="column in displayedColumns" :key="column" class="px-4 py-2 cursor-pointer" @click="sortBy(column)">
              {{ snakeToCamelCase(column)  }}
              <span v-if="sortKey === snakeToCamelCase(column)" :class="{ 'hidden': sortDirection === 'desc' }">&#9650;</span>
              <span v-if="sortKey === snakeToCamelCase(column)" :class="{ 'hidden': sortDirection === 'asc' }">&#9660;</span>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="data in sortedData" :key="data.id">
            <td v-for="column in displayedColumns" :key="column" class="border px-4 py-2">
            <template v-if="column === 'image'">
                <img :src="data.image" alt="Crypto Image" class="h-8 w-8 rounded-full">
              </template>
               <template v-else-if="column === 'name + symbol'">
                {{ data['name'] }} ({{ data['symbol'] }})
              </template>

              <template v-else>
                {{ data[column] }}
              </template>


            </td>
          </tr>
        </tbody>
      </table>
    </div>
</template>

<script>
import ColumnSelectorDialog  from './ColumnSelectorDialog.vue';

import axios from 'axios';
export default {
  components: {
    ColumnSelectorDialog,
  },
  data() {
    return {
      data: [
      ],
      dialogVisible: false,
      sortKey: null,
      sortDirection: 'asc',
      availableColumns: ['id', 'name + symbol', 'image',  'current_price', 'market_cap', 'market_cap_rank', 'fully_diluted_valuation', 'total_volume', 'high_24h', 'low_24h', 'price_change_24h', 'price_change_percentage_24h', 'market_cap_change_24h', 'market_cap_change_percentage_24h', 'circulating_supply', 'total_supply', 'max_supply', 'ath', 'ath_change_percentage', 'ath_date', 'atl', 'atl_change_percentage', 'atl_date', 'roi', 'last_updated'], 
      displayedColumns: ['image', 'name + symbol', 'current_price', 'market_cap', 'price_change_percentage_24h'] // Default columns to display
    };
  },
  computed: {
    sortedData() {
      const sortedData = [...this.data];
      if (this.sortKey) {
        sortedData.sort((a, b) => {
          let comparison = 0;
          if (a[this.sortKey] > b[this.sortKey]) {
            comparison = 1;
          } else if (a[this.sortKey] < b[this.sortKey]) {
            comparison = -1;
          }
          return this.sortDirection === 'asc' ? comparison : -comparison;
        });
      }
      return sortedData;
    }
  },
  methods: {
    sortBy(key) {
      if (this.sortKey === key) {
        this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
      } else {
        this.sortKey = key;
        this.sortDirection = 'asc';
      }
    },
     openDialog() {
      this.$emit('open-dialog');
    },
     snakeToCamelCase(str) {
      return str.replace(/_/g, ' ').replace(/\b\w/g, match => match.toUpperCase());
    },
    async fetchData() {
      try {
        const response = await axios.get('https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false');
        this.data = response.data;
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    openColumnSelector() {
      this.dialogVisible = true;
    },
    closeDialog() {
      this.dialogVisible = false;
    },
     updateDisplayedColumns(selectedColumns) {
      this.displayedColumns = selectedColumns;
       this.dialogVisible = false;
    },
  },
    created() {
    this.fetchData();
  }
};
</script>

<style>
th span {
  margin-left: 0.25rem;
}
</style>
