<template>
  <q-page >
    <div class="flex justify-center q-pa-sm ">
        <PlayerBar class="player-bar" :player="player" :isAi="player === players[1] && isAiActivated" :class="[player === activePlayer ? 'bg-positive' : '']" v-for="player in players"  :key="player.token" />
     </div>
    <div class="flex grid-container-custom column flex-center items-start" v-if="!gameOver" @aiHasBennToggled>
      <div class="row no-wrap justify-center flex shadow-10">
        <div class="column reverse column-custom" v-for="(column, colIndex) in grid" :key="colIndex">
          <div class="cell flex justify-center items-center grow-1" v-for="cell in column">
            <h4 class="q-ma-none" >{{ cell.cellValue }}</h4>
          </div>
          <div class="play-button-wrap flex grow-1">
            <q-btn
              :disabled="counts[colIndex] >= 4 || (isAiActivated && activePlayer === players[1])"
              :ripple="{ color: 'black' }"
              @click="playToken(colIndex)"
              class="fit q-pa-none"
              color="primary"
              glossy
              icon="fas fa-donate"
              label="play"
              push
              stack
              wait-for-ripple
            />
          </div>
          
        </div>
       
      </div>
      
    </div>
    <div class="flex flex-center column" v-if="gameOver">
     <h3 class='text-center'> {{ gameOver }} </h3> 
     <q-separator color="secondary" inset />
     <q-btn @click="nextRound()" push size="xl" color="positive" label="Play again" class="shadow-2" />
    </div>
  </q-page>
</template>

<script>
import Counter from 'components/Counter.vue'
import PlayerBar from 'components/PlayerBar.vue'

