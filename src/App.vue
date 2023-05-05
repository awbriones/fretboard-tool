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
:root {
  --black-01: #0F0F0F;
  --black-02: #262626;
  --gray-01: #474747;
  --gray-02: #808080;
  --gray-03: #a8a8a8;
  --white-01: #f5f5f5;
  --white-02: #eeeeee;

  /* Highlight colors: */
  --red-primary: #FF6161;
  --orange-primary: #FF9344;
  --yellow-primary: #EDDA31;
  --green-primary: #5E9055;
  --blue-primary: #5297FD;
  --purple-primary: #9664FF;
  --pink-primary: #F559EF;

  /* dimmed colors */
    --red-dimmed: #4B2D2D;
    --orange-dimmed: #4B3728;
    --yellow-dimmed: #494526;
    --green-dimmed: #2D352A;
    --blue-dimmed: #2C374D;
    --purple-dimmed: #362E4E;
    --pink-dimmed: #492D4A;


/* transprent whites */
    --light-24: rgba(255, 255, 255, 0.24);   
    --light-12: rgba(255, 255, 255, 0.12);

/* Slider styles */
  --slider-background-color: #474747;
  --slider-checked-background-color: #474747;
  --color-for-checked-left-label: #f5f5f5;
  --color-for-checked-right-label: #f5f5f5;
}

/* Other global styles */


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
