<template>
  <div class="col s12 m6">
    <div>
      <div class="page-subtitle">
        <h4>{{'Create' | localize}}</h4>
      </div>

      <form @submit.prevent="submitHandler">
        <div class="input-field">
          <input id="name"
                 type="text"
                 v-model="title"
                 :class="{invalid: $v.title.$dirty && !$v.title.required}"
          >
          <label for="name">{{'Title' | localize}}</label>
          <span v-if="$v.title.$dirty && !$v.title.required"
                class="helper-text invalid">{{'Message_CategoryTitle' | localize}}</span>
        </div>

        <div class="input-field">
          <input id="limit"
                 type="number"
                 v-model.number="limit"
                 :class="{invalid: $v.limit.$dirty && !$v.limit.minValue}"
          >
          <label for="limit">{{'Limit' | localize}}</label>
          <span v-if="$v.title.$dirty && !$v.limit.minValue"
                class="helper-text invalid"
          >
            {{'Message_MinLength' | localize}} {{ $v.limit.$params.minValue.min }}
          </span>
        </div>

        <button class="btn waves-effect waves-light" type="submit">
          {{'Create' | localize}}
          <i class="material-icons right">send</i>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import M from 'materialize-css';
import {required, minValue} from 'vuelidate/lib/validators';
import category from '@/store/category';

export default {
  data: () => ({
    title: '',
    limit: 10,
  }),
  validations: {
    title: {required},
    limit: {minValue: minValue(10)},
  },
  mounted() {
    M.updateTextFields();
  },
  methods: {
    async submitHandler() {
      if (this.$v.$invalid) {
        this.$v.$touch();
        return;
      }

      try {
        const category = {
          title: this.title,
          limit: this.limit,
        }
        await this.$store.dispatch('createCategory', category);
        this.title = '';
        this.limit = this.$v.limit.$params.minValue.min;
        this.$v.$reset();
        this.$message('Категория была создана');
        this.$emit('created', category);
      } catch (e) {
        console.log(e);
      }
    }
  }
};
</script>