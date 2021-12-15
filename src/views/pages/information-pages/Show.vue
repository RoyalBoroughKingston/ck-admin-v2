<template>
  <gov-width-container>
    <vue-headful
      :title="
        `Help Yourself Sutton - Show Information Page ${informationPage.title}`
      "
    />

    <gov-back-link :to="{ name: 'information-pages-index' }"
      >Back to information pages</gov-back-link
    >
    <gov-main-wrapper></gov-main-wrapper>
  </gov-width-container>
</template>

<script>
export default {
  name: "ShowInformationPage",

  props: {
    informationPage: {
      type: Object,
      required: true
    }
  }
};
</script>

<style lang="scss" scoped></style>
