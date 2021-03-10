<template>
  <div class="position-relative">
    <p style="color: red" @click="toggle()">ICON HERE FOR CLICKING</p>
    <div v-if="visible" class="container-absolute">
      <div v-if="notFound" class="container-fixed">
        DiabloRun character not found.
      </div>
      <div v-else class="container-fixed">
        <div v-for="slot of characterItemSlots" :key="slot">
          <ItemSlot :snapshot="snapshot" :item-slot="slot" />
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.container-relative {
  position: relative;
}

.container-absolute {
  // TODO: position correctly relative to icon
  position: absolute;
  top: 500;
  left: 100px;
}

.container-fixed {
  position: fixed;

  border: 1px solid red;
  background-color: grey;
}
</style>

<script>
import ItemSlot from "./components/ItemSlot.vue";

export default {
  name: "App",
  components: {
    ItemSlot,
  },
  data() {
    return {
      visible: false,
      channelId: process.env.VUE_APP_CHANNEL_ID,
      notFound: false,
      snapshot: null,
      characterItemSlots: [
        "head",
        "amulet",
        "body_armor",
        "primary_left",
        "primary_right",
        "ring_left",
        "ring_right",
        "belt",
        "boots",
        "gloves",
        "secondary_left",
        "secondary_right",
      ],
    };
  },
  created() {
    window.Twitch.ext.onAuthorized(
      ({ channelId }) => (this.channelId = channelId)
    );

    if (process.env.NODE_ENV === 'development') {
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
    },
  },
};
</script>
