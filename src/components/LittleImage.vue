<template>
  <div class="image-box">
    <img class="little-image" :src="src" />
    <div class="little-name">{{ name }}</div>
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "little-image",
  props: {
    src: String,
    name: String
  }
});
</script>

<style scoped lang="scss">
.image-box {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.little-image {
  height: 60px;
  width: 60px;
}
.little-name {
  text-align: center;
  font-weight: 300;
  font-size: 12px;
  letter-spacing: 2px;
}
</style>