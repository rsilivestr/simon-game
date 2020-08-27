<template>
  <div class="simon-game__options">
    <section class="section">
      <h2>Choose difficulty</h2>
      <!-- Easy -->
      <label class="input-group">
        <input
          class="input-group__input"
          name="difficulty"
          value="easy"
          type="radio"
          :checked="difficulty === 'easy'"
          @change="$emit('set-difficulty', 'easy')"
        />
        <span class="input-group__label">Easy</span>
      </label>
      <!-- Medium -->
      <label class="input-group">
        <input
          class="input-group__input"
          name="difficulty"
          type="radio"
          value="medium"
          :checked="difficulty === 'medium'"
          @change="$emit('set-difficulty', 'medium')"
        />
        <span class="input-group__label">Medium</span>
      </label>
      <!-- Hard -->
      <label class="input-group">
        <input
          class="input-group__input"
          name="difficulty"
          type="radio"
          value="hard"
          :checked="difficulty === 'hard'"
          @change="$emit('set-difficulty', 'hard')"
        />
        <span class="input-group__label">Hard</span>
      </label>
    </section>

    <section class="section">
      <h2>Advanced options</h2>
      <!-- Autostart -->
      <label class="input-group">
        <input
          class="input-group__input"
          type="checkbox"
          :checked="roundAutostart"
          @change="$emit('toggle-auto-round')"
        />
        <span class="input-group__label">Start next round automaticaly</span>
      </label>
      <!-- Free board mode -->
      <label class="input-group">
        <input
          class="input-group__input"
          type="checkbox"
          :checked="freeBoard"
          @change="$emit('toggle-free-board')"
        />
        <span class="input-group__label">I just wanna smash buttons</span>
      </label>
    </section>
  </div>
</template>

<script>
export default {
  name: 'SimonGameOptions',

  props: {
    difficulty: {
      type: String,
      required: true,
    },
    roundAutostart: {
      type: Boolean,
      required: true,
    },
    freeBoard: {
      type: Boolean,
      required: true,
    },
  },
};
</script>

<style lang="scss" scoped>
.simon-game__options {
  grid-area: options;
  display: flex;
  flex-flow: column nowrap;

  @media (min-width: 580px) {
    flex-direction: row;
  }

  @media (min-width: 800px) {
    flex-direction: column;
  }
}

.section + .section {
  @media (min-width: 580px) and (max-width: 799px) {
    margin-left: 40px;
  }
}

.input-group {
  display: flex;
  align-items: center;
  padding: 4px;
  margin-bottom: 8px;

  &__input {
    margin: 0 10px 0 0;
  }

  &__label {
    font-size: 20px;
  }
}
</style>
