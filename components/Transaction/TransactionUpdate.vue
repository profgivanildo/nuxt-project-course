<template lang="">
  <div class="my-4 mt-10 space-y-4">
    <div class="grid sm:grid-cols-2 md:grid-cols-4 gap-5">
      <div>
        <AppFormLabel>Data da transação</AppFormLabel>
        <AppFormInput v-model="transactionUpdate.date" type="date" />
      </div>

      <div>
        <AppFormLabel>Valor</AppFormLabel>
        <AppFormInput v-model="transactionUpdate.amount" type="number" />
      </div>

      <div>
        <AppFormLabel>Descrição</AppFormLabel>
        <AppFormInput v-model="transactionUpdate.description" />
      </div>

      <div>
        <AppFormLabel>Categoria</AppFormLabel>
        <AppFormSelect v-model="transactionUpdate.categoryId" :options="categories" />
      </div>
    </div>

    <div class="space-x-4 flex items-center justify-end">
      <a href="" class="inline-flex text-gray-700 text-sm" @click.stop.prevent="onCancel()">
        Cancelar
      </a>

      <AppButton @click="updateTransaction">
        Editar
      </AppButton>
    </div>
  </div>
</template>
<script>
import AppButton from '~/components/Ui/AppButton';
import AppFormInput from '~/components/Ui/AppFormInput';
import AppFormLabel from '~/components/Ui/AppFormLabel';
import AppFormSelect from '~/components/Ui/AppFormSelect';

export default {
  name: 'TransactionUpdate',

  components: {
    AppButton,
    AppFormInput,
    AppFormLabel,
    AppFormSelect,
  },

  props: {
    transaction: {
      type: Object,
      default: () => ({}),
    },
  },

  data() {
    return {
      transactionUpdate: {
        description: this.transaction.description,
        amount: this.transaction.amount,
        date: this.transaction.date,
        categoryId: this.transaction.category.id,
      },
      categories: [],
    }
  },

  async fetch() {
    this.categories = await this.$store.dispatch('categories/getCategories');
  },

  methods: {
    updateTransaction() {
      this.$store.dispatch('transactions/updateTransaction', {id: this.transaction.id, data: this.transactionUpdate})
        .then((response) => {
          this.$emit('update-transaction', {
            ...response, 
            category: this.categories.find(obj => obj.id == this.transactionUpdate.categoryId),
          });
          this.onCancel();
        });
    },
    onCancel() {
      this.$emit('cancel');
    },
  }
}
</script>
<style lang="">
  
</style>