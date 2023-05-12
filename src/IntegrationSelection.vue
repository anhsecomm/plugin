<template>
  <div class="integration-selection">
    <div class="uk-flex uk-flex-middle util__grow integration-selection__header">
      <div class="util__grow">
        <form class="uk-margin-right" @submit.prevent="search">
          <input
            @input="debouncedSearch"
            v-model="search_term"
            placeholder="Search"
            class="uk-width-1-1"
          >
        </form>
      </div>
      <div class="uk-position-relative uk-text-nowrap">
        <slot name="actions">
          <a class="uk-button" @click.prevent="close">
            <i class="uk-icon-close"></i> Close Selection
          </a>
        </slot>
      </div>
    </div>
    <div class="spinner" v-if="loading"></div>
    <div class="uk-grid integration-results" v-if="!loading">
      <div
        class="uk-width-large-1-3 uk-width-medium-1-2 uk-margin-bottom"
        v-for="result in response.results"
        :key="result.id"
      >
        <a href="#" class="integration-result" @click.prevent="selectItem(result)">
          <IntegrationItem :item="result" :current="current"></IntegrationItem>
        </a>
      </div>
    </div>
    <IntegrationPagination
      :page="page"
      :totalPages="totalPages"
      :loadPages="loadPages"
      :loading="loading"
    ></IntegrationPagination>
  </div>
</template>

<script>
import axios from 'axios';
import debounce from 'debounce';
import IntegrationItem from "./IntegrationItem";
import IntegrationPagination from "./IntegrationPagination";

export default {
  props: {
    options: Object,
    current: Object,
    select: Function,
    close: Function
  },
  data() {
    return {
      search_term: "",
      per_page: 50,
      page: 1,
      response: {
        results_size: 0,
        results: []
      },
      loading: true
    };
  },
  computed: {
    totalPages() {
      return this.response.results_size != 0
        ? Math.ceil(this.response.results_size / this.per_page)
        : 1;
    },
    requestOptions() {
      let config = { params: { page: this.page, per_page: this.per_page } };
      if (this.search_term.length > 0) {
        config.params.search = this.search_term;
      }
      if (typeof this.options.token !== "undefined") {
        config.auth = {
          username: this.options.token
        };
      }
      return config;
    }
  },
  created() {
    this.load();
  },
  methods: {
    debouncedSearch: debounce(function() {
      this.search();
    }, 300),
    search() {
      this.page = 1;
      this.load();
    },
    load() {
      this.loading = true;
      axios.get(this.options.endpoint, this.requestOptions).then(res => {
        this.response = res.data;
        this.loading = false;
      });
    },
    selectItem(item) {
      this.select(item);
      this.close();
    },
    loadPage(page) {
      this.page = page;
      this.load();
    }
  },
  components: {
    IntegrationItem,
    IntegrationPagination
  }
};
</script>