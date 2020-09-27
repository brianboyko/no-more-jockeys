<template>
  <div class="winner-container">
    <img
      class="winner-image"
      src="@/assets/horne-big.jpg"
      v-if="winner === 'HORNE'"
    />
    <img
      class="winner-image"
      src="@/assets/watson-big.jpg"
      v-if="winner === 'WATSON'"
    />
    <img
      class="winner-image"
      src="@/assets/key-big.jpg"
      v-if="winner === 'KEY'"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "winner",
  props: ["winner"]
});
</script>

<style lang="scss" scoped>
img {
  max-width: 100%;
}
.winner-image {
  object-fit: contain;
  height: 50vh;
}
.winner-container {
  max-height: 50vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>