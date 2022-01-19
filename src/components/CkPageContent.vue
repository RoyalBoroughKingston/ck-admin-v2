<template>
  <div>
    <div v-for="(section, sectionId) in content" :key="sectionId">
      <gov-heading tag="h3" size="m">{{ section.label }}</gov-heading>
      <ck-text-input
        v-if="Object.keys(section).includes('title')"
        :value="section.title"
        @input="onChange(sectionId, 'title', null, $event)"
        :id="`${sectionId}-title`"
        label="Title"
        type="text"
        :error="errors.get(`content_${sectionId}_title`)"
      />
      <ck-wysiwyg-input
        v-for="(copy, index) in section.copy"
        :key="`${sectionId}-copy-${index}`"
        :value="copy"
        @input="onChange(sectionId, 'copy', index, $event)"
        :id="`${sectionId}-copy-${index}`"
        :label="section.label"
        :hint="section.hint"
        :error="errors.get(`content_${sectionId}_title`)"
        large
        :maxlength="60000"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "PageContent",

  props: {
    content: {
      type: Object,
      required: true
    },
    errors: {
      required: true
    }
  },

  methods: {
    onChange(section, field, index, value) {
      const content = Object.assign({}, this.content);

      if (null === index) {
        content[section][field] = value;
      } else {
        content[section][field][index] = value;
      }
      this.$emit("update", content);
      this.$emit("clear", `content_${section}_${field}`);
    }
  }
};
</script>

<style lang="scss" scoped></style>
