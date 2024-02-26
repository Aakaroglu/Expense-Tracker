<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <TransactionList :transactions = "transactions" 
    @transactionDeleted="handleTransactionDeleted"/>
    <AddTransactionList @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransactionList from './components/AddTransactionList.vue';

  import { useToast } from 'vue-toastification';

  import {ref , computed, onMounted} from 'vue';

  const toast = useToast();


  const transactions = ref([]);
  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

    if(savedTransactions) {
      transactions.value = savedTransactions;
    }
  });

       //Get Total
  const total = computed(() => {  //Bir dizi işlemin toplam miktarını hesaplamak için kullanıyoruz

    return transactions.value.reduce((acc, transaction)=> { //acc toplam değeri temsil eder (accumulator)
      return acc + transaction.amount;
    },0);
  });


  //Get income
  const income = computed(() => {  

return transactions.value
.filter((transaction) => transaction.amount>0)
.reduce((acc, transaction)=>  
   acc + transaction.amount,0).toFixed(2);
});

  //Get expenses
  const expenses = computed(() => {  

return transactions.value
.filter((transaction) => transaction.amount<0)
.reduce((acc, transaction)=>  
   acc + transaction.amount,0).toFixed(2);
});

//Add Transaction

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  saveTransactionsToLocalStorage();


toast.success('Transaction added');
};

//Generate unique ID

const generateUniqueId = () => {
  return Math.floor(Math.random()*1000000);
};

//Delete Transaction

const handleTransactionDeleted = (id) => {
  
    transactions.value = transactions.value.filter((transaction)=> 
    transaction.id !== id); //bunu mutlaka sor


    saveTransactionsToLocalStorage();

    toast.success('Transaction deleted');
  };


  // Save to LocalStorage

  const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
  }

</script>