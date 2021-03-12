<template>
  <div class="position-relative">
    <div @click="toggle()" class="icon">
      <img src="./assets/icons/sor.png" />
    </div>
    <div v-if="visible" class="container-absolute">
      <div v-if="notFound" class="container-fixed">
        DiabloRun character not found.
      </div>
      <div v-else class="container-fixed">
        <div class="grid">
          <div class="column" v-for="slot of characterItemSlots" :key="slot">
            <ItemSlot :snapshot="snapshot" :item-slot="slot" />
          </div>
        </div>
        <div class="footer">
          <a
            target="_blank"
            :href="`https://diablo.run/${snapshot.character.user_name}/@`"
          >
            <img src="./assets/dr_logo.png" />
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
body {
  background-color: #333; // Temporary
}

img {
  max-height: 120px;
}

.icon {
  cursor: pointer;
  display: inline-block;

  position: relative;
  top: 25px;
  left: 50px;
}

.icon img {
  border-radius: 50%;
  transition: border-radius 1s;
}

.icon img:hover {
  border-radius: 4px;
  transition: border-radius 0.5s;
}

.container-relative {
  position: relative;
}

.container-absolute {
  // TODO: position correctly relative to icon
  position: absolute;
  top: 33px;
  left: 126px;
}

.container-fixed {
  padding: 6px;
  position: fixed;

  border-radius: 4px;

  background-image: url("./assets/bg.jpg");
}

.footer {
  text-align: center;
}

.footer img {
  margin-top: 12px;
  height: 24px;
}

.grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 6px;
}

.column {
  min-height: 90px;
  max-height: 128px;

  max-height: 128px;
  min-width: 90px;

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
  background-color: black;
  color: #fff;
  text-align: center;
  border-radius: 4px;
  padding: 5px 0;

  white-space: nowrap;


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
    ItemSlot,
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
        "ring_right",
      ],
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
    },
  },
};
</script>
