<template>
    <Header />
    <div class="container">
        <Balance :total="+total"/>
        <IncomeExpense :income="+income" :expense="+expense"/>
        <Transaction :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
        <AddTransaction @transactionSubmitted="handleTransactionSumitted"/>
    </div>
</template>

<script setup>
import Header from './component/Header.vue'
import AddTransaction from './component/AddTransaction.vue'
import Balance from './component/Balance.vue'
import IncomeExpense from './component/IncomeExpense.vue'
import Transaction from './component/Transaction.vue'
import {useToast} from 'vue-toastification'
import { ref, computed, onMounted } from 'vue'
onMounted(()=> {
    const savedTransaction = JSON.parse(localStorage.getItem('transactions'))
    if(savedTransaction){
        transactions.value = savedTransaction
    }
})
const toast = useToast()
const transactions = ref([])
const income = computed(()=>{
    return transactions.value
        .filter((transaction) => transaction.amount > 0) 
        .reduce((acc, transaction)=>{
            return acc + transaction.amount
        }, 0)
        .toFixed(2)
    })
const expense = computed(()=>{
        return transactions.value
        .filter((transaction) => transaction.amount <0)
        .reduce((acc, transaction)=>{
            return acc + transaction.amount
        }, 0)
        .toFixed(2)
})
const total = computed(()=>{
        return transactions.value.reduce((acc, transaction)=>{
            return acc + transaction.amount
        }, 0)
    }
)

const handleTransactionSumitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
    })

    saveTransactionsToLocastorage()

    toast.success("Transaction added successfully")
}

const generateUniqueId= () =>{
    return Math.floor(Math.random() * 1000000)
}
const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id != id)
    saveTransactionsToLocastorage()
    toast.success('Transaction deleted')
}

const saveTransactionsToLocastorage = ()=> {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))}
</script>