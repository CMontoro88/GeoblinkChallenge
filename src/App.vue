<template>
  <div id="app">
    <StatesList />
  </div>
</template>

<script>
import StatesList from './components/StatesList'

export default {
  name: 'App',
  components: {
    StatesList
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
