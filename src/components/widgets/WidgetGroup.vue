<template>
  <div class="widget-group">
    <h2 v-if="group.name" class="widget-group-title">
      <span v-if="groupIcon" class="group-icon-slot">
        <AppIcon :icon="groupIcon" class="group-icon" />
      </span>
      {{ group.name }}
    </h2>

    <component
      :is="resolveComponent(item.type)"
      v-for="(item, index) in group.items || []"
      :key="`widget-${index}-${item.type || 'unknown'}`"
      :item="item"
      :provider="providerFor(item)"
    />
  </div>
</template>

<script>
import AppIcon from "../AppIcon.vue";
import CryptoTickerWidget from "./CryptoTickerWidget.vue";
import IpSkkWidget from "./IpSkkWidget.vue";

const WIDGET_COMPONENTS = {
  cryptoTicker: CryptoTickerWidget,
  ipSkk: IpSkkWidget,
};

export default {
  name: "WidgetGroup",
  components: {
    AppIcon,
    CryptoTickerWidget,
    IpSkkWidget,
  },
  props: {
    group: {
      type: Object,
      required: true,
    },
    providers: {
      type: Object,
      default: () => ({}),
    },
  },
  computed: {
    groupIcon() {
      // Backward compat: icon > simpleIcon > logo
      return this.group.icon || (this.group.simpleIcon ? `si-${this.group.simpleIcon}` : "") || this.group.logo || "";
    },
  },
  methods: {
    resolveComponent(type) {
      return WIDGET_COMPONENTS[type] || null;
    },
    providerFor(item) {
      return this.providers?.[item.provider] || {};
    },
  },
};
</script>

<style scoped>
.widget-group-title {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
  font-size: 1.3rem;
}

/* FontAwesome icons in the title */
.widget-group-title :deep(i) {
  font-size: 1.3rem;
  color: var(--accent-color, var(--highlight-primary));
}

/* SimpleIcons & image icons: white circle wrapper */
.group-icon-slot :deep(.app-icon--si),
.group-icon-slot :deep(.app-icon--img) {
  width: 1.1rem;
  height: 1.1rem;
}

.group-icon-slot {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 1.6rem;
  height: 1.6rem;
  border-radius: 50%;
  background: #ffffff;
  flex-shrink: 0;
  box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.06);
}
</style>
