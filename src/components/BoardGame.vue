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

            let boxItemsObject = {
                boxItems: {
                    // active array to avoid to foreach all time the activeBox when it not need
                    activeBox: [28, 29, 36, 37],
                    isBlack : 0,
                    isWhite : 1,

                    28: {
                        color: isBlack,
                        active: true,
                    },
                    29: {
                        color: isWhite,
                        active: true,
                    },
                    36: {
                        color: isWhite,
                        active: true,
                    },
                    37: {
                        color: isBlack,
                        active: true,
                    },

                    lines: {
                        x: {},
                        y: {}
                    }
                }
            }

            // Default, all pawn are dark for work with hover preview
            for(let i=1; i <= 64; i++){
                // Only if is not yet set for avoid erasing previous proprities
                if(typeof(boxItemsObject.boxItems[i]) == 'undefined'){
                    boxItemsObject.boxItems[i] = {
                        'color' : boxItemsObject.boxItems.isBlack
                    }
                }

                // We going to set all line x,y,z for don't recalcule all position
                boxItemsObject.boxItems[i].lines = {}

                /*
                 ------ X Line -----
                 -------------------
                 */

                // X is simply a division by full horizontal line (8 box), if result < 1 we are at line 1, if result is > 1 && < 2 we are at line 2 etc..
                // We have to add 1 cause line begin at 0 with this method, and remove float number that not necessary to know the precise positon

                let calculX = (i / 8);
                // If is not int we hae to grow up to indicate the curernt line
                if(!(calculX % 1 === 0)){
                    calculX = Math.trunc(calculX + 1);
                }

                boxItemsObject.boxItems[i].lines.x = calculX;

                // We going to put all number line to array for simplify the read of object and performance (  long to foreach all key with all value )
                if(typeof(boxItemsObject.boxItems.lines.x[boxItemsObject.boxItems[i].lines.x]) == 'undefined'){
                    // If the line don't existe we have to create
                    boxItemsObject.boxItems.lines.x[boxItemsObject.boxItems[i].lines.x] = []
                }

                boxItemsObject.boxItems.lines.x[boxItemsObject.boxItems[i].lines.x].push(i);

                /*
                 ------ Y Line -----
                 -------------------
                 */

                let calculY = i % 8;

                // If the result is zero we are on the final line 8 cause we made the modulo with
                if(calculY == 0){
                    calculY = 8;
                }

                // For vertical line we have to use modulo of one full line to know where we are
                boxItemsObject.boxItems[i].lines.y = calculY ;

                if(typeof(boxItemsObject.boxItems.lines.y[boxItemsObject.boxItems[i].lines.y]) == 'undefined'){
                    boxItemsObject.boxItems.lines.y[boxItemsObject.boxItems[i].lines.y] = []
                }

                // If the line don't existe we have to create
                boxItemsObject.boxItems.lines.y[boxItemsObject.boxItems[i].lines.y].push(i);
            }

            return boxItemsObject;
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
