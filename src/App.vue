<template>
  <div id="app">
    <boardGame :playerCanPlay="game.playerCanPlay"/>
    <alertBox :class="{'visible' : alertBox.displayMessage}" :message="alertBox.message" />
    <img src="/src/assets/imgs/robot.svg" />
  </div>
</template>

<script>
  import boardGame from './components/boardGame.vue'
  import alertBox from './components/alertBox.vue'

  export default {
    name: 'app',
    components: {
      boardGame,
      alertBox
    },
    data() {
      return {
        alertBox: {
          message: 'start',
          displayMessage: false
        },
        game: {
          playerCanPlay: false,
        }
      }
    },
    mounted() {
      // We going to appar message for start playinf
      var viewThis = this;

      setTimeout(function(){
        viewThis.alertBox.displayMessage = true;

        // send away message after 2 secondes
        setTimeout(function(){
          viewThis.alertBox.displayMessage = false;

          viewThis.game.playerCanPlay = true;
        }, 2500);
      }, 500);
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
