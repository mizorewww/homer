<template>
  <article class="card widget-card">
    <div class="card-content">
      <p class="widget-title">{{ item.title || "Crypto" }}</p>
      <div v-if="error" class="widget-error">{{ error }}</div>
      <div v-else>
        <div v-for="row in rows" :key="row.symbol" class="ticker-row">
          <span class="ticker-left">
            <img
              v-if="iconUrlFor(row.symbol)"
              class="coin-icon"
              :src="iconUrlFor(row.symbol)"
              :alt="`${row.symbol} icon`"
              loading="lazy"
            />
            <SimpleIcon
              v-else-if="simpleIconFor(row.symbol)"
              :slug="simpleIconFor(row.symbol)"
              :name="row.symbol"
            />
            {{ row.symbol }}
          </span>
          <span class="ticker-right" :class="priceClass(row.change)">
            {{ formatPrice(row.last) }}
            <small>({{ formatPercent(row.changePercent) }})</small>
          </span>
        </div>
      </div>
    </div>
  </article>
</template>

<script>
import SimpleIcon from "./SimpleIcon.vue";

export default {
  name: "CryptoTickerWidget",
  components: { SimpleIcon },
  props: {
    item: {
      type: Object,
      required: true,
    },
    provider: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      rows: [],
      error: "",
      timer: null,
    };
  },
  mounted() {
    this.fetchData();
    const refreshMs = this.provider.refreshMs || 10000;
    this.timer = setInterval(this.fetchData, refreshMs);
  },
  beforeUnmount() {
    if (this.timer) clearInterval(this.timer);
  },
  methods: {
    iconUrlFor(symbol) {
      return this.item.iconUrls?.[symbol];
    },
    simpleIconFor(symbol) {
      return this.item.simpleIcons?.[symbol];
    },
    async fetchData() {
      try {
        const symbols = this.item.symbols || [];
        const baseUrl = this.provider.baseUrl || "https://www.okx.com";
        const path = this.provider.path || "/api/v5/market/ticker";
        const quote = this.provider.quote || "USDT";

        const data = await Promise.all(
          symbols.map(async (symbol) => {
            const instId = `${symbol}-${quote}`;
            const res = await fetch(`${baseUrl}${path}?instId=${instId}`);
            const json = await res.json();
            const ticker = json?.data?.[0] || {};
            const last = Number(ticker.last || 0);
            const open24h = Number(ticker.open24h || last || 1);
            const change = last - open24h;
            const changePercent = open24h ? (change / open24h) * 100 : 0;
            return { symbol, last, change, changePercent };
          }),
        );

        this.rows = data;
        this.error = "";
      } catch {
        this.error = "获取 OKX 数据失败";
      }
    },
    priceClass(change) {
      return {
        "is-up": change > 0,
        "is-down": change < 0,
      };
    },
    formatPrice(value) {
      return Number(value || 0).toLocaleString(undefined, {
        maximumFractionDigits: 2,
      });
    },
    formatPercent(value) {
      const n = Number(value || 0);
      return `${n > 0 ? "+" : ""}${n.toFixed(2)}%`;
    },
  },
};
</script>

<style scoped>
.widget-title {
  font-weight: 600;
  margin-bottom: 0.6rem;
}
.ticker-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.35rem;
}
.ticker-left {
  display: inline-flex;
  align-items: center;
}
.ticker-right {
  display: inline-flex;
  align-items: baseline;
  gap: 0.25rem;
}
.coin-icon {
  width: 1rem;
  height: 1rem;
  margin-right: 0.45rem;
  vertical-align: text-bottom;
}
.is-up {
  color: #18a058;
}
.is-down {
  color: #d03050;
}
.widget-error {
  color: #d03050;
}
</style>
