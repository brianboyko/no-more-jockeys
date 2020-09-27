<template>
  <img src="@/assets/horne-big.jpg" v-if="winner === 'HORNE'" />
  <img src="@/assets/watson-big.jpg" v-if="winner === 'WATSON'" />
  <img src="@/assets/key-big.jpg" v-if="winner === 'KEY'" />
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "timer",
  props: ["winner"]
});
</script>

<style lang="scss" scoped>
img {
  max-width: 100%;
}
</style>