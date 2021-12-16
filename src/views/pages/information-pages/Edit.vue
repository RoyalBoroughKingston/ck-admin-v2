<template>
  <gov-width-container>
    <ck-loader v-if="loading" />
    <template v-else>
      <vue-headful
        :title="
          `Help Yourself Sutton - Edit Information Page ${informationPage.title}`
        "
      />

      <gov-back-link :to="{ name: 'information-pages-index' }"
        >Back to information pages</gov-back-link
      >
      <gov-main-wrapper>
        <information-page-form
          :informationPage="informationPage"
          :errors="form.$errors"
          :parent_id.sync="form.parent_id"
          :title.sync="form.title"
          :content.sync="form.content"
          :image_file_id.sync="form.image_file_id"
          :enabled.sync="form.enabled"
          @clear="form.$errors.clear($event)"
        />
      </gov-main-wrapper>

      <gov-section-break size="l" />

      <gov-grid-row>
        <gov-grid-column width="three-quarters">
          <gov-button v-if="form.$submitting" disabled type="submit"
            >Updating...</gov-button
          >
          <gov-button v-else @click="onSubmit" type="submit">Update</gov-button>

          <ck-submit-error v-if="form.$errors.any()" />
        </gov-grid-column>
        <gov-grid-column width="one-quarter">
          <ck-delete-button
            v-if="canDelete"
            resource="information page"
            :endpoint="`/information-pages/${this.informationPage.id}`"
            @deleted="onDelete"
          />
        </gov-grid-column>
      </gov-grid-row>
    </template>
  </gov-width-container>
</template>

<script>
import http from "@/http";
import Form from "@/classes/Form";
import InformationPageForm from "./forms/InformationPageForm";

export default {
  name: "EditInformationPage",

  components: {
    InformationPageForm
  },

  data() {
    return {
      loading: false,
      informationPage: null,
      form: null
    };
  },
  computed: {
    updateButtonText() {
      return this.auth.isGlobalAdmin ? "Update" : "Request update";
    },
    canDelete() {
      return (
        this.auth.isGlobalAdmin && this.informationPage.children.length === 0
      );
    }
  },
  methods: {
    async fetchInformationPage() {
      this.loading = true;

      const response = await http.get(
        `/information-pages/${this.$route.params.informationPage}`
      );
      this.informationPage = response.data.data;
      this.form = new Form({
        title: this.informationPage.title,
        content: this.informationPage.content,
        parent_id: this.informationPage.parent
          ? this.informationPage.parent.id
          : null,
        enabled: this.informationPage.enabled,
        image_file_id: this.informationPage.image
          ? this.informationPage.image.id
          : null
      });

      this.loading = false;
    },
    async onSubmit() {
      await this.form.put(
        `/information-pages/${this.informationPage.id}`,
        (config, data) => {
          // Remove any unchanged values.
          if (data.title === this.informationPage.title) {
            delete data.title;
          }
          if (data.content === this.informationPage.content) {
            delete data.content;
          }
          if (data.parent_id === this.informationPage.parent_id) {
            delete data.parent_id;
          }
          if (data.enabled === this.informationPage.enabled) {
            delete data.enabled;
          }

          // Remove the image from the request if null, or delete if false.
          if (data.image_file_id === null) {
            delete data.image_file_id;
          } else if (
            this.informationPage.image &&
            data.image_file_id === this.informationPage.image.id
          ) {
            delete data.image_file_id;
          } else if (data.image_file_id === false) {
            data.image_file_id = null;
          }
        }
      );
      this.$router.push({
        name: "information-pages-index",
        query: { updated: true }
      });
    },
    onDelete() {
      this.$router.push({
        name: "information-pages-index"
      });
    }
  },
  created() {
    this.fetchInformationPage();
  }
};
</script>

<style lang="scss" scoped></style>
