<template>
  <!-- FontAwesome icon -->
  <i v-if="type === 'fa'" :class="['fa-fw', icon]"></i>
  <!-- SimpleIcons via CDN -->
  <img
    v-else-if="type === 'si'"
    class="app-icon app-icon--si"
    :src="siUrl"
    :alt="`${icon} icon`"
    loading="lazy"
  />
  <!-- Image URL (png / svg / jpeg / etc.) -->
  <img
    v-else-if="type === 'img'"
    class="app-icon app-icon--img"
    :src="icon"
    :alt="alt || 'icon'"
    loading="lazy"
  />
</template>

<script>
/**
 * Universal icon component.
 *
 * Detects icon type from the `icon` string:
 *   - "fas fa-xxx" / "fab fa-xxx" / "far fa-xxx"  →  FontAwesome  <i>
 *   - "si-xxx"                                     →  SimpleIcons  <img> via CDN
 *   - "https://..." / "http://..." / "/" / "./"    →  image URL    <img>
 */
export default {
  name: "AppIcon",
  props: {
    icon: {
      type: String,
      default: "",
    },
    alt: {
      type: String,
      default: "",
    },
  },
  computed: {
    type() {
      const v = (this.icon || "").trim();
      if (!v) return null;
      // FontAwesome: starts with fa- / fas / fab / far / fal / fad
      if (/^fa[sbrld]?\s/.test(v)) return "fa";
      // SimpleIcons: si-<slug>
      if (v.startsWith("si-")) return "si";
      // Image URL: starts with http(s):// or / or ./
      if (/^(https?:\/\/|\/|\.\/)/i.test(v)) return "img";
      // Fallback: treat as FontAwesome class (e.g. "fa-solid fa-house")
      if (v.startsWith("fa-")) return "fa";
      return null;
    },
    siUrl() {
      // strip "si-" prefix → slug
      const slug = this.icon.replace(/^si-/, "");
      return `https://cdn.simpleicons.org/${slug}`;
    },
  },
};
</script>

<style scoped>
.app-icon {
  width: 1em;
  height: 1em;
  vertical-align: text-bottom;
  object-fit: contain;
}
</style>
