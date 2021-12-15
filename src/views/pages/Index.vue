<template>
  <gov-width-container>
    <vue-headful title="Help Yourself Sutton - Pages" />

    <gov-back-link :to="{ name: 'dashboard' }">Back to dashboard</gov-back-link>

    <gov-main-wrapper>
      <gov-grid-row>
        <gov-grid-column width="full">
          <gov-heading size="xl">Pages</gov-heading>
          <gov-tabs :tabs="tabs" bar :panel="false">
            <gov-section-break size="m" />
            <router-view />
          </gov-tabs>
        </gov-grid-column>
      </gov-grid-row>
    </gov-main-wrapper>
  </gov-width-container>
</template>

<script>
export default {
  name: "Pages",
  data() {
    return {
      tabs: [
        {
          heading: "Information Pages",
          to: { name: "information-pages-index" }
        }
      ]
    };
  }
};
</script>

<style lang="scss" scoped></style>
