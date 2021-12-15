<template>
  <gov-width-container>
    <vue-headful title="Help Yourself Sutton - Add Information Page" />

    <gov-back-link :to="{ name: 'information-pages-index' }"
      >Back to information pages</gov-back-link
    >
    <gov-main-wrapper>
      <information-page-form
        :errors="form.$errors"
        :parent_id.sync="form.parent_id"
        :title.sync="form.title"
        :content.sync="form.content"
        :enabled.sync="form.enabled"
        @clear="form.$errors.clear($event)"
      />
    </gov-main-wrapper>

    <gov-section-break size="l" />

    <gov-button v-if="form.$submitting" disabled type="submit"
      >Creating...</gov-button
    >
    <gov-button v-else @click="onSubmit" type="submit">Create</gov-button>
    <ck-submit-error v-if="form.$errors.any()" />
  </gov-width-container>
</template>

<script>
import InformationPageForm from "./forms/InformationPageForm";
import Form from "@/classes/Form";

export default {
  name: "CreateInformationPage",

  components: {
    InformationPageForm
  },

  data() {
    return {
      form: new Form({
        parent_id: null,
        title: "",
        content: "",
        enabled: false
      })
    };
  },
  methods: {
    async onSubmit() {
      await this.form.post("/information-pages");
      this.$router.push({ name: "information-pages-index" });
    }
  }
};
</script>

<style lang="scss" scoped></style>
