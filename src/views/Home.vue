<template>
  <div>
    <div v-if="isMobile">
      <HomeMobile />
    </div>
    <div v-else>
      <HomeDesktop />
    </div>
  </div>
</template>

<script>
import HomeDesktop from "./HomeDesktop.vue";
import HomeMobile from "./HomeMobile.vue";

export default {
  name: "Home",
  components: {
    HomeDesktop,
    HomeMobile,
  },
  computed: {
    isMobile() {
      return screen && screen.width <= 760;
    },
  },
};
</script>

<style lang="scss"></style>
