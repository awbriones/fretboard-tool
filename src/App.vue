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

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
