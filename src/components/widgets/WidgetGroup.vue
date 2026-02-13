<template>
  <div class="widget-group">
    <h2 v-if="group.name" class="widget-group-title">
      <i v-if="group.icon" :class="['fa-fw', group.icon]"></i>
      <span v-else-if="group.simpleIcon" class="group-icon-wrapper">
        <SimpleIcon
          :slug="group.simpleIcon"
          :name="group.name"
          class="group-simple-icon"
        />
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
import SimpleIcon from "./SimpleIcon.vue";
import CryptoTickerWidget from "./CryptoTickerWidget.vue";
import IpSkkWidget from "./IpSkkWidget.vue";

const WIDGET_COMPONENTS = {
  cryptoTicker: CryptoTickerWidget,
  ipSkk: IpSkkWidget,
};

export default {
  name: "WidgetGroup",
  components: {
    SimpleIcon,
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

.widget-group-title i {
  font-size: 1.3rem;
  color: var(--accent-color, var(--highlight-primary));
}

.group-icon-wrapper {
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

.group-simple-icon {
  width: 1.1rem;
  height: 1.1rem;
  margin-right: 0;
}
</style>
