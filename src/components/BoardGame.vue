<template>
  <div id="bordGame">
div   <case  />
  </div>
</template>

<script>

    import boardGame from './components/boardGame.vue'

    export default {
        name: 'BoardGame',
        data: {
            message: 'Bonjour'
        }
    }
</script>

<style lang="less" scoped>
  #bordGame{
      background-color: #32bb85;
      width: 100%;
      height: 100%;
      box-sizing: border-box;
      position: fixed;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      margin: auto;
      border: 30px solid #5d290f;
  }
</style>
