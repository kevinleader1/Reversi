<template>
    <div class="box" @click="activeBox(boxId, 0)">
        {{boxId}}
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
        },
        methods: {
            activeBox(boxId, idColorPlayer) {
                // We going to start if we can play in this box
                let canWePlayBox = this.canWePlayBox(boxId, idColorPlayer);

                if(canWePlayBox.weCanPlay){
                    // We going to switch box in boxSwitch if it's the player
                }
            },
            canWePlayBox(boxId, idColorPlayer){

                let canWePlayBox = {
                    weCanPlay: false,
                    boxSwitch: []
                };

                let boxSwitch = [];

                if(typeof(boxId) != 'undefined'){

                    let boxItems = this.$parent.boxItems;

                    // If is not a yet item active
                    if(boxItems.activeBox.indexOf(boxId) == -1){
                        // We need to check if box have another box around to have the possibility to play

                        /*
                        ---- Start with X verification ---
                        ----                           ---
                         */

                        let canWePLayX = this.canWePlayBoxX(boxId,idColorPlayer, boxItems);

                        if(canWePLayX.weCanPlay){
                            canWePlayBox.weCanPlay = true;
                            boxSwitch = boxSwitch.concat(canWePLayX.boxSwitch);
                        }

                        /*
                         ---- Start with Y verification ---
                         ----                           ---
                         */

                        let canWePLayY = this.canWePlayBoxY(boxId,idColorPlayer, boxItems);

                        if(canWePLayY.weCanPlay){
                            canWePlayBox.weCanPlay = true;
                            boxSwitch = boxSwitch.concat(canWePLayY.boxSwitch);
                        }
                    }

                } else {
                    console.error('Error, no boxId selected');
                }

                return canWePlayBox;
            },
            canWePlayBoxX(boxId,idColorPlayer, boxItems){

                let canWePlayBoxX = {
                    weCanPlay: false,
                    boxSwitch: [],
                    boxToStop: 0
                };

                let wePossibilityCanPlay = false;

                // Check in X line is easy, we have just to add 1 and -1 to verify is box is active or not
                if(boxItems.activeBox.indexOf(boxId - 1) !== -1){
                    // We can play only if is not the same color that we play 0 is player 1 computer
                    if(boxItems[boxId - 1].color != idColorPlayer){
                        wePossibilityCanPlay = true;
                    }
                } else if (boxItems.activeBox.indexOf(boxId + 1) !== -1) {
                    if(boxItems[boxId + 1].color != idColorPlayer){
                        wePossibilityCanPlay = true;
                    }
                }

                if(wePossibilityCanPlay){

                    boxItems.lines.x[boxItems[boxId].lines.x].forEach(function(boxInLineX){
                        // We need to find an item active AND that is the current color player
                        if(boxItems.activeBox.indexOf(boxInLineX) !== -1 && boxItems[boxInLineX].color === idColorPlayer){
                            // Only one is need for can play
                            canWePlayBoxX.weCanPlay = true;
                            canWePlayBoxX.boxToStop = boxInLineX;
                        }
                    });

                    // If we can play we going to add in array the box that will be switch
                    if(canWePlayBoxX.weCanPlay){
                        // We make a for to parcours the smaller to the biggest
                        let biggestItem = Math.max(canWePlayBoxX.boxToStop, boxId),
                            smallerItem = Math.min(canWePlayBoxX.boxToStop, boxId),
                            nextItem = smallerItem + 1;

                        for(nextItem; nextItem < biggestItem; nextItem++){
                            canWePlayBoxX.boxSwitch.push(nextItem);
                        }
                    }
                }

                return canWePlayBoxX;
            },

            canWePlayBoxY(boxId,idColorPlayer, boxItems){

                let canWePlayBoxY = {
                    weCanPlay: false,
                    boxSwitch: [],
                    boxToStop: 0
                };

                let wePossibilityCanPlay = false;

                for(let i=0; i < boxItems.lines.y[boxItems[boxId].lines.y].length; i++){
                    console.log(boxItems.lines.y[boxItems[boxId].lines.y][i])
                }

                // Check in X line is easy, we have just to add 1 and -1 to verify is box is active or not
                if(boxItems.activeBox.indexOf() !== -1){
                    // We can play only if is not the same color that we play 0 is player 1 computer
                    if(boxItems[boxId - 1].color != idColorPlayer){
                        wePossibilityCanPlay = true;
                    }
                } else if (boxItems.activeBox.indexOf(boxId + 1) !== -1) {
                    if(boxItems[boxId + 1].color != idColorPlayer){
                        wePossibilityCanPlay = true;
                    }
                }

                if(wePossibilityCanPlay){

                    boxItems.lines.x[boxItems[boxId].lines.x].forEach(function(boxInLineX){
                        // We need to find an item active AND that is the current color player
                        if(boxItems.activeBox.indexOf(boxInLineX) !== -1 && boxItems[boxInLineX].color === idColorPlayer){
                            // Only one is need for can play
                            canWePlayBoxX.weCanPlay = true;
                            canWePlayBoxX.boxToStop = boxInLineX;
                        }
                    });

                    // If we can play we going to add in array the box that will be switch
                    if(canWePlayBoxX.weCanPlay){
                        // We make a for to parcours the smaller to the biggest
                        let biggestItem = Math.max(canWePlayBoxX.boxToStop, boxId),
                                smallerItem = Math.min(canWePlayBoxX.boxToStop, boxId),
                                nextItem = smallerItem + 1;

                        for(nextItem; nextItem < biggestItem; nextItem++){
                            canWePlayBoxX.boxSwitch.push(nextItem);
                        }
                    }
                }

                return canWePlayBoxY;
            }
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
             opacity: 0.6;
        }
    }
</style>
