<template>
  <h2 :class="group.class">
    <AppIcon v-if="groupIcon" :icon="groupIcon" class="group-header-icon" />
    {{ group.name }}
  </h2>
</template>

<script>
import AppIcon from "./AppIcon.vue";

export default {
  name: "GroupHeader",
  components: { AppIcon },
  props: {
    group: {
      type: Object,
      required: true,
    },
  },
  computed: {
    groupIcon() {
      // Backward compat: accept `icon`, `logo`, or `simpleIcon`
      return this.group.icon || this.group.logo || (this.group.simpleIcon ? `si-${this.group.simpleIcon}` : "");
    },
  },
};
</script>

<style scoped>
.group-header-icon {
  margin-right: 0.35rem;
}
h2 :deep(.app-icon) {
  width: 1.2em;
  height: 1.2em;
}
h2 :deep(i) {
  color: var(--accent-color, var(--highlight-primary));
}
</style>
