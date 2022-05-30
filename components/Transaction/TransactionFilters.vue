<template lang="">
  <div class="mt-6 pb-6 flex items-center space-x-4 border-b border-gray-300">
    <div>
      <AppFormLabel>Descrição</AppFormLabel>
      <AppFormInput v-model="filters.description" />
    </div>

    <div>
      <AppFormLabel>Categoria</AppFormLabel>
      <AppFormSelect v-model="filters.categoryId" :options="categories" />
    </div>
  </div>
</template>
<script>
import AppFormInput from '~/components/Ui/AppFormInput';
import AppFormLabel from '~/components/Ui/AppFormLabel';
import AppFormSelect from '~/components/Ui/AppFormSelect';

export default {
  name: "TransactionFilters",

  components: {
    AppFormInput,
    AppFormLabel,
    AppFormSelect,
  },

  data() {
    return {
      filters: {
        description: "",
        categoryId: 0,
      },
      categories: [],
    }
  },

  async fetch() {
    this.categories = await this.$store.dispatch('categories/getCategories');
  },

  watch: {
    filters: {
      deep: true,
      handler() {
        this.$emit('filtering', this.filters);
      },
    }
  },

  methods: {
    
  },
}
</script>
<style lang="">
  
</style>