export default {
  name: 'PageIndex',
  components: {
    Counter,
    PlayerBar,
  },
  data() {
    return {
      grid: [
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
      ],
      victoryCases: [
        [{ col: 0, row: 0}, { col: 0, row: 1}, { col: 0, row: 2}, { col: 0, row: 3}],
        [{ col: 1, row: 0}, { col: 1, row: 1}, { col: 1, row: 2}, { col: 1, row: 3}],
        [{ col: 2, row: 0}, { col: 2, row: 1}, { col: 2, row: 2}, { col: 2, row: 3}],
        [{ col: 3, row: 0}, { col: 3, row: 1}, { col: 3, row: 2}, { col: 3, row: 3}],

        [{ col: 0, row: 0}, { col: 1, row: 0}, { col: 2, row: 0}, { col: 3, row: 0}],
        [{ col: 0, row: 1}, { col: 1, row: 1}, { col: 2, row: 1}, { col: 3, row: 1}],
        [{ col: 0, row: 2}, { col: 1, row: 2}, { col: 2, row: 2}, { col: 3, row: 2}],
        [{ col: 0, row: 3}, { col: 1, row: 3}, { col: 2, row: 3}, { col: 3, row: 3}],

        [{ col: 0, row: 0}, { col: 1, row: 1}, { col: 2, row: 2}, { col: 3, row: 3}],
        [{ col: 0, row: 3}, { col: 1, row: 2}, { col: 2, row: 1}, { col: 3, row: 0}],
      ],
      players: [
        {name: "John", score: 0, token: '❌', avatarUrl: "https://cdn.pixabay.com/photo/2016/12/07/21/01/cartoon-1890438_960_720.jpg"},
        {name: "Sarah", score: 0, token: '⬤', avatarUrl: "https://cdn.pixabay.com/photo/2017/01/31/19/07/avatar-2026510_960_720.png"},

      ],
      activePlayer: null,
      firstPlayer: null,
      
    }
  },
  watch: {
    isAiActivated(newValue, oldValue) {
      console.log(`watched from game component ${newValue}`)
       if(newValue === true && this.activePlayer.token === '⬤') {
        setTimeout(this.aiPlayToken, 500) 
      }
    },
  },
  computed: {
    isAiActivated: {
      get () {
        return this.$store.state.isAiToggled
      },
    },
    gameOver() {
      if(this.isXVictory) {
        this.players[0].score ++
        return `${this.players[0].name} a gagné la partie`
      }
      else if(this.isOVictory) {
        this.players[1].score ++
        return `${this.players[1].name} a gagné la partie`
      }
      else if(this.isDraw) {
        return `Match nul`
      }
      else {
        return false
      }
    },
    isXVictory() {
      return this.victoryStatus.some( vCase => vCase.countX === 4 )
    },
    isOVictory() {
      return this.victoryStatus.some( vCase => vCase.countO === 4 )
    },
    isDraw() {
      return this.turnsCount >= 16
    },
    counts() {
      return [this.returnCount(0),this.returnCount(1),this.returnCount(2),this.returnCount(3)]
    },
    turnsCount() {
      return this.counts.reduce((a,b) => a + b, 0)
    },
    victoryStatus() {
     return this.victoryCases.map( (vCase, index) => this.computeVictoryCaseStatus(vCase, index) )
    },
    victoryStatusSortedByX() {
      return this.victoryStatus.filter( vCase => vCase.isPossibleX).sort(this.sortByCountX)
    },
    victoryStatusSortedByO() {
      return this.victoryStatus.filter( vCase => vCase.isPossibleO).sort(this.sortByCountO)
    },
    nextIndexesToBePlayed() {
      return this.grid.map( (column, index) =>  { return { col: index, row: column.findIndex( cell => cell.cellValue === "")}} )
    },
    nextAIVictoryCaseToFillX() {
      return this.victoryStatusSortedByX.find( vCase => this.victoryCases[vCase.vCaseIndex].some( cellCase => cellCase.row === this.nextIndexesToBePlayed[cellCase.col].row && cellCase.col === this.nextIndexesToBePlayed[cellCase.col].col ))
    },
    nextAIVictoryCaseToFillO() {
      return this.victoryStatusSortedByO.find( vCase => this.victoryCases[vCase.vCaseIndex].some( cellCase => cellCase.row === this.nextIndexesToBePlayed[cellCase.col].row && cellCase.col === this.nextIndexesToBePlayed[cellCase.col].col ))
    },
   
    nextAIColToPlay() {
      if(this.nextAIVictoryCaseToFillO) {
        if (this.nextAIVictoryCaseToFillO.countO === 3 ) {
          const winningColToPLay = this.findPlayableColInVictoryCase(this.victoryCases[this.nextAIVictoryCaseToFillO.vCaseIndex])
          
          if (winningColToPLay) {
            console.log("WINNING COL SELECTED")
            return winningColToPLay.col
          }
        }
      }
      if(this.nextAIVictoryCaseToFillX) {
        const colToPlay = this.findPlayableColInVictoryCase(this.victoryCases[this.nextAIVictoryCaseToFillX.vCaseIndex]) 
        console.log(`primary calculation ---> ${colToPlay}`)
        if(colToPlay) {
        console.log("PRIMARY COL SELECTED")
        return colToPlay.col
        }
      }
      
      if(this.nextAIVictoryCaseToFillO) {
        console.log("sec init")
        const altColToPlay = this.findPlayableColInVictoryCase(this.victoryCases[this.nextAIVictoryCaseToFillO.vCaseIndex])
        console.log(`secondary calculation ---> ${altColToPlay}`)
        if (altColToPlay) {
          console.log("ALT COL SELECTED")
          return altColToPlay.col
        }
      }
        console.log("RANDOM COL SELECTED")
        const freeCol = this.grid.findIndex( column => column.some( (cell) => cell.cellValue === ""))
        console.log(`secondary calculation ---> ${freeCol}`)
        
        return freeCol
    }
  },
  methods: {
     findPlayableColInVictoryCase(victoryCase) {
      return victoryCase.find( cellCase => cellCase.row === this.nextIndexesToBePlayed[cellCase.col].row && cellCase.col === this.nextIndexesToBePlayed[cellCase.col].col)
    },
    sortByCountX (a, b) {
      if (a.countX < b.countX) {
        return 1
      }
      else if (a.countX > b.countX) {
        return -1
      }
      else {
        return 0
      }
    },
    sortByCountO (a, b) {
      if (a.countO < b.countO) {
        return 1
      }
      else if (a.countO > b.countO) {
        return -1
      }
      else {
        return 0
      }
    },
    aiPlayToken() {
      this.playToken(this.nextAIColToPlay)
    },
    computeVictoryCaseStatus(victoryCase, victoryCaseIndex) {
      const signs = []
      const result = { vCaseIndex: victoryCaseIndex, countNeutral: 0, countX: 0, countO: 0, isPossibleX: true, isPossibleO: true }
      victoryCase.forEach( vCase => {
          signs.push( this.grid[vCase.col][vCase.row].cellValue )
        })
      signs.forEach( sign =>  sign === '❌' ? result.countX ++ : sign === '⬤' ? result.countO ++ : result.countNeutral ++ )
      if (result.countX > 0 && result.countO > 0) {
        result.isPossibleX = false
        result.isPossibleO = false
      }
      else if (result.countX > 0) {
        result.isPossibleO = false
      }
      else if (result.countO > 0) {
        result.isPossibleX = false
      }
      return result
    },
    returnCount(colIndex) {
      return this.grid[colIndex].filter( cell => cell.cellValue !== "").length
    },
    returnIndexOfFreeCell(colIndex) {
      return this.grid[colIndex].findIndex( cell => cell.cellValue === "")
    },
    playToken(colIndex) {
      this.grid[colIndex][this.returnIndexOfFreeCell(colIndex)].cellValue = this.activePlayer.token
      if(this.turnsCount < 16) {
        this.switchActivePlayer()
      }
      
    },
    switchActivePlayer() {
      this.activePlayer = this.players.find(player => player !== this.activePlayer)
      // if(this.isIaActivated && this.activePlayer.token === '⬤') {
      //  setTimeout(this.aiPlayToken, 500) 
      // }
      if(this.isAiActivated && this.activePlayer.token === '⬤') {
        setTimeout(this.aiPlayToken, 500) 
      } 

    },
    switchFirstPlayer() {
      if (this.firstPlayer) {
        this.firstPlayer = this.players.find( player => player !== this.firstPlayer )
      }
      else {
        this.firstPlayer = this.returnRandomPlayer()
      }
    },
    returnRandomPlayer() {
      return this.players[Math.floor(Math.random() * Math.floor(2))]
    },
    nextRound() {
      this.grid = this.returnNewGrid()
      this.switchFirstPlayer()
      this.activePlayer = this.firstPlayer
      if(this.isAiActivated && this.activePlayer.token === '⬤') {
       setTimeout(this.aiPlayToken, 500) 
      }
    },
    returnNewGrid() {
      return [
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
        [{cellValue: ""}, {cellValue: ""}, {cellValue: ""}, {cellValue: ""}],
      ]
    },
    
  },
  beforeMount() {
      this.nextRound()
    },
}
</script>
<style lang="scss">
  .cell {
    min-height: 60px;
    min-width: 60px;
    width: 23vw;
    height: 23vw;
    border: 1px solid black;
    flex: 1 1 max-content;
    max-height: 120px;
    max-width: 120px;
    
  }
  .play-button-wrap {
   min-height: 60px;
    min-width: 60px;
    width: 23vw;
    height: 23vw;
    
    flex: 1 1 max-content;
    max-height: 120px;
    max-width: 120px;
  }
  .grid-container-custom {
     width: 100%;
    height: 100%;
    flex: 1 1 auto;
  }
  .column-custom {
    width: 100%;
    height: 100%;
    flex: 1 1 auto;
  }
  .play-button :hover {
    background-color: $accent ;
  }
  .play-button {
    background-color: $secondary ;
  }
  .player-bar {
    
  }
</style>