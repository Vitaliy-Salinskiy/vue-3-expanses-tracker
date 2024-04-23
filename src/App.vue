<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

const total = computed(() => {
  return transactions.value.reduce(
    (acc, transaction) => acc + transaction.amount,
    0
  );
});

const income = computed(() =>
  transactions.value
    .filter((t) => t.amount > 0)
    .reduce((acc, t) => acc + t.amount, 0)
    .toFixed(2)
);

const expanses = computed(() =>
  transactions.value
    .filter((t) => t.amount < 0)
    .reduce((acc, t) => acc + t.amount, 0)
    .toFixed(2)
);

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: new Date().getTime(),
    ...transactionData,
  });

  saveToLocalStorage();
  toast.success("Transaction added successfully");
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((t) => t.id !== id);

  saveToLocalStorage();
  toast.success("Transaction deleted successfully");
};

const saveToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expanses="+expanses" />
    <TransactionList
      :transactions="transactions"
      @transaction-deleted="handleTransactionDeleted"
    />
    <AddTransaction @transaction-submitted="handleTransactionSubmitted" />
  </div>
</template>
