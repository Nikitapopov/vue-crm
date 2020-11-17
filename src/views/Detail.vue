<template>
  <div>
    <Loader v-if="loading"/>

    <div v-else-if="record">
      <div class="breadcrumb-wrap">
        <router-link to="/history" class="breadcrumb">{{'Menu_History' | localize}}</router-link>
        <router-link @click.prevent to="#" class="breadcrumb">
          {{ record.typeText }}
        </router-link>
      </div>
      <div class="row">
        <div class="col s12 m6">
          <div class="card"
               :class="{
            'red': record.type === 'outcome',
            'green': record.type === 'income',
               }"
          >
            <div class="card-content white-text">
              <p>{{'Description' | localize}}: {{ record.descriprion }}</p>
              <p>{{'Amount' | localize}}: {{ record.amount | currency }}</p>
              <p>{{'Category' | localize}}: {{ record.categoryName }}</p>
              <small>{{ record.date | date('datetime') }}</small>
            </div>
          </div>
        </div>
      </div>
    </div>

    <p v-else class="center">
      {{'NoRecord' | localize}}
    </p>
  </div>
</template>

<script>
import localizeFilter from '@/filters/localize.filter';

export default {
  name: 'detail',
  metaInfo() {
    return {
      title: this.$title('ProfileTitle')
    };
  },
  data: () => ({
    record: null,
    loading: true,
  }),
  async mounted() {
    const id = this.$route.params.id;
    const record = await this.$store.dispatch('fetchRecordById', id);
    const category = await this.$store.dispatch('fetchCategoryById', record.categoryId);

    this.record = {
      ...record,
      typeText: record.type === 'income' ? localizeFilter('Income') : localizeFilter('Outcome'),
      categoryName: category.title,
    };

    this.loading = false;
  },
};
</script>