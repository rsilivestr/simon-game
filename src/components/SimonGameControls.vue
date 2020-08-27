<template>
  <div class="simon-game__controls game-controls">
    <h2>Round: {{ round }}</h2>
    <button
      :disabled="roundActive || freeBoard"
      class="game-controls__button"
      @click="$emit('start-round')"
    >
      START
    </button>
    <button
      :disabled="noRestart"
      class="game-controls__button"
      @click="$emit('start-again')"
    >
      RESTART
    </button>
  </div>
</template>

<script>
export default {
  name: 'SimonGameControls',
  props: {
    freeBoard: {
      type: Boolean,
      required: true,
    },
    round: {
      type: Number,
      required: true,
    },
    roundActive: {
      type: Boolean,
      required: true,
    },
  },
  computed: {
    noRestart() {
      return this.round === 1 && !this.roundActive;
    },
  },
};
</script>

<style lang="scss" scoped>
.simon-game__controls {
  grid-area: controls;
  text-align: center;

  @media (min-width: 800px) {
    text-align: left;
  }
}

.game-controls {
  &__button {
    font-size: 20px;
  }
}
</style>
