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
        <ck-table-filters @search="onSearch" hide-extra>
          <gov-form-group>
            <gov-label for="filter[title]">Information Page title</gov-label>
            <gov-grid-row>
              <gov-grid-column width="three-quarters">
                <gov-input
                  v-model="filters.title"
                  id="filter[title]"
                  name="filter[title]"
                  type="search"
                />
              </gov-grid-column>
              <gov-grid-column width="one-quarter">
                <gov-button type="button" @click="clearSearch"
                  >Cancel</gov-button
                >
              </gov-grid-column>
            </gov-grid-row>
          </gov-form-group>
        </ck-table-filters>
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
        <gov-list v-if="searching" :bullet="true">
          <li v-for="page in informationPages" :key="page.id">
            {{ page.title }}
            <span v-if="auth.isGlobalAdmin">
              <gov-link
                :to="{
                  name: 'information-pages-edit',
                  params: { informationPage: page.id }
                }"
              >
                Edit
              </gov-link>
            </span>
          </li>
        </gov-list>
        <ck-tree-list
          v-else
          key="informationPageslist"
          :nodes="informationPagesTree"
          edit="information-pages-edit"
          nodeType="informationPage"
          :bullet="true"
          @move-up="onMoveUp"
          @move-down="onMoveDown"
        />
      </gov-grid-column>
    </gov-grid-row>
  </div>
</template>

<script>
import http from "@/http";
import CkTreeList from "@/components/Ck/CkTreeList";
import CkTableFilters from "@/components/Ck/CkTableFilters";

export default {
  name: "ListInformationPages",
  components: {
    CkTreeList,
    CkTableFilters
  },
  data() {
    return {
      loading: false,
      searching: false,
      informationPages: [],
      filters: {
        title: ""
      },
      minSearchPhraseLength: 3
    };
  },
  computed: {
    informationPagesTree() {
      return this.buildInformationPagesTree(
        this.informationPages.filter(page => {
          return !page.parent;
        })
      );
    },
    params() {
      const params = {};
      if (this.filters.title.length > this.minSearchPhraseLength) {
        params["filter[title]"] = this.filters.title;
      }
      return params;
    }
  },
  methods: {
    async fetchInformationPages() {
      this.loading = true;
      const { data } = await http.get("/information-pages/index", {
        params: this.params
      });
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
    },
    async onSearch() {
      if (this.filters.title.length > this.minSearchPhraseLength) {
        this.loading = true;
        this.searching = true;
        await this.fetchInformationPages();
        this.loading = false;
      }
    },
    async clearSearch() {
      this.filters.title = "";
      this.loading = true;
      this.searching = false;
      await this.fetchInformationPages();
      this.loading = false;
    },
    buildInformationPagesTree(pages, parsed = [], depth = 0) {
      pages
        .sort((page1, page2) => {
          return page1.order - page2.order;
        })
        .forEach(page => {
          page.children = this.informationPages.filter(
            child => child.parent && child.parent.id === page.id
          );

          if (depth === 0) {
            parsed.push(page);
          }

          if (page.children.length > 0 && depth < 4) {
            parsed = this.buildInformationPagesTree(
              page.children,
              parsed,
              depth + 1
            );
          }
        });

      return parsed;
    }
  },
  created() {
    this.fetchInformationPages();
  }
};
</script>

<style l CkTreeListang="scss" scoped></style>
