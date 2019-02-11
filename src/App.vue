<template>
  <div id="app">
    <p>vue-life-game</p>
    <life-game-table></life-game-table>
  </div>
</template>

<script>
import LifeGameTable from './components/LifeGameBoard'
export default {
  name: 'App',
  components: {
    LifeGameTable
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
