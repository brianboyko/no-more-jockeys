<template>
  <h1>NO MORE JOCKEYS</h1>
  <div class="jockey-entry">
    <table>
      <thead>
        <tr>
          <th>Play #</th>
          <th>Player</th>
          <th>Answer</th>
          <th>No More...</th>
          <th>Status</th>
          <th />
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(log, index) in logs"
          :key="log.entry"
          :class="{ 'light-bg': index % 2 === 0, 'dark-bg': index % 2 === 1 }"
        >
          <td>{{ logs.length - index }}</td>
          <td>
            <little-image :name="log.player" :src="getImage(log.player)" />
          </td>
          <td>{{ log.entry }}</td>
          <td>{{ log.noMoreRule }}</td>
          <td>{{ log.status }}</td>

          <td>
            <div v-if="isChallenged(index)">
              <button @click="cancelChallenge(index)">Cancel</button>
              <button @click="rejectChallenge(index)">Reject</button>
            </div>
            <div v-else>
              <button @click="challenge(index)">Challenge</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
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
        <button @click="nextPlayer">Next</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import LittleImage from "@/components/LittleImage.vue";

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
    "little-image": LittleImage
  },

  computed: {
    isChallenged() {
      return (index: number): boolean =>
        this.logs[index].status === Validity.CHALLENGED;
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
</style>
