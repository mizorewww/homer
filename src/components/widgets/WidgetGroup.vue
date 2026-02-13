<template>
  <div class="widget-group">
    <h2 v-if="group.name" class="widget-group-title">
      <i v-if="group.icon" :class="['fa-fw', group.icon]"></i>
      <SimpleIcon
        v-else-if="group.simpleIcon"
        :slug="group.simpleIcon"
        :name="group.name"
      />
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
  margin-bottom: 0.75rem;
}
</style>
