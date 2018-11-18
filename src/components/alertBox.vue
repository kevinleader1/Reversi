<template>
    <div id="alertMessage">
        <p>{{messageToShow[message]}}</p>
    </div>
</template>

<script>
    export default {
        name: 'alertBox',
        props: [
            'message'
        ],
        data(){
            return {
                messageToShow: {
                    start: "Good luck =)",
                    yourTurn: "It's your turn.",
                    computerTurn: "It's computer turn."
                }
            }
        }
    }
</script>

<style lang="less" scoped>
    #alertMessage {
        z-index: 1;
        position: absolute;
        top: -200%;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
        max-height: 100px;
        height: 100%;
        width: 100%;
        max-width: 430px;
        font-size: 18px;
        font-weight: bold;
        font-family: monospace, arial;
        background-color: white;
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 5px;
        border-radius: 5px;
        box-shadow: 2px 2px 13px 2px #5f5f5f;
        transition: 1s top;

        &.visible {
            top: 0;
        }
    }
</style>
