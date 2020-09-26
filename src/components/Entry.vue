<template>
  <h3>{{ msg }}</h3>
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
        <tr v-for="(log, index) in reversedLogs" :key="log.entry">
          <td>{{ reversedLogs.length - index }}</td>
          <td>{{ log.player }}</td>
          <td>{{ log.entry }}</td>
          <td>{{ log.noMoreRule }}</td>
          <td>{{ log.status }}</td>

          <td>
            <div v-if="isChallenged(index)">
              <button @click="acceptChallenge(index)">Accept</button>
              <button @click="rejectChallenge(index)">Reject</button>
            </div>
            <div v-else>
              <button @click="challenge(index)">Challenge</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <div></div>
    <div class="entry-area">
      <input v-model="player" />
      <input v-model="entry" />
      <input v-model="noMoreRule" />
      <button @click="submit">Submit</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
enum Validity {
  VALID = "VALID",
  CHALLENGED = "CHALLENGED",
  REJECTED = "REJECTED"
}
interface Play {
  player: string;
  entry: string;
  noMoreRule: string;
  status: Validity;
}
export default defineComponent({
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      player: "",
      entry: "",
      noMoreRule: "",
      logs: [] as Play[]
    };
  },
  computed: {
    isChallenged() {
      return (index: number): boolean =>
        this.logs[index].status === Validity.CHALLENGED;
    },
    reversedLogs(): Play[] {
      return [...this.logs].reverse();
    }
  },
  methods: {
    submit() {
      this.logs.push({
        player: this.player,
        entry: this.entry,
        noMoreRule: this.noMoreRule,
        status: Validity.VALID
      });
      this.clearInputs();
    },
    clearInputs() {
      this.player = "";
      this.entry = "";
      this.noMoreRule = "";
    },
    challenge(index: number) {
      this.logs[index].status = Validity.CHALLENGED;
    },
    acceptChallenge(index: number) {
      this.logs[index].status = Validity.REJECTED;
    },
    rejectChallenge(index: number) {
      this.logs[index].status = Validity.VALID;
    }
  }
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
