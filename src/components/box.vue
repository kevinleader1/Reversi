<template>
    <div class="box" @click="activeBox(boxId, 0)">
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
                // Si il s'agit du joeur
                if(idColorPlayer == 0){
                    // Si il ne peux pas jouer on ne fait rien
                    if(!this.$parent.$parent.game.playerCanPlay){
                        return false;
                    }
                }
                // We going to start if we can play in this box
                let canWePlayBox = this.canWePlayBox(boxId, idColorPlayer);

                if(canWePlayBox.weCanPlay){
                    // We going to switch box in boxSwitch if it's the player
                    if(idColorPlayer == 0){

                        this.switchBoxColor(canWePlayBox.boxSwitch, idColorPlayer);

                        this.$parent.boxItems.activeBox.push(boxId);

                        // We going to call the IA for she play
                        this.$parent.turnToIA();
                    }
                }
            },
            switchBoxColor(boxToSwitch, idColorPlayer){
                for (let boxSwitch of boxToSwitch){
                    this.$parent.boxItems[boxSwitch].color = idColorPlayer;
                }
            },
            canWePlayBox(boxId, idColorPlayer){

                let canWePlayBox = {
                    weCanPlay: false,
                    boxSwitch: []
                };

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
                            canWePlayBox.boxSwitch = canWePlayBox.boxSwitch.concat(canWePLayX.boxSwitch);
                        }

                        /*
                         ---- Start with Y verification ---
                         ----                           ---
                         */

                        // Only if we don't have near same color on X
                        if(canWePLayX.blockNearNotSameColor != null && canWePLayX.blockNearNotSameColor) {
                            let canWePLayY = this.canWePlayBoxY(boxId, idColorPlayer, boxItems);

                            if (canWePLayY.weCanPlay) {
                                canWePlayBox.weCanPlay = true;
                                canWePlayBox.boxSwitch = canWePlayBox.boxSwitch.concat(canWePLayY.boxSwitch);
                            }
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
                    blockNearNotSameColor: null,
                    boxToStop: 0
                };

                let wePossibilityCanPlay = false;

                // Check in X line is easy, we have just to add 1 and -1 to verify is box is active or not
                if(boxItems.activeBox.indexOf(boxId - 1) !== -1){
                    // We can play only if is not the same color that we play 0 is player 1 computer
                    if(boxItems[boxId - 1].color != idColorPlayer){
                        canWePlayBoxX.blockNearNotSameColor = true;
                        wePossibilityCanPlay = true;
                    } else {
                        canWePlayBoxX.blockNearNotSameColor = false;
                    }
                } else if (boxItems.activeBox.indexOf(boxId + 1) !== -1) {
                    if(boxItems[boxId + 1].color != idColorPlayer){
                        canWePlayBoxX.blockNearNotSameColor = true;
                        wePossibilityCanPlay = true;
                    } else {
                        canWePlayBoxX.blockNearNotSameColor = false;
                    }
                } else {
                    // If none is active, we only put blockNearNotSameColor to true for Y logique
                    canWePlayBoxX.blockNearNotSameColor = true;
                }

                if(wePossibilityCanPlay){

                    for(let boxInLineX of boxItems.lines.x[boxItems[boxId].lines.x]){
                        if(boxItems.activeBox.indexOf(boxInLineX) !== -1 && boxItems[boxInLineX].color === idColorPlayer){
                            // Only one is need for can play
                            canWePlayBoxX.weCanPlay = true;
                            canWePlayBoxX.boxToStop = boxInLineX;

                            break;
                        }
                    }

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

                let boxWeHaveToCheck = [];

                // We need to find case that on the - 1 line X or + 1 line X and that in same Y line
                boxWeHaveToCheck = boxWeHaveToCheck.concat(boxItems.lines.x[boxItems[boxId].lines.x - 1], boxItems.lines.x[boxItems[boxId].lines.x + 1]);

                let boxItemY = [];

                for(let i=0; i < boxItems.lines.y[boxItems[boxId].lines.y].length; i++){
                    if(boxWeHaveToCheck.indexOf(boxItems.lines.y[boxItems[boxId].lines.y][i]) !== -1){
                        boxItemY.push(boxItems.lines.y[boxItems[boxId].lines.y][i]);
                    }
                }

                for(let j=0; j < boxItemY.length; j++){
                    if(boxItems[boxItemY[j]].color != idColorPlayer){
                        wePossibilityCanPlay = true;
                    }
                }

                if(wePossibilityCanPlay){

                    for(let boxInLineY of boxItems.lines.y[boxItems[boxId].lines.y]){
                        if(boxItems.activeBox.indexOf(boxInLineY) !== -1 && boxItems[boxInLineY].color === idColorPlayer){
                            // Only one is need for can play
                            canWePlayBoxY.weCanPlay = true;
                            canWePlayBoxY.boxToStop = boxInLineY;

                            break;
                        }
                    }

                    // If we can play we going to add in array the box that will be switch
                    if(canWePlayBoxY.weCanPlay){
                        // We make a for to parcours the smaller to the biggest
                        let biggestItem = Math.max(canWePlayBoxY.boxToStop, boxId),
                                smallerItem = Math.min(canWePlayBoxY.boxToStop, boxId),
                                nextItem = smallerItem + 1;

                        for(nextItem; nextItem < biggestItem; nextItem++){
                            // Only if there are on the same line !
                            if(boxItems[nextItem].lines.y === boxItems[boxId].lines.y){
                                canWePlayBoxY.boxSwitch.push(nextItem);
                            }
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
