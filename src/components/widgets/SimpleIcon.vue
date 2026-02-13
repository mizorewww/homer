<template>
  <img
    class="simple-icon"
    :src="iconUrl"
    :alt="`${name || slug} icon`"
    loading="lazy"
  />
</template>

<script>
export default {
  name: "SimpleIcon",
  props: {
    slug: {
      type: String,
      required: true,
    },
    name: {
      type: String,
      default: "",
    },
  },
  computed: {
    iconUrl: function () {
      return `https://cdn.simpleicons.org/${this.slug}`;
    },
  },
};
</script>

<style scoped>
.simple-icon {
  width: 1rem;
  height: 1rem;
  vertical-align: text-bottom;
  margin-right: 0.45rem;
}
</style>
