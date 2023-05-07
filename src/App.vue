<template>
  <div id="app">
    <GuitarFretboard />
  </div>
</template>

<script>
import GuitarFretboard from './components/GuitarFretboard.vue';

export default {
  name: 'App',
  components: {
    GuitarFretboard,
  },
};
</script>

<style lang="scss">
@import '@/styles/variables.scss';

#app {
  font-family: 'Jost', Avenir Next, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  box-sizing: border-box;
}
* {
  font-family: 'Jost', Avenir Next, Helvetica, Arial, sans-serif;
}
</style>
