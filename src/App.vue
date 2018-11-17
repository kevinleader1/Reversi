<template>
  <div id="app">
    <boardGame/>
  </div>
</template>

<script>
  import boardGame from './components/boardGame.vue'

  export default {
    name: 'app',
    components: {
      boardGame
    }
  }
</script>

<style>
  body,html, #app {
    width: 100%;
    height: 100%;
  }

  body,html, #app {
    padding: 0;
    margin: 0
  }
</style>
