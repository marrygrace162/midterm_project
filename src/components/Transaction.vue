<template>
  <div class="transaction flex items-center gap-4 p-4 bg-white rounded-lg shadow-md border border-gray-200">
    <input type="checkbox" v-model="isEditing" class="w-4 h-4 text-teal-600 focus:ring-teal-600" />
    <span v-if="!isEditing" class="flex-1 text-gray-800">
      {{ transaction.description }} ({{ transaction.category }}) - <span class="font-semibold ${{ transaction.category === 'Income' ? 'text-teal-600' : 'text-red-500' }}">${{ transaction.amount.toFixed(2) }}</span>
    </span>
    <div v-else class="flex-1 flex gap-2">
      <input v-model="editedDescription" placeholder="Description" class="flex-1 p-3 border border-gray-300 rounded-md text-gray-800 focus:ring-2 focus:ring-teal-600 focus:border-transparent" />
      <input v-model.number="editedAmount" type="number" placeholder="Amount" class="w-20 p-3 border border-gray-300 rounded-md text-gray-800 focus:ring-2 focus:ring-teal-600 focus:border-transparent" />
      <select v-model="editedCategory" class="p-3 border border-gray-300 rounded-md text-gray-800 focus:ring-2 focus:ring-teal-600 focus:border-transparent">
        <option value="Income">Income</option>
        <option value="Expense">Expense</option>
      </select>
      <button @click="saveEdit" class="bg-teal-600 text-white px-3 py-2 rounded-md hover:bg-teal-700 transition-colors">Save</button>
    </div>
    <button @click="$emit('delete', transaction.id)" class="bg-red-600 text-white px-3 py-2 rounded-md hover:bg-red-700 transition-colors">Delete</button>
  </div>
</template>

<script>
export default {
  props: ['transaction'],
  data() {
    return {
      isEditing: false,
      editedDescription: this.transaction.description,
      editedAmount: this.transaction.amount,
      editedCategory: this.transaction.category
    };
  },
  methods: {
    saveEdit() {
      this.$emit('edit', {
        id: this.transaction.id,
        description: this.editedDescription,
        amount: this.editedAmount,
        category: this.editedCategory
      });
      this.isEditing = false;
    }
  }
};
</script>