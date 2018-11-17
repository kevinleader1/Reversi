<template>
    <div class="box">
        <pawn></pawn>
    </div>
</template>

<script>
    import pawn from './pawn.vue'

    export default {
        name: 'box',
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
        cursor:pointer;

        .active {
            cursor:default;
        }
    }
</style>
