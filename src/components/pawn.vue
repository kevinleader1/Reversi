<template>
    <div class="pawn" :class="{'active': this.$parent.$parent.boxItems.activeBox.indexOf(boxId) !== -1, 'black' : this.$parent.$parent.boxItems[this.boxId].color == this.$parent.$parent.boxItems.isBlack, 'white' : this.$parent.$parent.boxItems[this.boxId].color == this.$parent.$parent.boxItems.isWhite}"></div>
</template>

<script>
    export default {
        name: 'pawn',
        props: [
            'boxId'
        ]
    }
</script>

<style lang="less" scoped>
    .pawn {
        height: 70%;
        width: 70%;
        border-radius: 100%;
        opacity: 0;
        transition: 0.4s all;

        &.active {
            display: inline-block;
            opacity: 1;
        }

        &.black {
            transform: rotateY(180deg);
            box-shadow: 0 5px 0 0 #fff;
            background-color: #333;
            display:inline-block;
        }

        &.white {
            transform: rotateY(360deg);
            box-shadow: 0 5px 0 0 #333;
            background-color: #fff;
            display:inline-block;
         }
    }
</style>
