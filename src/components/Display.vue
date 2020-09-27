<template>
  <h1>NO MORE JOCKEYS</h1>
  <div>
    <div class="jockey-entry">
      <div class="display-image">
        <little-image :name="currentPlayer" :src="getImage(currentPlayer)" />
      </div>
      <div class="jockey-editable">
        <div class="display-white-yellow">
          <input class="display-entry__edit" v-model="entry" />
          <div class="display-no-more-rule">
            NO MORE
            <input class="display-no-more-rule__edit" v-model="noMoreRule" />
          </div>
        </div>
      </div>
      <div class="accept-reject">
        <img
          @click="submitEntry"
          class="accept-button"
          src="@/assets/accept.svg"
        />
        <img
          @click="rejectEntry"
          class="reject-button"
          src="@/assets/reject.svg"
        />
      </div>
    </div>
    <hr />
    <div
      v-for="(log, index) in logs"
      :key="log.entry"
      class="display"
      :class="{ 'light-bg': index % 2 === 0, 'dark-bg': index % 2 === 1 }"
    >
      <!-- <div>
        <div v-if="isChallenged(index)">
          <button @click="cancelChallenge(index)">Cancel</button>
          <button @click="rejectChallenge(index)">Reject</button>
        </div>
        <div v-else>
          <button @click="challenge(index)">Challenge</button>
        </div>
      </div> -->
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
            NO MORE <span class="bold">{{ log.noMoreRule }}</span></template
          >
        </div>
      </div>
    </div>

    <div class="bottom-area">
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
// const robotBlip2Audio = new Audio(require("@/assets/RobotBlip2.mp3"));
const acceptAudio = new Audio(require("@/assets/accept.wav"));
const rejectAudio = new Audio(require("@/assets/reject.wav"));

export default defineComponent({
  name: "HelloWorld",
  props: ["playOrder"],
  data() {
    return {
      playCount: 0,
      entry: "",
      noMoreRule: "",
      logs: [] as Play[],
      currentIsChallenged: false,
      currentIsRejected: false
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
    editEntry(event: any) {
      console.log(event);
      this.entry = event.target.innerText;
    },
    editNoMoreRule(event: any) {
      console.log(event);
      this.noMoreRule = event.target.innerText;
    },
    submitEntry() {
      this.logs.unshift({
        player: this.currentPlayer,
        entry: this.entry,
        noMoreRule: this.noMoreRule,
        status: Validity.VALID
      });
      acceptAudio.play();
      this.entry = "";
      this.noMoreRule = "";
      this.nextPlayer();
    },
    rejectEntry() {
      this.logs.unshift({
        player: this.currentPlayer,
        entry: this.entry,
        noMoreRule: this.noMoreRule,
        status: Validity.REJECTED
      });
      rejectAudio.play();
      this.entry = "";
      this.noMoreRule = "";
      this.nextPlayer();
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
.bold {
  font-weight: 600;
  margin-left: 6px;
}
.jockey-entry {
  display: flex;
  flex-direction: row;
  width: 100%;
  align-items: center;
}
.jockey-editable {
  width: 100%;
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
  min-width: 75px;
  text-align: center;
}
.display-white-yellow {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}
.accept-reject {
  display: flex;
  flex-wrap: no-wrap;
  flex-direction: row;
  margin: 0 25px;
  align-items: center;
  justify-content: flex-end;
}
.accept-button {
  height: 50px;
  width: 50px;
  filter: drop-shadow(0px 0px 10px white);
  cursor: pointer;
  &:hover {
    cursor: pointer;
    filter: drop-shadow(0px 0px 10px white) brightness(0) invert(1);
  }
}
.reject-button {
  height: 50px;
  width: 50px;
  filter: drop-shadow(0px 0px 10px white);
  cursor: pointer;
  &:hover {
    cursor: pointer;
    filter: drop-shadow(0px 0px 10px white) brightness(0) invert(1);
  }
}
.display-entry {
  background-color: #ffc806;
  width: 100%;
  box-sizing: border-box;
  color: black;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 2px;
  padding: 0px 5px;
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
.display-entry__edit {
  background-color: #ffc806;
  width: 100%;
  color: black;
  text-transform: uppercase;
  font-weight: 600;
  letter-spacing: 2px;
  border: 0;
  box-sizing: border-box;
  padding: 5px 5px;
  font-size: 20px;
  letter-spacing: 2px;
}
.display-no-more-rule {
  display: flex;
  flex-direction: row;
  box-sizing: border-box;
  padding: 0px 5px;
  justify-content: flex-start;
  align-items: center;
  background-color: white;
  width: 100%;
  color: black;
  text-transform: uppercase;
  font-size: 20px;
  font-weight: 300;
  letter-spacing: 2px;
  cursor: default;
  &__rejected {
    color: #ff0032;
  }
  &.no-more {
    font-weight: 300;
  }
  &__edit {
    font-size: 20px;
    padding: 0;
    margin: 0;
    flex-grow: 1;
    margin-left: 10px;
    font-weight: 600;
    box-sizing: border-box;
    border: 0;
    text-transform: uppercase;
    letter-spacing: 2px;

    cursor: text;
  }
}
</style>
