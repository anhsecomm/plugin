<template>
  <div class="integration-item" v-bind:class="{ 'integration-item--selected': selected }">
    <div class="integration-item__left">
      <img :src="item.image_url" class="integration-item__image">
    </div>
    <div class="integration-item__right">
      <div class="uk-form-text-label">{{item.title}}</div>
      <div class="uk-text-muted">{{truncate(item.description)}}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    current: Object,
    item: Object
  },
  computed: {
    selected() {
      if (typeof this.current === "undefined" || this.current == null)
        return false;
      return this.current.id == this.item.id;
    }
  },
  methods: {
    truncate(string) {
      let maxLength = 50;
      if (string.length <= maxLength) {
        return string;
      }

      let trimmed = string.substring(0, Math.min(maxLength, string.length));
      trimmed = trimmed.substr(
        0,
        Math.min(trimmed.length, trimmed.lastIndexOf(" "))
      );
      return `${trimmed}...`;
    }
  }
};
</script>

