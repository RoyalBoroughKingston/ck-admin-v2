<template>
  <gov-width-container>
    <gov-back-link :to="{ name: 'pages-index' }">Back to pages</gov-back-link>
    <gov-main-wrapper>
      <ck-loader v-if="loading" />
      <gov-grid-row v-else>
        <vue-headful :title="`${appName} - Show Page ${page.title}`" />

        <gov-grid-column width="full">
          <gov-grid-row>
            <gov-grid-column width="two-thirds">
              <gov-heading size="m">
                <gov-caption size="m">{{ page.title }}</gov-caption>
                View page
              </gov-heading>
              <gov-inset-text v-if="updated"
                >page {{ page.title }} has been updated</gov-inset-text
              >
            </gov-grid-column>
            <gov-grid-column
              v-if="auth.canEdit('page', page)"
              width="one-third"
              class="text-right"
            >
              <gov-button
                :to="{ name: 'pages-edit', params: { page: page.id } }"
                >Edit page</gov-button
              >
            </gov-grid-column>
          </gov-grid-row>

          <gov-heading tag="h3" size="m">{{
            pageTypes[page_type]
          }}</gov-heading>

          <gov-heading tag="h2" size="l">{{ page.title }}</gov-heading>

          <page-content :content="page.content" />

          <template v-if="auth.canDelete('page')">
            <gov-section-break size="l" />

            <gov-body
              >Please be certain of the action before deleting a page</gov-body
            >

            <ck-delete-button
              resource="page"
              :endpoint="`/pages/${this.page.id}`"
              @deleted="onDelete"
            />
          </template>
        </gov-grid-column>
      </gov-grid-row>
    </gov-main-wrapper>
  </gov-width-container>
</template>

<script>
import http from "@/http";
import PageContent from "@/views/pages/show/PageContent.vue";

export default {
  name: "ShowPage",

  components: {
    PageContent,
  },

  data() {
    return {
      loading: false,
      page: null,
      updated: false,
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
};
</script>

<style lang="scss" scoped></style>
