<template>
  <div>
    <div v-if="item" class="tooltip">
      <img :src="imageSrc" />
      <div class="tooltip-text">
        <h1>
          <span v-if="runeword" class="quality-gold">
            {{ runeword }}<br />
          </span>
          <span :class="`quality-${runeword ? 'socketed' : item.quality}`">
            {{ runeword ? item.base_name : item.name }}
          </span>
        </h1>
        <p
          :class="{ 'quality--red': property.includes('ÿc1') }"
          v-for="property of properties"
          :key="property"
        >
          {{ property.replace(/^ÿc1/, "") }}
        </p>
      </div>
    </div>
    <div v-else class="quality-empty">{{ itemSlot }} empty</div>
  </div>
</template>

<style scoped lang="scss">
.quality-yellow {
  color: #fdfd70;
}

.quality-red {
  color: #e72626;
}

.quality-orange {
  color: #f7c100;
}

.quality-green {
  color: #01fd00;
}

.quality-gold {
  color: #dcc784;
}

.quality-blue {
  color: #7575fd;
}

.quality-socketed {
  opacity: 0.5;
  font-weight: 400;
}

.quality-white {
  color: #fdfdfd;
}

.quality-empty {
  opacity: 0.5;
  color: #fdfdfd;
}
</style>

<script>
import { itemImages } from "@diablorun/diablorun-data";

export default {
  name: "ItemSlot",
  props: {
    snapshot: Object,
    itemSlot: String
  },
  data() {
    return {
      item: null,
      imageSrc: "",
      properties: [],
      runeword: ""
    };
  },
  watch: {
    snapshot: {
      immediate: true,
      handler(snapshot) {
        this.item = snapshot
          ? snapshot.items.find(
              item =>
                item.container === "character" && item.slot === this.itemSlot
            )
          : null;

        if (!this.item) {
          return;
        }

        this.imageSrc =
          itemImages[this.item.name] || itemImages[this.item.base_name];
        this.properties = JSON.parse(this.item.properties);

        const runewordMatch = this.item.name.match(/^(.*?) \[(.*?)\]$/);

        if (runewordMatch) {
          this.runeword = runewordMatch[2];
        } else {
          this.runeword = "";
        }
      }
    }
  }
};
</script>
