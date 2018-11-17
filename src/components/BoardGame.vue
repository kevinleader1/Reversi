<template>
    <div id="bordGame">
        <box v-for="n in 64"></box>
    </div>
</template>

<script>
    import box from './box.vue'

    export default {
        name: 'BoardGame',
        props: ['numberBox'],
        components: {
            box
        },
    }
</script>

<style lang="less" scoped>
    #bordGame{
        display: flex;
        flex-wrap: wrap;
        flex-direction: row;
        align-items: stretch;
        box-sizing: border-box;
        background-color: #32bb85;
        width: 100%;
        max-width: 800px;
        height: 100%;
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
        border: 20px solid #5d290f;
        border-image: repeating-linear-gradient(30deg, #88421D, #85530F, #7E3300 20px) 60;
    }
</style>
