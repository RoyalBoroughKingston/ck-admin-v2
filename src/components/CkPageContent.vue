<template>
  <div>
    <div v-for="[sectionId, section] in sortedContent" :key="sectionId">
      <gov-heading tag="h3" size="m">{{ section.label }}</gov-heading>
      <ck-text-input
        v-if="Object.keys(section).includes('title')"
        :value="section.title"
        @input="onChangeTitle(sectionId, $event)"
        :id="`${sectionId}-title`"
        label="Title"
        type="text"
        :error="errors.get(`content_${sectionId}_title`)"
      />
      <div
        v-for="(content, index) in section.content"
        :key="`${sectionId}-copy-${index}`"
      >
        <ck-wysiwyg-input
          v-if="content.type === 'copy'"
          :value="content.value"
          @input="onChangeCopy(sectionId, 'copy', index, $event)"
          :id="`${sectionId}-copy-${index}`"
          :label="section.label"
          :hint="section.hint"
          :error="errors.get(`content_${sectionId}_copy_${index}`)"
          large
          :maxlength="60000"
        />
        <gov-button type="button" @click="addCopy(sectionId, index)"
          >Add Content</gov-button
        >&nbsp;
        <gov-button
          v-if="index > 0"
          type="button"
          :warning="true"
          @click="removeCopy(sectionId, index)"
          >Remove Content</gov-button
        >
      </div>
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

  data() {
    return {
      contentOrder: {
        introduction: 1,
        about: 2,
        info_pages: 3,
        collections: 4
      }
    };
  },

  computed: {
    sortedContent() {
      return Object.entries(this.content)
        .map(([key, value]) => {
          if (!value.order) {
            value.order = this.contentOrder[key];
          }
          return [key, value];
        })
        .sort((a, b) => {
          return a[1].order - b[1].order;
        });
    }
  },

  methods: {
    onChangeTitle(section, value) {
      const content = Object.assign({}, this.content)

      content[section]['title'] = value

      this.$emit('update', content)
      this.$emit('clear', `content_${section}_title`)
    },
    onChangeCopy(section, index, value) {
      const content = Object.assign({}, this.content)

      content[section]['content'][index] = {
        type: 'copy',
        value: value,
      }

      this.$emit('update', content)
      this.$emit('clear', `content_${section}_copy`)
    },
    addCopy(section, index) {
      const content = Object.assign({}, this.content)

      content[section]['content'].splice(index + 1, 0, {
        type: 'copy',
        value: '',
      })

      this.$emit('update', content)
      this.$emit('clear', `content_${section}_copy`)
    },
    removeCopy(section, index) {
      const content = Object.assign({}, this.content)

      content[section]['content'].splice(index, 1)

      this.$emit('update', content)
      this.$emit('clear', `content_${section}_copy`)
    },
  },
}
</script>

<style lang="scss" scoped></style>
