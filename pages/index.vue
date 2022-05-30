<template>
  <div>

    <div class="flex items-center justify-between">
      <h1 class="font-bold text-2xl">
        Transações
      </h1>

      <AppButton v-if="!showAdd" @click="showAdd = !showAdd">
        Nova transação
      </AppButton>
    </div>

    <transaction-filters @filtering="onFilter" />
 
    <TransactionAdd v-if="showAdd" @cancel="showAdd = false" @after-add="afterAddTransaction" />

    <div class="mt-4" >
      <div class="space-y-8">
        <div v-for="(group, index) in transactionsComputed" :key="index">
          <div class="mb-1">
            <div class="font-bold text-sm">
              {{ formatDate(index) }}
            </div>
          </div>
          <div class="space-y-3">
            <Transaction v-for="transaction in group" :key="transaction.id" :transaction="transaction" @update-transaction="onUpdateTransaction" />
          </div>
        </div>
      </div>
    </div>
      
  </div>
</template>

<script>
import { groupBy, orderBy, filter } from 'lodash';
import AppButton from '~/components/Ui/AppButton';
import AppFormInput from '~/components/Ui/AppFormInput';
import AppFormLabel from '~/components/Ui/AppFormLabel';
import AppFormSelect from '~/components/Ui/AppFormSelect';
import Transaction from '~/components/Transaction/Transaction.vue';
import TransactionAdd from '~/components/Transaction/TransactionAdd';
import TransactionFilters from '~/components/Transaction/TransactionFilters';
import categoriesVue from './categories.vue';

export default {
  name: 'IndexPage',

  components: {
    AppButton,
    AppFormInput,
    AppFormLabel,
    AppFormSelect,
    Transaction,
    TransactionAdd,
    TransactionFilters,
  },

  async asyncData({ store }) {
    return {
      transactions: await store.dispatch('transactions/getTransactions'),
      transactionsRaw: await store.dispatch('transactions/getTransactions'),
    }
  },

  data() {
    return {
      showAdd: false,
      form: {
        date: "",
        amount: "",
        description: "",
        categoryId: "",
      },
      transactionsRaw: [],
    }
  },

  computed: {
    transactionsComputed() {
      return groupBy(orderBy(this.transactions, 'date', 'desc'), 'date');
    },
  },

  methods: {
    formatDate(date) {
      return this.$dayjs(date).format('DD, MMM - YYYY');
    },
    afterAddTransaction(transaction) {
      this.transactions.push(transaction);
      console.log(transaction);
    },
    onUpdateTransaction(transaction) {
      // Encontrar o index da transaction alterada
      const index = this.transactions.findIndex(obj => obj.id == transaction.id);

      // Remover a transaction antiga e inserir a nova
      this.transactions.splice(index, 1, transaction);
    },
    onFilter(filters) {
      // Filtrar a partir do categoryId
      if(filters.categoryId != "" && filters.categoryId != 0) {
        this.filterCategory(filters);
      }
      // Filtrar a partir do description
      if(filters.description != "") {
        this.filterDescription(filters);
      }

      // Filtrar a partir do description e categoryId
      if( (filters.categoryId != "" && filters.categoryId != 0) && (filters.description != "") ) {
        var stringFilter = filters.description.toString().toLowerCase();
        this.transactions = this.transactionsRaw.filter(function(element) {
          return ( (element.description.toString().toLowerCase().includes(stringFilter) == true) && (element.categoryId == filters.categoryId) )
        });
      }

      // Zerar os filtros
      if( (filters.categoryId == "" || filters.categoryId == 0) && (filters.description == "") ) {
        this.transactions = this.transactionsRaw;
      }
    },

    filterCategory(filters){
      this.transactions = this.transactionsRaw.filter(function(element) {
        return element.categoryId == filters.categoryId
      });
    },
    filterDescription(filters) {
      var stringFilter = filters.description.toString().toLowerCase();
      this.transactions = this.transactionsRaw.filter(function(element) {
        return element.description.toString().toLowerCase().includes(stringFilter) == true
      });
    }
  }
}
</script>
