<template>
  <div id="app">
    <TableMain />
  </div>
</template>

<script>
import TableMain from './components/tableMain.vue'

export default {
  name: 'App',
  components: {
    TableMain
  }
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
</style>
