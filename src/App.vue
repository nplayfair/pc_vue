<template>
  <div id="app">
    <PollenCount />
  </div>
</template>

<script>

import PollenCount from './components/PollenCount.vue'

export default {
  name: 'app',
  components: {
    PollenCount
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
