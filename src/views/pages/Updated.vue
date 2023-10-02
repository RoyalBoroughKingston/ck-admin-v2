<template>
  <gov-width-container>
    <ck-loader v-if="loading" />
    <template v-else>
      <vue-headful :title="`${appName} - Page Updated: ${page.title}`" />

      <gov-back-link :to="{ name: 'pages-show', params: { page: page.id } }"
        >Back to page</gov-back-link
      >
      <gov-main-wrapper>
        <gov-grid-row>
          <gov-grid-column width="one-half">
            <gov-heading size="xl">Update request submitted</gov-heading>
            <gov-body>
              Your update request for this page has been received. It will need
              to be approved by an admin before the changes will be applied.
            </gov-body>

            <gov-button
              :to="{
                name: 'pages-show',
                params: { page: page.id },
              }"
              >Back to page</gov-button
            >
          </gov-grid-column>
        </gov-grid-row>
      </gov-main-wrapper>
    </template>
  </gov-width-container>
</template>

<script>
import http from "@/http";

export default {
  name: "PageUpdatedView",

  data() {
    return {
      loading: false,
      page: null,
    };
  },
  methods: {
    async fetchPage() {
      this.loading = true;
      const response = await http.get(`/pages/${this.$route.params.page}`);
      this.page = response.data.data;
      this.loading = false;
    },
  },
  created() {
    this.fetchPage();
  },
};
</script>

<style lang="scss" scoped></style>
