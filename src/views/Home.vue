<template>
  <div class="home">
    <Display />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Display from "@/components/Display.vue";
export default defineComponent({
  name: "Home",
  components: {
    Display
  }
});
</script>
