<template>
  <div @click="toggleTimer">{{ timeLeft }}</div>
  <button @click="resetClock">Reset</button>
</template>

<script lang="ts">
import { defineComponent } from "vue";

let internalClock: number;

export default defineComponent({
  name: "timer",
  data() {
    return {
      isRunning: false,
      timeLeft: 60
    };
  },
  created() {
    internalClock = setInterval(() => {
      if (this.isRunning && this.timeLeft > 0) {
        this.timeLeft = Math.max(0, this.timeLeft - 1);
      }
    }, 1000);
  },
  beforeUnmount() {
    clearInterval(internalClock);
  },
  methods: {
    toggleTimer() {
      this.isRunning = !this.isRunning;
    },
    resetClock() {
      this.timeLeft = 60;
    }
  }
});
</script>