<script>
import { mapActions } from 'vuex';
import allLocations from '../../assets/data/locations';

export default {
  props: {
    showAll: {
      type: Boolean,
      required: false,
      default: false,
    },
    searchable: {
      type: Boolean,
      required: false,
      default: false,
    },
    value: {
      type: String,
      required: false,
      default: null,
    },
  },
  data() {
    return {
      selected: null,
      options: [],
    };
  },
  methods: {
    ...mapActions(['fetchAvailableLocations']),
    handleChange(value) {
      this.$emit('input', value);
    },
    syncValue() {
      if (this.value) {
        this.selected = this.value;
      }
    },
    searchChange(text) {
      if (text.trim() === '') {
        this.options = this.locations;

        return;
      }

      this.options = this.locations.filter(
        l => (l.toLocaleLowerCase('tr').indexOf(text.toLocaleLowerCase('tr')) > -1),
      );
    },
    searchChange(text) {
      this.options = text.trim()
        ? this.locations.filter(l => l.toLocaleLowerCase().search(text.toLocaleLowerCase()) !== -1)
        : this.locations;
    },
  },
  watch: {
    value() {
      // Here's why we use if condition:
      // When a change occurs, the value is emit to up.
      // When the value above changes, we do not want this watcher to work unnecessarily.
      if (this.selected !== this.value) this.syncValue();
    },
  },
  created() {
    this.syncValue();
    this.fetchAvailableLocations()
      .then(() => {
        this.options = this.locations;
      });
  },
};
</script>

<template>
  <multiselect
    :class="{ 'is-searchable': searchable }"
    v-model="selected"
    :options="options"
    :searchable="searchable"
    :close-on-select="true"
    :show-labels="false"
    :internal-search="false"
    placeholder="Şehir seçiniz..."
    @input="handleChange"
    @search-change="searchChange"
  >
    <div slot="noResult">
      Aramanızla eşleşen bir sonuç bulunamadı.
    </div>
  </multiselect>
</template>
