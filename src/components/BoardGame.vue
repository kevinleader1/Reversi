<template>
    <div id="bordGame">
        <box v-for="n in 64" :boxId="n"></box>
    </div>
</template>

<script>
    import box from './box.vue'

    export default {
        name: 'BoardGame',
        components: {
            box
        },
        // We need to init configuration in bordGame once time to not repeat data
        data: function(){

            let isBlack = 0, isWhite = 1;

            return {
                pawnItem: {
                    activeByDefault: [28, 29, 36, 37],
                    isBlack : 0,
                    isWhite : 1,

                    28: {
                        color: isBlack
                    },
                    29: {
                        color: isWhite
                    },
                    36: {
                        color: isBlack
                    },
                    37: {
                        color: isWhite
                    }
                }
            }
        }
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
