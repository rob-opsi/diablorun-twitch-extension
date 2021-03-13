<template>
  <img :src="icons[`./${hero}.png`]" />
</template>

<script>
const icons = {};
const modules = require.context('../assets/icons', true);

for (const key of modules.keys()) {
  icons[key] = modules(key);
}

export default {
  name: 'HeroIcon',
  props: {
    hero: String
  },
  data() {
    return {
      icons
    };
  }
};
</script>
