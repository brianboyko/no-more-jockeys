<template>
  <div class="home">
    <Entry msg="No More Jockeys" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Entry from "@/components/Entry.vue";
export default defineComponent({
  name: "Home",
  components: {
    Entry
  }
});
</script>
