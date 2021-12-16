<template>
  <gov-list :bullet="bullet">
    <li v-for="node in nodes" :key="node.id">
      {{ node.label }}
      <span v-if="canEdit">
        <gov-link
          :to="{
            name: edit,
            params: { [nodeType]: node.id }
          }"
        >
          Edit </gov-link
        >&nbsp;
        <gov-link @click="$emit('move-up', node)" v-if="node.order > 0"
          >(Move up)</gov-link
        >&nbsp;
        <gov-link
          @click="$emit('move-down', node)"
          v-if="node.order < nodes.length - 1"
          >(Move down)</gov-link
        >
      </span>
      <slot>
        <ck-tree-list
          v-if="node.children"
          :key="`node.${node.id}.children`"
          :nodes="node.children"
          :edit="edit"
          :nodeType="nodeType"
          :bullet="bullet"
          @move-up="$emit('move-up', $event)"
          @move-down="$emit('move-down', $event)"
        />
      </slot>
    </li>
  </gov-list>
</template>

<script>
export default {
  name: "CkTreeList",

  props: {
    nodes: {
      required: true,
      type: Array
    },
    nodeType: {
      type: String,
      default: ""
    },
    edit: {
      type: String,
      default: ""
    },
    bullet: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    canEdit() {
      return (
        this.edit.length > 0 &&
        this.nodeType.length > 0 &&
        this.auth.isGlobalAdmin
      );
    }
  }
};
</script>

<style lang="scss" scoped></style>
