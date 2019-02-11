<template>
    <div>
        <p>generation {{generation}}</p>
        <input type="number" v-model.number="gameSpeed">
        <div v-for="(value, key) in table" :key="'row' + key" class="row">
            <div class="square is-editable" v-for="(cellValue, k) in value" :key="key + '-' + k"
                    v-bind:class="cellValue == 0 ? 'empty' : 'fill'"
                    @click="changeValue(key, k)"
                    >
            </div>
        </div>
        <button v-on:click="randomize">randomize</button>
        <button v-on:click="nextStep">next</button>
        <button v-if="!progress" v-on:click="startGame">start</button>
        <button v-if="progress" v-on:click="stopGame">stop</button>
    </div>
</template>
<script>
const size = 50;

export default {
    data: function() {
        return {
            size: size,
            table: this.makeInitialTable(),
            generation: 0,
            progress: false,
            timer: undefined,
            gameSpeed: 1000
        }
    },
    methods: {
        /**
         * returns 0 or 1
         */
        getRandomValue() { 
            return Math.floor(Math.random() * Math.floor(2));
        },
        /**
         * make initial tables for data property
         */
        makeInitialTable() {
            var item = {}
            for (var i = 0; i < size; i++) {
                item[i] =  {};
                for (var j = 0; j < size; j++) {
                    item[i][j] = 0;
                }
            }
            return item;
        },
        /**
         * progress game by 1 step
         */
        nextStep () {
            let nextStepCell = this.makeInitialTable();
            // copy value from original table
             for(var i = 0; i < size; i++) {
                for (var j = 0; j < size; j++) {
                    nextStepCell[i][j] = this.table[i][j];
                }
            }
            
            // check current status and defines the value of next table
            for (var i = 0; i < size; i++) {
                for (var j = 0; j < size; j++) {
                    let live = this.table[i][j] != 0;
                    let liveCount = this.getStatus(i, j).live;
                    if (live) {
                        if (liveCount <= 1 || liveCount >= 4)  {
                            // dead
                            nextStepCell[i][j] = 0;
                        }
                    }
                    else if (liveCount == 3) {
                        // birth
                        nextStepCell[i][j] = 1;
                    }
                    
                }
            }

            // write back value to original table
            for(var i = 0; i < size; i++) {
                for (var j = 0; j < size; j++) {
                    this.table[i][j] = nextStepCell[i][j];
                }
            }

            this.generation += 1;
        },
        getStatus (targetRow, targetColumn) {
            function valid (row, column) {
                return row >= 0 && column >= 0 && row < size && column < size;
            }

            let leftUpper = [targetRow - 1, targetColumn - 1];
            let upper = [targetRow - 1, targetColumn];
            let rightUpper = [targetRow - 1, targetColumn + 1];

            let left = [targetRow, targetColumn - 1];
            let right = [targetRow, targetColumn + 1];

            let leftLower = [targetRow + 1, targetColumn - 1];
            let lower = [targetRow + 1, targetColumn];
            let rightLower = [targetRow + 1, targetColumn + 1];

            let liveCount = 0;
            let deadCount = 0;

            let checkCells = [leftUpper, upper, rightUpper, left, right, leftLower, lower, rightLower];
            for (var cell of checkCells) {
                if (valid(cell[0], cell[1])) {
                    let val = this.table[cell[0]][cell[1]];
                    if (val == 0) {
                        deadCount++;
                    } else {
                        liveCount++;
                    }
                }
            }

            return {
                live: liveCount,
                dead: deadCount
            }
        },
        startGame: function() {
            this.progress = true;
            this.timer = setInterval(() => {
                this.nextStep();

            }, this.gameSpeed);
        },
        stopGame: function() {
            if (this.timer) {
                clearInterval(this.timer);
                this.progress = false;
            }

        },
        changeValue(row, column) {
            let current = this.table[row][column];
            if (current == 0) {
                this.table[row][column] = 1;
            } else {
                this.table[row][column] = 0;
            }

        },
        randomize() {
            for(var i = 0; i < size; i++) {
                for (var j = 0; j < size; j++) {
                    this.table[i][j] = this.getRandomValue()
                }
            }
        }
    }
}
</script>

<style scoped>
.square {
    width: 20px;
    height: 20px;
    text-align: center;
    display: inline-block;
    border: 1px solid lightgray;
    box-sizing: border-box;
}

.is-editable {
    cursor: pointer;
}

.fill {
    background-color: blue;
}

.row {
    display: block;
    height: 20px;
}

</style>
