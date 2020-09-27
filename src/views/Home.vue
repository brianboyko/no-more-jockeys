<template>
  <div class="home">
    <div class="player-list__container" v-if="playOrder.length < 2">
      <div class="player-list">
        <img
          @click="addPlayer('HORNE')"
          v-if="!playOrder.includes('HORNE')"
          src="@/assets/horne-light.png"
        />
        <img
          @click="addPlayer('KEY')"
          v-if="!playOrder.includes('KEY')"
          src="@/assets/key-light.png"
        />
        <img
          @click="addPlayer('WATSON')"
          v-if="!playOrder.includes('WATSON')"
          src="@/assets/watson-light.png"
        />
      </div>
      <h2 class="directions">
        CHOOSE {{ playOrder.length === 0 ? "FIRST" : "SECOND" }} PLAYER
      </h2>
    </div>
    <Game v-else :playOrder="playOrder" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Game from "@/components/Game.vue";
const robotBlipAudio = new Audio(require("@/assets/RobotBlip.mp3"));

export default defineComponent({
  name: "Home",
  components: {
    Game
  },
  data() {
    return {
      playOrder: [] as string[]
    };
  },
  methods: {
    addPlayer(player: string) {
      this.playOrder.push(player);
      robotBlipAudio.play();

      if (this.playOrder.length === 2) {
        this.playOrder = this.playOrder.concat(
          ["HORNE", "KEY", "WATSON"].filter(
            (name: string) => !this.playOrder.includes(name)
          )
        );
      }
    }
  }
});
</script>

<style scoped lang="scss">
.home {
}
.player-list {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  & img {
    &:hover {
      filter: drop-shadow(0px -2px 0px #ffc806)
        drop-shadow(-3px -3px 10px white);
    }
  }
  &__container {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
    align-items: center;
    width: 100%;
    justify-content: center;
  }
}
.directions {
  width: 100%;
  text-align: center;
}
</style>