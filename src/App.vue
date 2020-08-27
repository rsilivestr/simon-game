<template>
  <SimonGame />
</template>

<script>
import SimonGame from './components/SimonGame';

export default {
  name: 'App',

  components: {
    SimonGame,
  },
};
</script>

<style lang="scss">
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  min-height: 100vh;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}

.container {
  width: 100%;
  max-width: 1024px;
  padding: 0 20px;
  margin: 0 auto;
}
</style>
