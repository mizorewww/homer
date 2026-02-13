<template>
  <article class="card widget-card">
    <div class="card-content">
      <p class="widget-title">{{ item.title || "Network" }}</p>
      <div v-if="error" class="widget-error">{{ error }}</div>
      <div v-else-if="!info" class="ip-content">加载中...</div>
      <div v-else class="network-list">
        <div class="network-row">
          <span class="label"><i class="fas fa-network-wired fa-fw"></i> IP</span>
          <span class="value">{{ info.ip || "-" }}</span>
        </div>
        <div class="network-row">
          <span class="label"><i class="fas fa-flag fa-fw"></i> 国家</span>
          <span class="value">{{ info.country || "-" }} ({{ info.country_code || "-" }})</span>
        </div>
        <div class="network-row">
          <span class="label"><i class="fas fa-map-marker-alt fa-fw"></i> 地区</span>
          <span class="value">{{ info.region || "-" }} ({{ info.region_code || "-" }})</span>
        </div>
        <div class="network-row">
          <span class="label"><i class="fas fa-satellite-dish fa-fw"></i> 运营商</span>
          <span class="value">{{ info.isp || info.organization || "-" }}</span>
        </div>
        <div class="network-row">
          <span class="label"><i class="fas fa-building fa-fw"></i> ASN</span>
          <span class="value">AS{{ info.asn || "-" }} {{ info.asn_organization || "" }}</span>
        </div>
        <div class="network-row">
          <span class="label"><i class="fas fa-clock fa-fw"></i> 时区</span>
          <span class="value">{{ info.timezone || "-" }} (UTC{{ utcOffset }})</span>
        </div>
        <div class="network-row">
          <span class="label"><i class="fas fa-compass fa-fw"></i> 坐标</span>
          <span class="value">{{ info.latitude ?? "-" }}, {{ info.longitude ?? "-" }}</span>
        </div>
      </div>
    </div>
  </article>
</template>

<script>
export default {
  name: "IpSkkWidget",
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
      info: null,
      error: "",
    };
  },
  computed: {
    utcOffset() {
      const seconds = Number(this.info?.offset || 0);
      const sign = seconds >= 0 ? "+" : "-";
      const abs = Math.abs(seconds);
      const h = String(Math.floor(abs / 3600)).padStart(2, "0");
      const m = String(Math.floor((abs % 3600) / 60)).padStart(2, "0");
      return `${sign}${h}:${m}`;
    },
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async requestText(url) {
      const res = await fetch(url, {
        headers: {
          Accept: "text/plain,*/*;q=0.8",
        },
      });

      if (!res.ok) {
        throw new Error("INVALID_IP_PROVIDER_RESPONSE");
      }

      return (await res.text()).trim();
    },
    async requestJson(url) {
      const res = await fetch(url, {
        headers: {
          Accept: "application/json,text/plain;q=0.8,*/*;q=0.5",
        },
      });

      if (!res.ok) {
        throw new Error("INVALID_GEO_PROVIDER_RESPONSE");
      }

      return await res.json();
    },
    async fetchData() {
      try {
        const ipUrl = this.provider.ipUrl || this.provider.url || "https://api.ip.sb/ip";
        const backupIpUrl =
          this.provider.backupIpUrl || this.provider.backupUrl || "https://api.ipify.org";
        const geoipUrlBase =
          this.provider.geoipUrlBase || "https://api.ip.sb/geoip/";

        let ip = "";

        try {
          ip = await this.requestText(ipUrl);
        } catch {
          ip = await this.requestText(backupIpUrl);
        }

        try {
          const geo = await this.requestJson(`${geoipUrlBase}${encodeURIComponent(ip)}`);
          this.info = geo;
        } catch {
          this.info = { ip };
        }

        this.error = "";
      } catch {
        this.error = "获取 IP 信息失败";
      }
    },
  },
};
</script>

<style scoped>
.widget-title {
  font-weight: 600;
  margin-bottom: 0.6rem;
}
.ip-content {
  margin: 0;
  white-space: pre-wrap;
  font-size: 0.85rem;
}
.network-list {
  display: grid;
  gap: 0.3rem;
}
.network-row {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  gap: 0.8rem;
}
.label {
  opacity: 0.8;
  white-space: nowrap;
}
.value {
  text-align: right;
  word-break: break-word;
}
.widget-error {
  color: #d03050;
}
</style>
