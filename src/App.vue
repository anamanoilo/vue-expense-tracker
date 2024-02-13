<template>
  <Header/>
  <div class="container">
  <Balance :total="+total" />
  <IncomeExpenses :income="+income" :expenses="+expenses"/>
  <TransactionList :transactions="transactions"
  @transactionDeleted="handleTransactionDeleted"/>
  <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
})

const toast = useToast();

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
})

// Get income
const income = computed(() => {
  return transactions.value.filter((transaction)=>transaction.amount > 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
})

// Get expenses
const expenses = computed(() => {
  return transactions.value.filter((transaction)=>transaction.amount < 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
})

// Add transaction
const handleTransactionSubmitted = (transaction) => {
  transactions.value.push({
    id: generateId(),
    text: transaction.text,
    amount: transaction.amount
  });

  saveTransactionToLocalStorage();

  toast.success('Transaction added');
}



const generateId = () => {
  return Math.floor(Math.random() * 1000000);
}

// Delete transaction 
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
  console.log('transactions.value:', transactions.value);

  saveTransactionToLocalStorage();

  toast.success('Transaction deleted')

}

// Save to localStorage 
const saveTransactionToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>