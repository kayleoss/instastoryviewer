<template>
  <h1>InstaStory Viewer</h1>
  <Main />
  <hr>
  <p>View &amp; download instagram stories anonymously</p>
  <small>InstaStory Viewer is completely free to use and ad-free, however <a href="https://www.paypal.com/donate/?business=TY6AGAGFKLC2U&no_recurring=0&currency_code=CAD" target="_blank">donations are always appreciated</a>.</small>
</template>

<script>
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Main
  },
}
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
h1 {
  color: #277D75;
}
</style>
