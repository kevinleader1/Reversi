<template>
    <div class="box" @click="$parent.activeBox(boxId, 0)">
        <p>{{boxId}}</p>
        <pawn :boxId="boxId"></pawn>
    </div>
</template>

<script>
    import pawn from './pawn.vue'

    export default {
        name: 'box',
        props: ['boxId'],
        components: {
            pawn
        }
    }
</script>

<style lang="less" scoped>
    .box {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 12.5%;
        border: 1px solid #2ba072;
        margin: 0;
        box-sizing: border-box;
        cursor: pointer;

        &:hover .pawn:not(.active) {
             opacity: 0.4;
        }
    }
</style>
