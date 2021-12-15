<template>
  <div>
    <vue-headful title="Help Yourself Sutton - Pages: Information Pages" />

    <gov-grid-row>
      <gov-grid-column width="two-thirds">
        <gov-heading size="l">Information Pages</gov-heading>
        <gov-body>
          From this page, you can edit the information pages available, as well
          as add new ones.
        </gov-body>
      </gov-grid-column>
      <gov-grid-column v-if="auth.isGlobalAdmin" width="one-third">
        <gov-button :to="{ name: 'information-pages-create' }" success expand
          >Add a new information page</gov-button
        >
      </gov-grid-column>
    </gov-grid-row>

    <ck-loader v-if="loading" />
    <gov-grid-row v-else>
      <gov-grid-column width="two-thirds">
        <ck-tree-list
          key="informationPageslist"
          :nodes="informationPages"
          edit="information-pages-edit"
          nodeType="informationPage"
          :bullet="true"
          @moveUp="onMoveUp"
          @moveDown="onMoveDown"
        />
      </gov-grid-column>
    </gov-grid-row>
  </div>
</template>

<script>
import http from "@/http";
import CkTreeList from "@/components/Ck/CkTreeList.vue";

export default {
  name: "ListInformationPages",
  components: {
    CkTreeList
  },
  data() {
    return {
      loading: false,
      informationPages: []
    };
  },
  methods: {
    async fetchInformationPages() {
      this.loading = true;

      const { data } = await http.get("/information-pages/index");
      this.informationPages = data.data.map(informationPage => {
        return {
          label: informationPage.title,
          ...informationPage
        };
      });

      this.loading = false;
    },
    async onMoveUp(informationPage) {
      informationPage.order--;
      await http.put(`/information-pages/${informationPage.id}`, {
        ...informationPage
      });
      this.fetchInformationPages();
    },
    async onMoveDown(informationPage) {
      informationPage.order++;
      await http.put(`/information-pages/${informationPage.id}`, {
        ...informationPage
      });
      this.fetchInformationPages();
    }
  },
  created() {
    this.fetchInformationPages();
  }
};
</script>

<style l CkTreeListang="scss" scoped></style>
