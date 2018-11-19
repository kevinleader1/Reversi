<template>
  <div id="app">
    <boardGame :playerCanPlay="game.playerCanPlay"/>
    <alertBox :class="{'visible' : alertBox.displayMessage}" :message="alertBox.message" />
    <img :class="{'show': !game.playerCanPlay ,'hide' : game.playerCanPlay}" id="yourRobotOpponent" src="./assets/imgs/svg/robot.svg" />
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
    },
    methods: {
      showMessage(message){
        this.alertBox.message = message;

        this.alertBox.displayMessage = true;
      }
    }
  }
</script>

<style lang="less" scoped>
  body,html, #app {
    width: 100%;
    height: 100%;
  }

  body,html, #app {
    padding: 0;
    margin: 0
  }

  #yourRobotOpponent {
    z-index: 1;
    position: absolute;
    left: 0;
    bottom: 0;
    margin: 0;
    height: 25%;
    width: 25%;
    display: inline-block;
    transition: top 1s;

    &.show {
      top: 50px;
    }

    &.hide {
      top: -100%
    }
  }
</style>

<style>
  .text-small {
    font-size: 14px;
  }

  p {
    font-family: monospace, arial;
  }
</style>
