<template>
  <div>
    <div v-if="item">
      <img :src="imageSrc" />
      <h5>
        <span v-if="runeword" class="quality-gold"> {{ runeword }}<br /> </span>
        <span :class="`quality-${runeword ? 'socketed' : item.quality}`">
          {{ runeword ? item.base_name : item.name }}
        </span>
      </h5>
      <p
        class="mb-0 body-2"
        :class="{ 'error--text': property.includes('ÿc1') }"
        v-for="property of properties"
        :key="property"
      >
        {{ property.replace(/^ÿc1/, "") }}
      </p>
    </div>
    <div v-else>{{ itemSlot }} empty</div>
  </div>
</template>

<style scoped lang="scss">
.quality-yellow {
  color: #fdfd70;
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
</style>

<script>
import { itemImages } from "@diablorun/diablorun-data";

export default {
  name: "ItemSlot",
  props: {
    snapshot: Object,
    itemSlot: String,
  },
  data() {
    return {
      item: null,
      imageSrc: "",
      properties: [],
      runeword: "",
    };
  },
  watch: {
    snapshot: {
      immediate: true,
      handler(snapshot) {
        this.item = snapshot ? snapshot.items.find(
          (item) => item.container === "character" && item.slot === this.itemSlot
        ) : null;

        if (!this.item) {
          return;
        }

        this.imageSrc = itemImages[this.item.name] || itemImages[this.item.base_name];
        this.properties = JSON.parse(this.item.properties);

        const runewordMatch = this.item.name.match(/^(.*?) \[(.*?)\]$/);

        if (runewordMatch) {
          this.runeword = runewordMatch[2];
        } else {
          this.runeword = "";
        }
      },
    },
  },
};
</script>
