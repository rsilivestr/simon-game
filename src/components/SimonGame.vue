<template>
  <main class="simon-game container">
    <div class="simon-game__info">
      <h1>Simon Says</h1>
      <p>
        Play game repeating button sequence. To start new round press START
        button or right-click on the game field. Difficulty defines delay
        between button flashes: easy - 1.5s, medium - 1s, hard - 0.4s. To reset
        your progress and start new game press RESTART button. Difficulty change
        will also reset the game.
      </p>
    </div>
    <SimonGameField
      :freeBoard="freeBoard"
      :roundActive="roundActive"
      :roundPattern="roundPattern"
      :timing="currentTiming"
      @end-round="endRound"
      @start-round="startRound"
    />
    <SimonGameControls
      :freeBoard="freeBoard"
      :round="round"
      :roundActive="roundActive"
      @start-again="startAgain"
      @start-round="startRound"
    />
    <SimonGameOptons
      :difficulty="difficulty"
      :freeBoard="freeBoard"
      :roundAutostart="roundAutostart"
      @set-difficulty="setDifficulty"
      @toggle-auto-round="roundAutostart = !roundAutostart"
      @toggle-free-board="freeBoard = !freeBoard"
    />
  </main>
</template>

<script>
import SimonGameField from './SimonGameField';
import SimonGameControls from './SimonGameControls';
import SimonGameOptons from './SimonGameOptions';

export default {
  name: 'SimonGame',

  components: {
    SimonGameField,
    SimonGameControls,
    SimonGameOptons,
  },

  data() {
    return {
      difficulty: 'easy',
      freeBoard: false,
      round: 1,
      roundActive: false,
      roundAutostart: false,
      roundPattern: [],
      // timings in ms
      timings: {
        easy: 1500,
        medium: 1000,
        hard: 400,
      },
    };
  },

  computed: {
    currentTiming() {
      return this.timings[this.difficulty];
    },
  },

  methods: {
    generateRoundPattern() {
      this.roundPattern.push(Math.ceil(Math.random() * 4));
    },

    // start new game
    startAgain() {
      // stop current round immediately
      this.roundActive = false;
      // reset state
      this.round = 1;
      this.roundPattern = [];
    },

    startRound() {
      this.generateRoundPattern();
      this.roundActive = true;
    },

    endRound(success) {
      this.roundActive = false;

      if (success) {
        // go to the next round on succsess
        this.round += 1;

        if (this.roundAutostart) {
          // go to the next round automatically
          setTimeout(() => {
            this.startRound();
          }, 1000);
        }
      } else {
        // start over on failure
        this.startAgain();
      }
    },

    setDifficulty(value) {
      this.difficulty = value;

      this.startAgain();
    },
  },
};
</script>

<style lang="scss" scoped>
.simon-game {
  display: grid;
  grid-template: 'info' auto 'controls' auto 'field' auto 'options' auto / auto;
  gap: 20px;
  align-items: start;
  padding-top: 20px;
  padding-bottom: 20px;

  @media (min-width: 800px) {
    grid-template: 'info info' auto 'field controls' auto 'field options' auto / 2fr 1fr;
  }

  &__info {
    grid-area: info;
    font-size: 20px;
    line-height: 1.5;
  }
}
</style>
