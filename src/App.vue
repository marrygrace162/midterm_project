<template>
  <div class="min-h-screen bg-gradient-to-b from-gray-200 to-gray-50 p-6 font-inter">
    <div class="max-w-3xl mx-auto">
      <h1 class="text-4xl font-bold text-white bg-teal-500 font-poppins py-4 px-6 rounded-lg border-b-4 border-teal-600 mb-8 text-center">VueBudget</h1>
      <div class="summary bg-white p-6 rounded-lg shadow-lg border border-gray-300 mb-6">
        <p class="text-lg text-gray-900 font-medium">Total Income: <span class="text-teal-500 font-semibold">${{ totalIncome.toFixed(2) }}</span></p>
        <p class="text-lg text-gray-900 font-medium">Total Expenses: <span class="text-red-600 font-semibold">${{ totalExpenses.toFixed(2) }}</span></p>
        <p class="text-lg text-gray-900 font-semibold">Balance: <span class="text-teal-500">${{ balance.toFixed(2) }}</span></p>
      </div>
      <form @submit.prevent="addTransaction" class="bg-white p-6 rounded-lg shadow-lg border border-gray-300 mb-6 flex flex-col sm:flex-row gap-4">
        <input v-model="newDescription" placeholder="Description (e.g., Salary)" class="flex-1 p-3 border border-gray-400 rounded-md text-gray-900 focus:ring-2 focus:ring-teal-500 focus:border-transparent" required />
        <input v-model.number="newAmount" type="number" placeholder="Amount" class="w-24 p-3 border border-gray-400 rounded-md text-gray-900 focus:ring-2 focus:ring-teal-500 focus:border-transparent" required />
        <select v-model="newCategory" class="p-3 border border-gray-400 rounded-md text-gray-900 focus:ring-2 focus:ring-teal-500 focus:border-transparent">
          <option value="Income">Income</option>
          <option value="Expense">Expense</option>
        </select>
        <button type="submit" class="bg-blue-600 text-white px-4 py-3 rounded-md hover:bg-blue-700 transition-colors">Add Transaction</button>
        <p v-if="error" class="text-red-600 mt-2 text-sm w-full font-medium">{{ error }}</p>
      </form>
      <div class="filter mb-6">
        <label class="mr-2 text-gray-900 font-medium">Filter:</label>
        <select v-model="filter" class="p-3 border border-gray-400 rounded-md text-gray-900 focus:ring-2 focus:ring-teal-500 focus:border-transparent">
          <option value="All">All</option>
          <option value="Income">Income</option>
          <option value="Expense">Expense</option>
        </select>
      </div>
      <div class="transactions space-y-4">
        <Transaction
          v-for="transaction in filteredTransactions"
          :key="transaction.id"
          :transaction="transaction"
          @edit="editTransaction"
          @delete="deleteTransaction"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Transaction from './components/Transaction.vue';

export default {
  name: 'App',
  components: { Transaction },
  data() {
    return {
      transactions: [],
      newDescription: '',
      newAmount: '',
      newCategory: 'Income',
      filter: 'All',
      error: ''
    };
  },
  computed: {
    totalIncome() {
      return this.transactions
        .filter(t => t.category === 'Income')
        .reduce((sum, t) => sum + t.amount, 0);
    },
    totalExpenses() {
      return this.transactions
        .filter(t => t.category === 'Expense')
        .reduce((sum, t) => sum + t.amount, 0);
    },
    balance() {
      return this.totalIncome - this.totalExpenses;
    },
    filteredTransactions() {
      if (this.filter === 'All') return this.transactions;
      return this.transactions.filter(t => t.category === this.filter);
    }
  },
  methods: {
    addTransaction() {
      this.error = '';
      if (this.newDescription && this.newAmount) {
        if (this.newAmount <= 0) {
          this.error = 'Amount must be positive';
          return;
        }
        this.transactions.push({
          id: Date.now(),
          description: this.newDescription,
          amount: this.newAmount,
          category: this.newCategory
        });
        this.newDescription = '';
        this.newAmount = '';
        this.newCategory = 'Income';
        this.saveToLocalStorage();
      } else {
        this.error = 'Please fill in all fields';
      }
    },
    editTransaction(updatedTransaction) {
      const index = this.transactions.findIndex(t => t.id === updatedTransaction.id);
      if (index !== -1) {
        this.transactions[index] = updatedTransaction;
        this.saveToLocalStorage();
      }
    },
    deleteTransaction(id) {
      this.transactions = this.transactions.filter(t => t.id !== id);
      this.saveToLocalStorage();
    },
    saveToLocalStorage() {
      localStorage.setItem('transactions', JSON.stringify(this.transactions));
    },
    loadFromLocalStorage() {
      const saved = localStorage.getItem('transactions');
      if (saved) {
        this.transactions = JSON.parse(saved);
      }
    }
  },
  mounted() {
    this.loadFromLocalStorage();
  }
};
</script>