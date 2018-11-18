<template>
    <div id="bordGame" :class="{'playerCantPlay' : !playerCanPlay}">
        <box v-for="n in 64" :boxId="n"></box>
    </div>
</template>

<script>
    import box from './box.vue'

    export default {
        name: 'BoardGame',
        props: ['playerCanPlay'],
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
                    },
                    29: {
                        color: isWhite,
                    },
                    36: {
                        color: isWhite,
                    },
                    37: {
                        color: isBlack,
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
        },
        methods: {

            activeBox(boxId, idColorPlayer, test) {

                if(typeof(test) == 'undefined'){
                    test = false;
                }

                // If it's the reel player we make sur he can play
                if(idColorPlayer == 0){
                    if(!this.$parent.game.playerCanPlay){
                        return false;
                    }
                }
                // We going to start if we can play in this box
                let canWePlayBox = this.canWePlayBox(boxId, idColorPlayer);

                if(canWePlayBox.weCanPlay){

                    // if it's not test, we going to switch box in boxSwitch
                    if(!test){

                        this.switchBoxColor(canWePlayBox.boxSwitch, idColorPlayer);

                        this.boxItems.activeBox.push(boxId);
                    }

                    canWePlayBox.boxPlayed = boxId;

                    // We going to call the IA for she play
                    if(idColorPlayer == 0) {
                        this.turnToIA();
                    } else if (idColorPlayer == 1){
                        this.$parent.game.playerCanPlay = true;
                    }
                }

                return canWePlayBox;
            },
            switchBoxColor(boxToSwitch, idColorPlayer){
                for (let boxSwitch of boxToSwitch){
                    this.boxItems[boxSwitch].color = idColorPlayer;
                }
            },
            canWePlayBox(boxId, idColorPlayer){

                let canWePlayBox = {
                    weCanPlay: false,
                    boxSwitch: []
                };

                if(typeof(boxId) != 'undefined'){

                    let boxItems = this.boxItems;

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

                // We need to find case that on the - 1 line X or + 1 line X and that in same Y linef

                if(boxItems[boxId].lines.x > 1){
                    boxWeHaveToCheck = boxWeHaveToCheck.concat(boxItems.lines.x[boxItems[boxId].lines.x - 1])
                }

                if(boxItems[boxId].lines.x < 8){
                    boxWeHaveToCheck = boxWeHaveToCheck.concat(boxItems.lines.x[boxItems[boxId].lines.x + 1])
                }

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
            },

            turnToIA: function(){
                this.$parent.game.playerCanPlay = false;

                let boxToPlay = {
                    id: 0,
                    numberSwitch: 0
                };

                // We going to search for all the line what is the line who will gain the most benefit
                for(let i=1; i <= 64; i++){
                    // Id color player 1 for computer
                    let responseSimulatePlay = this.activeBox(i, 1, true);

                    if(responseSimulatePlay.weCanPlay == true){
                        if(responseSimulatePlay.boxSwitch.length > boxToPlay.numberSwitch){
                            // the new best choice

                            console.log(responseSimulatePlay);
                            boxToPlay.numberSwitch = responseSimulatePlay.boxSwitch.length;
                            boxToPlay.id = responseSimulatePlay.boxPlayed
                        }
                    }
                }

                if(boxToPlay.numberSwitch > 0){
                    // Simulation finish we can realy play
                    this.activeBox(boxToPlay.id, 1);
                } else {
                    // You Win !
                    alert('victoire !')
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
        max-width: 600px;
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

<style lang="less">
    #bordGame {
        &.playerCantPlay{
             cursor: default;

            .box {
                cursor: default !important;

                &:hover .pawn:not(.active){
                     opacity: 0 !important;
                 }
            }
        }
    }
</style>
