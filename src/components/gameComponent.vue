<template>
    <div id="gameComponent">
        <div class="main-wrapper">
            <div class="content-container">
                <div class="hiddenWrapper">
                </div>
                <div class="game-field">
                    <ul>
                        <li class="red hoverable" @mousedown="setActive" @mouseup="setInactive" v-on:click="Clicked" data-tile="1"></li>
                        <li class="blue hoverable" @mousedown="setActive" @mouseup="setInactive" v-on:click="Clicked" data-tile="2"></li>
                        <li class="yellow hoverable" @mousedown="setActive" @mouseup="setInactive" v-on:click="Clicked" data-tile="3"></li>
                        <li class="green hoverable" @mousedown="setActive" @mouseup="setInactive" v-on:click="Clicked" data-tile="4"></li>
                    </ul>
                </div>
                <div class="game-info-container">
                    <div class="game-info-container">
                        <h2>Round: {{curRound}}</h2>
                        <div id="lose-message">Вы проиграли после {{curRound}} раунда.</div>
                        <button class="start-button" v-on:click="startGame">Start</button>
                    </div>
                    
                    <div class="game-settings-container">
                        <h2>Difficulty level:</h2>
                        <input id="easy" type="radio" name="mode" v-model="curDifficulty" value="1500" >
                        <label for="easy">Easy</label><br>
                        <input id="medium" type="radio" name="mode" v-model="curDifficulty" value="1000">
                        <label for="medium">Medium</label><br>
                        <input id="hard" type="radio" name="mode" v-model="curDifficulty" value="400">
                        <label for="hard">Hard</label>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'gameComponent',
    data() {
        return {
            curRound: 0,
            tilesArray: [],
            tile: '',
            currentClickNumber: 0,
            amountOfColors: 4,
            isGameStarted: false,
            curDifficulty: 1500
        }
    },
        methods: {
            getRandomTile: function() {
                let curTileNumber = Math.floor(Math.random() * (this.amountOfColors))+1;
                console.log(curTileNumber)
                return curTileNumber;
            },  
            ClearVars: function() {
                this.curRound = 0;
                this.tilesArray = [];
                this.currentClickNumber = 0;
            },
            startGame: function() {
                document.querySelector('.hiddenWrapper').style.zIndex = -1;
                this.isGameStarted = true;
                document.querySelector('#lose-message').style.visibility = "hidden";
                this.ClearVars();
                this.newRound();
            },

            newRound: function() {
                this.curRound += 1;
                this.currentClickNumber = 0;
                this.tilesArray.push(this.getRandomTile());
                this.animate(this.tilesArray);
            },

            animate: function(tilesArray) {
                var i = 0;
                var that = this;
                var interval = setInterval(function() {
                    that.playSound(tilesArray[i]);
                    that.lightUpTile(tilesArray[i]);

                    i++;
                    if (i >= tilesArray.length) {
                        clearInterval(interval);
                    }
                }, this.curDifficulty);
            },
            lightUpTile: function(tileNumber) {
                    var tile = document.querySelector(`[data-tile='${tileNumber}']`);
                    tile.classList.add('lit');
                    window.setTimeout(function() {
                        tile.classList.remove('lit');
                    }, 300);
            },
            playSound: function(tile) {
                var sound = new Audio(require(`@/assets/sounds/${tile}.ogg`));
                sound.play();
            },
            Clicked: function(e) { 
                if (this.isGameStarted) {
                    var clickedTileNubmer = e.target.dataset.tile;
                    this.playSound(clickedTileNubmer);
                    if (this.tilesArray[this.currentClickNumber] == clickedTileNubmer) {
                        this.currentClickNumber += 1;
                        if (this.currentClickNumber == this.tilesArray.length)
                        this.newRound();
                    } else {
                        document.querySelector('#lose-message').style.visibility = "visible";
                        document.querySelector('.hiddenWrapper').style.zIndex = 1000;
                        this.isGameStarted = false;
                    }
                }
            },
            setActive: function(e) {
                e.target.classList.add('active');
            },
            setInactive: function(e) {
                e.target.classList.remove('active');
            }
        }
}
</script>

<style>
.content-container {
    width: 50%;
    display: flex;
    justify-content: space-between;
}

.hiddenWrapper {
    position: absolute;
    width: 400px;
    height: 400px;
    z-index: 1000;
}

.start-button {
    cursor: pointer;
    width: 5em;
    box-sizing: border-box;
    font-size: 1.4em;
    -webkit-border-radius: 10px 10px 10px 10px;
    border-radius: 10px 10px 10px 10px;
    background: #6DABE8;
    border: none;
    padding: 0.3em 0.6em;
}

.red, .yellow, .blue, .green {
    opacity: 0.6;
    height: 290px;
    -webkit-border-radius: 150px 150px 150px 150px;
    border-radius: 150px 150px 150px 150px;
    position: absolute;
    text-indent: 10000px;
}

.red:hover, .yellow:hover, .blue:hover, .green:hover {
    border: 2px solid black;
}

.active {
    opacity: 1;
}

.lit {
    opacity: 1;
}

ul {
    list-style: none;
}

li {
    display: list-item;
    text-align: -webkit-match-parent;
    cursor: pointer;
}

.red {
    background: #FF5643;
    clip: rect(0px, 300px, 150px, 150px);
    width: 296px;
}

.blue {
    background: dodgerblue;
    clip: rect(0px, 150px, 150px, 0px);
    width: 300px;
}

.yellow {
    background: #FEEF33;
    clip: rect(150px, 150px, 300px, 0px);
    width: 300px;
}

.green {
    background: #BEDE15;
    clip: rect(150px,300px, 300px, 150px);
    width: 296px;
}

#lose-message {
    visibility: hidden;
}
</style>