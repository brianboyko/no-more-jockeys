<template>
  <h1>NO MORE JOCKEYS</h1>
  <h2><timer-timer /></h2>
  <div class="jockey-entry">
    <div
      v-for="(log, index) in logs"
      :key="log.entry"
      class="display"
      :class="{ 'light-bg': index % 2 === 0, 'dark-bg': index % 2 === 1 }"
    >
      <div>
        <div v-if="isChallenged(index)">
          <button @click="cancelChallenge(index)">Cancel</button>
          <button @click="rejectChallenge(index)">Reject</button>
        </div>
        <div v-else>
          <button @click="challenge(index)">Challenge</button>
        </div>
      </div>
      <div class="display-image">
        <little-image :name="log.player" :src="getImage(log.player)" />
      </div>
      <div class="display-white-yellow">
        <div
          :class="{
            'display-entry__challenged': isChallenged(index),
            'display-entry__rejected': isRejected(index)
          }"
          class="display-entry"
        >
          {{ log.entry }}<span v-if="isChallenged(index)"> - CHALLENGED</span>
          <span v-if="isRejected(index)"> - REJECTED</span>
        </div>
        <div
          class="display-no-more-rule"
          :class="{
            'display-no-more-rule__rejected': isRejected(index)
          }"
        >
          <span v-if="log.noMoreRule === ''">&nbsp;</span>
          <template v-else>
            <span class="no-more">NO MORE </span>{{ log.noMoreRule }}
          </template>
        </div>
      </div>
    </div>

    <div class="bottom-area">
      <div class="entry-area">
        <input v-model="entry" />
        <button @click="submitEntry">Submit</button>
      </div>
      <div class="no-more-rule-area">
        <input v-model="noMoreRule" />
        <button @click="submitNoMoreRule">Submit</button>
      </div>

      <div class="choose-player">
        <img
          v-if="currentPlayer === 'HORNE'"
          class="player highlighted"
          src="@/assets/horne-light.png"
        />
        <img
          v-else
          class="player"
          src="@/assets/horne-transparent.png"
          @click="choosePlayer('HORNE')"
        />
        <img
          v-if="currentPlayer === 'KEY'"
          class="player highlighted"
          src="@/assets/key-light.png"
        />
        <img
          v-else
          class="player"
          src="@/assets/key-transparent.png"
          @click="choosePlayer('KEY')"
        />
        <img
          v-if="currentPlayer === 'WATSON'"
          class="player highlighted"
          src="@/assets/watson-light.png"
        />
        <img
          v-else
          class="player"
          src="@/assets/watson-transparent.png"
          @click="choosePlayer('WATSON')"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import LittleImage from "@/components/LittleImage.vue";
import Timer from "@/components/Timer.vue";

enum Validity {
  VALID = "VALID",
  CHALLENGED = "CHALLENGED",
  REJECTED = "REJECTED"
}

enum Player {
  KEY = "KEY",
  WATSON = "WATSON",
  HORNE = "HORNE",
  none = "NONE"
}
interface Play {
  player: Player;
  entry: string;
  noMoreRule: string;
  status: Validity;
}

const robotBlipAudio = new Audio(require("@/assets/RobotBlip.mp3"));
const robotBlip2Audio = new Audio(require("@/assets/RobotBlip2.mp3"));

export default defineComponent({
  name: "HelloWorld",
  props: ["playOrder"],
  data() {
    return {
      playCount: 0,
      entry: "",
      noMoreRule: "",
      logs: [] as Play[]
    };
  },
  components: {
    "little-image": LittleImage,
    "timer-timer": Timer
  },

  computed: {
    isChallenged() {
      return (index: number): boolean =>
        this.logs[index].status === Validity.CHALLENGED;
    },
    isRejected() {
      return (index: number): boolean =>
        this.logs[index].status === Validity.REJECTED;
    },
    getImage() {
      return (p: Player) => require(`@/assets/${p.toLowerCase()}-little.png`);
    },
    currentPlayer(): Player {
      return this.playOrder[this.playCount % this.playOrder.length];
    }
  },
  methods: {
    submitEntry() {
      this.logs.unshift({
        player: this.currentPlayer,
        entry: this.entry,
        noMoreRule: this.noMoreRule,
        status: Validity.VALID
      });
      robotBlip2Audio.play();
      this.entry = "";
      this.noMoreRule = "";
    },
    submitNoMoreRule() {
      const lastLog = this.logs[0];
      lastLog.noMoreRule = this.noMoreRule;
    },
    challenge(index: number) {
      this.logs[index].status = Validity.CHALLENGED;
    },
    cancelChallenge(index: number) {
      this.logs[index].status = Validity.VALID;
    },
    rejectChallenge(index: number) {
      this.logs[index].status = Validity.REJECTED;
    },
    choosePlayer(player: Player | string) {
      const index = this.playOrder.indexOf(player);
      this.playCount = index;
      robotBlipAudio.play();
    },
    nextPlayer() {
      this.playCount += 1;
      robotBlipAudio.play();
    }
  }
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h1 {
  font-weight: 300;
  letter-spacing: 10px;
  text-align: center;
  font-size: 50px;
}
table {
  width: 100%;
  border-collapse: collapse;
}
.highlighted {
  filter: drop-shadow(0px -2px 0px #ffc806) drop-shadow(-3px -3px 10px white);
}
.bottom-area {
  position: fixed;
  bottom: 0;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.8);
}
.choose-player {
  width: 100%;
  display: flex;
  justify-content: space-evenly;
}
.light-bg {
  background-color: rgba(0, 0, 0, 0.3);
}
.dark-bg {
  background-color: rgba(0, 0, 0, 0.5);
}
.display {
  width: 100%;
  display: flex;
  flex-direction: row;
}
.display-image {
  margin: 2px;
  margin-right: 20px;
}
.display-white-yellow {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}
.display-entry {
  background-color: #ffc806;
  width: 100%;
  color: black;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 2px;
  &__challenged {
    & span {
      font-weight: 300;
      letter-spacing: 0;
      text-decoration: italic;
      color: #ff0032;
    }
  }
  &__rejected {
    background-color: #ff0032;
    color: white;
  }
}
.display-no-more-rule {
  background-color: white;
  width: 100%;
  color: black;
  text-transform: uppercase;
  font-size: 20px;
  font-weight: 600;
  letter-spacing: 2px;
  &__rejected {
    color: #ff0032;
  }
  &.no-more {
    font-weight: 300;
  }
}
</style>
