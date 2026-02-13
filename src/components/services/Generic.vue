<template>
  <div>
    <div class="card" :style="`background-color:${item.background};`">
      <a :href="item.url" :target="item.target" rel="noreferrer">
        <div class="card-content">
          <div :class="mediaClass">
            <slot name="icon">
              <div v-if="itemIcon" class="media-left">
                <figure class="image is-48x48">
                  <AppIcon :icon="itemIcon" :alt="`${item.name} icon`" class="service-icon" />
                </figure>
              </div>
            </slot>
            <div class="media-content">
              <slot name="content">
                <p class="title">{{ item.name }}</p>
                <p v-if="item.quick" class="quicklinks">
                  <a
                    v-for="(link, linkIndex) in item.quick"
                    :key="linkIndex"
                    :style="`background-color:${link.color};`"
                    :href="link.url"
                    :target="link.target"
                    rel="noreferrer"
                  >
                    <span v-if="link.icon"
                      ><i
                        style="font-size: 12px"
                        :class="['fa-fw', link.icon]"
                      ></i
                    ></span>
                    {{ link.name }}
                  </a>
                </p>
                <p v-if="item.subtitle" class="subtitle">
                  {{ item.subtitle }}
                </p>
              </slot>
            </div>
            <slot name="indicator" class="indicator"></slot>
          </div>
          <div v-if="item.tag" class="tag" :class="item.tagstyle">
            <strong class="tag-text">#{{ item.tag }}</strong>
          </div>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
import AppIcon from "../AppIcon.vue";

export default {
  name: "Generic",
  components: { AppIcon },
  props: {
    item: Object,
  },
  computed: {
    mediaClass: function () {
      return { media: true, "no-subtitle": !this.item.subtitle };
    },
    itemIcon: function () {
      // Backward compat: icon > logo > simpleIcon
      return this.item.icon || this.item.logo || (this.item.simpleIcon ? `si-${this.item.simpleIcon}` : "");
    },
  },
};
</script>

<style scoped lang="scss">
.media-left {
  .image {
    display: flex;
    align-items: center;
  }

  img {
    max-height: 100%;
    object-fit: contain;
  }

  // AppIcon: FA icons sized to 32px, images fill container
  .service-icon :deep(i) {
    font-size: 32px;
  }
  .service-icon :deep(.app-icon) {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }
}

a[href=""] {
  pointer-events: none;
  cursor: default;
}

.quicklinks {
  float: right;
  a {
    font-size: 0.75rem;
    padding: 3px 6px;
    margin-left: 6px;
    border-radius: 100px;
    background-color: var(--background);
    z-index: 9999;
    pointer-events: all;
  }
}
</style>
