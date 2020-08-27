<template>
  <div class="simon-game__field">
    <div class="simon-buttons" @contextmenu.prevent="nextRound">
      <div
        :class="{ active: flashingQuadrant === 1 }"
        class="simon-buttons__button simon-buttons__button--1"
        @mousedown="pressButton(1)"
      ></div>
      <div
        :class="{ active: flashingQuadrant === 2 }"
        class="simon-buttons__button simon-buttons__button--2"
        @mousedown="pressButton(2)"
      ></div>
      <div
        :class="{ active: flashingQuadrant === 3 }"
        class="simon-buttons__button simon-buttons__button--3"
        @mousedown="pressButton(3)"
      ></div>
      <div
        :class="{ active: flashingQuadrant === 4 }"
        class="simon-buttons__button simon-buttons__button--4"
        @mousedown="pressButton(4)"
      ></div>
    </div>
    <div class="simon-says">
      <transition name="bounce">
        <span v-if="simonSays" class="simon-says__what">{{ simonSays }}</span>
      </transition>
    </div>
  </div>
</template>

<script>
const sound1 = require('../../public/assets/1.ogg');
const sound2 = require('../../public/assets/2.ogg');
const sound3 = require('../../public/assets/3.ogg');
const sound4 = require('../../public/assets/4.ogg');

export default {
  name: 'SimonGameField',

  props: {
    freeBoard: {
      type: Boolean,
      required: true,
    },
    roundActive: {
      type: Boolean,
      required: true,
    },
    roundPattern: {
      type: Array,
      required: true,
    },
    timing: {
      type: Number,
      required: true,
    },
  },

  data() {
    return {
      allowInput: false,

      // pattern index which is checked on user input
      checkedIndex: 0,

      // which quadrant is flashing right now
      flashingQuadrant: 0,

      // displayed message
      simonSays: '',

      // setInterval reference
      sequence: null,

      buttonSounds: {
        1: sound1,
        2: sound2,
        3: sound3,
        4: sound4,
      },
    };
  },

  computed: {
    len() {
      return this.roundPattern.length;
    },
  },

  watch: {
    // start round
    roundActive: function (newState, oldState) {
      // if round is active start sequence
      if (newState) {
        let i = 0;

        // start flashing buttons interval
        this.sequence = setInterval(() => {
          // stop on pattern end or user abort
          if (i === this.len - 1 || !this.roundActive) {
            clearInterval(this.sequence);

            // start user input phase
            setTimeout(() => {
              this.startUserInput();
            }, 500);
          }

          // flash button & play sound
          const quadrant = this.roundPattern[i];
          this.flashQuadrant(quadrant);
          this.playSound(quadrant);

          // next iteration
          i += 1;
        }, this.timing);
      } else {
        // clear interval on game reset
        clearInterval(this.sequence);
      }
    },
  },

  methods: {
    /* Show animated message, hide after duration */
    showMessage(message, duration = 500) {
      this.simonSays = message;
      setTimeout(() => {
        this.simonSays = '';
      }, duration);
    },

    /* Check user input against given pattern */
    checkUserInput(input) {
      return input === this.roundPattern[this.checkedIndex];
    },

    flashQuadrant(quadrant) {
      this.flashingQuadrant = quadrant;
      setTimeout(() => {
        this.flashingQuadrant = 0;
      }, 300);
    },

    playSound(quadrant) {
      new Audio(this.buttonSounds[quadrant]).play();
    },

    pressButton(quadrant) {
      if (this.freeBoard) {
        // flash and sound
        this.flashQuadrant(quadrant);
        this.playSound(quadrant);
      }

      // check if input is allowed
      if (this.allowInput) {
        // flash and sound
        this.flashQuadrant(quadrant);
        this.playSound(quadrant);

        // check user input against round pattern
        const match = this.checkUserInput(quadrant);

        if (!match) {
          // exit on wrong button
          this.allowInput = false;
          this.showMessage('Wrong!');
          this.$emit('end-round', false);
        } else if (this.checkedIndex === this.len - 1) {
          // exit on last pattern item
          this.allowInput = false;
          this.showMessage('Good job!');
          this.$emit('end-round', true);
        } else {
          // if pattern has more elements
          this.checkedIndex += 1;
        }
      }
    },

    startUserInput() {
      // allow user input
      this.allowInput = true;
      // reset checked index
      this.checkedIndex = 0;
      this.showMessage('GO!');
    },

    // handy way to start next round
    nextRound() {
      if (!this.roundActive) this.$emit('start-round');
    },
  },
};
</script>

<style lang="scss" scoped>
.simon-game__field {
  margin: 20px 0;
  grid-area: field;
}

.simon-buttons {
  position: relative;
  width: 300px;
  height: 300px;
  margin: auto;

  &__button {
    position: absolute;
    width: 300px;
    height: 300px;
    border-radius: 150px;

    &:hover {
      border: 5px solid #444;
    }

    &--1 {
      clip: rect(0px, 300px, 150px, 150px);
      background-color: #d42;

      &.active {
        background-color: #f64;
      }
    }

    &--2 {
      clip: rect(0px, 150px, 150px, 0);
      background-color: #48c;

      &.active {
        background: #4af;
      }
    }

    &--3 {
      clip: rect(150px, 150px, 300px, 0);
      background-color: #da0;

      &.active {
        background-color: #fd0;
      }
    }

    &--4 {
      clip: rect(150px, 300px, 300px, 150px);
      background-color: #385;

      &.active {
        background-color: #3d5;
      }
    }
  }
}

.simon-says {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 48px;
  margin-top: 24px;

  &__what {
    font-size: 48px;
    font-weight: 700;
    line-height: 1;
    text-align: center;
  }
}

.bounce-enter-active {
  animation: bounce-in 0.25s;
}

.bounce-leave-active {
  animation: bounce-in 0.25s reverse;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  100% {
    transform: scale(1);
  }
}
</style>
