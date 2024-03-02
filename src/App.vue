

<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :totalIncome="+totalIncome" :totalExpense="+totalExpense" />
    <TransactionList
      :transactions="transactions"
      @deleteTransaction="handleTransactionDeletion"
    />
    <AddTransaction @addTransaction="handleTransactionAddition" />
  </div>
</template>


<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import { useToast } from "vue-toastification";

import { ref, computed, onMounted } from "vue";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));
  if (savedTransactions) {
    console.log(savedTransactions);
    transactions.value = savedTransactions;
  }
});

const totalIncome = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2);
});

const totalExpense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2);
});

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

const handleTransactionAddition = (newTransaction) => {
  newTransaction.id = generateUniqueId();
  transactions.value.push(newTransaction);
  saveTransactionsToLocalStorage();
  toast.success("Transaction added successfully");
};

const handleTransactionDeletion = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveTransactionsToLocalStorage();
  toast.success("Transaction deleted successfully");
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>