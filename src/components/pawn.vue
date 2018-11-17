<template>
    <div class="pawn" :class="{'active': this.$parent.$parent.pawnItem.activeByDefault.indexOf(boxId) !== -1, 'black' : typeof(this.$parent.$parent.pawnItem[this.boxId]) != 'undefined' && this.$parent.$parent.pawnItem[this.boxId].color == this.$parent.$parent.pawnItem.isBlack, 'white' : typeof(this.$parent.$parent.pawnItem[this.boxId]) != 'undefined' && this.$parent.$parent.pawnItem[this.boxId].color == this.$parent.$parent.pawnItem.isWhite}"></div>
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
        cursor:pointer;

        &.active {
            display: inline;
            opacity: 1;
            cursor: default;
        }

        &.black {
            transform: rotateY(0deg);
            box-shadow: 0px 5px 0px 0px #fff;
            background-color: #333;
        }

        &.white {
            transform: rotateY(180deg);
            box-shadow: 0px 5px 0px 0px #333;
            background-color: #fff;
         }
    }
</style>
