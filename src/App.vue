<template>
  <div class="position-relative">
    <p style="color: red" @click="toggle()">ICON HERE FOR CLICKING</p>
    <div v-if="visible" class="container-absolute">
      <div v-if="notFound" class="container-fixed">
        DiabloRun character not found.
      </div>
      <div v-else class="container-fixed">
        <div class="column" v-for="slot of characterItemSlots" :key="slot">
          <ItemSlot :snapshot="snapshot" :item-slot="slot" />
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
body {
  background-color: #ddd;
}

img {
  max-height: 148px;
}

.container-relative {
  position: relative;
}

.container-absolute {
  // TODO: position correctly relative to icon
  position: absolute;
  top: 500;
  left: 100;
}

.container-fixed {
  padding: 6px;
  position: fixed;

  border: 2px solid #111119;
  border-radius: 6px;

  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 6px;

  background-image: url("./assets/bg.jpg");
}

.column {
  height: 150px;
  width: 150px;

  display: grid;
  justify-items: center;
  align-items: center;

  background-color: rgba(28, 28, 44, 0.5);
  box-shadow: 0 2px 1px -1px rgba(0, 0, 0, 0.2), 0 1px 1px 0 rgba(0, 0, 0, 0.14),
    0 1px 3px 0 rgba(0, 0, 0, 0.12);
}

.tooltip {
  position: relative;
  display: inline-block;
}

.tooltip .tooltip-text {
  visibility: hidden;
  width: 120px;
  background-color: black;
  color: #fff;
  text-align: center;
  border-radius: 6px;
  padding: 5px 0;

  /* Position the tooltip */
  position: absolute;
  z-index: 1;
}

.tooltip:hover .tooltip-text {
  visibility: visible;
}
</style>

<script>
import ItemSlot from "./components/ItemSlot.vue";

export default {
  name: "App",
  components: {
    ItemSlot
  },
  data() {
    return {
      visible: false,
      channelId: process.env.VUE_APP_CHANNEL_ID,
      notFound: false,
      snapshot: null,
      characterItemSlots: [
        "primary_left",
        "head",
        "primary_right",
        "secondary_left",
        "body_armor",
        "secondary_right",
        "gloves",
        "belt",
        "boots",
        "ring_left",
        "amulet",
        "ring_right"
      ]
    };
  },
  created() {
    window.Twitch.ext.onAuthorized(
      ({ channelId }) => (this.channelId = channelId)
    );

    if (process.env.NODE_ENV === "development") {
      this.show();
    }
  },
  methods: {
    async toggle() {
      if (this.visible) {
        this.hide();
      } else {
        await this.show();
      }
    },
    async show() {
      await this.sync();
      this.visible = true;
    },
    async hide() {
      this.visible = false;
    },
    async sync() {
      if (!this.channelId) {
        return;
      }

      try {
        const res = await fetch(
          `https://api-1.diablo.run/snapshots/users/${this.channelId}`
        );

        this.snapshot = await res.json();
        this.notFound = false;
      } catch (err) {
        this.notFound = true;
      }
    }
  }
};
</script>
