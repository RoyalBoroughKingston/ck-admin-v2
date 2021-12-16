<template>
  <div>
    <ck-loader v-if="loading" />
    <ck-select-input
      v-else
      :value="parent_id"
      @input="onInput('parent_id', $event)"
      id="parent_id"
      label="Parent"
      :options="parentOptions"
      :error="errors.get('parent_id')"
    />

    <ck-text-input
      :value="title"
      @input="onInput('title', $event)"
      id="title"
      label="Title"
      type="text"
      :error="errors.get('title')"
    />

    <ck-wysiwyg-input
      :value="content"
      @input="onInput('content', $event)"
      id="content"
      label="Page content"
      :hint="
        `This is the largest content of the page. Use formatting to improve readability and impact.`
      "
      :error="errors.get('content')"
      large
      :maxlength="3000"
    />

    <ck-image-input
      @input="onInput('image_file_id', $event.file_id)"
      id="image"
      label="Information Page Image"
      :existing-url="existingImageUrl"
    />

    <ck-radio-input
      :value="enabled"
      @input="onInput('enabled', $event)"
      id="enabled"
      label="Is the information page enabled"
      hint="Indicates if the page is enabled or disabled"
      :options="enabledOptions"
      :error="errors.get('enabled')"
    />
  </div>
</template>

<script>
import http from "@/http";
import CkImageInput from "@/components/Ck/CkImageInput";

export default {
  name: "InformationPageForm",

  components: { CkImageInput },

  props: {
    errors: {
      type: Object,
      required: true
    },
    parent_id: {
      required: true
    },
    title: {
      type: String,
      required: true
    },
    content: {
      type: String,
      required: true
    },
    enabled: {
      type: Boolean,
      required: true
    },
    informationPage: {
      type: Object,
      default: null
    },
    image_file_id: {
      default: null
    }
  },

  data() {
    return {
      loading: false,
      informationPages: [],
      enabledOptions: [
        { label: "Enabled", value: true },
        { label: "Disabled", value: false }
      ]
    };
  },

  computed: {
    parentOptions() {
      return [
        { text: "No parent (top level)", value: null },
        ...this.parseInformationPages(
          this.informationPages.filter(page => {
            return !page.parent;
          })
        )
      ];
    },
    imageFileSuffix() {
      return this.informationPage.image
        ? {
            "image/jpeg": "jpg",
            "image/png": "png"
          }[this.informationPage.image.mime_type]
        : null;
    },
    existingImageUrl() {
      return this.informationPage && this.informationPage.image
        ? this.apiUrl(
            `/information-pages/${this.informationPage.id}/image.${this.imageFileSuffix}?v=${this.now}`
          )
        : undefined;
    }
  },

  methods: {
    onInput(field, value) {
      this.$emit(`update:${field}`, value);
      this.$emit("clear", field);
    },
    async fetchInformationPages() {
      this.loading = true;

      const { data } = await http.get("/information-pages/index");
      this.informationPages = data.data;

      this.loading = false;
    },
    parseInformationPages(pages, parsed = [], depth = 0) {
      pages
        .filter(
          page => !this.informationPage || page.id !== this.informationPage.id
        )
        .forEach(page => {
          const text = "-".repeat(depth) + " " + page.title;
          parsed.push({ text, value: page.id });
          const children = this.informationPages.filter(
            child => child.parent && child.parent.id === page.id
          );
          if (children.length > 0 && depth < 4) {
            parsed = this.parseInformationPages(children, parsed, depth + 1);
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

<style lang="scss" scoped></style>
