<template>
  <div id="app">
    <v-app><ReactionBasic/></v-app>
  </div>
</template>

<script>
//import ReactionBasic from './components/ReactionP1A_4.vue'
import ReactionBasic from './components/Home'

export default {
  name: 'App',
  components: {
    //Home
    ReactionBasic
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@300&display=swap');

#app {
  font-family: 'Quicksand', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
