<template>
  <v-app id="app">
    <section class="rounds-section">
      <h2>Rondas: {{ countRound }}</h2>
    </section>
    <section class="game-section">
      <h1 class="title-game">{{ title }}</h1>
      <div class="box-container">
        <div class="box-selected" v-for="item in board" :key="item.value" @click="paintCellByPlayer(item.value)">
          <svg v-if="item.circle" height="100" width="100" class="red-circle">
            <circle cx="50" cy="50" r="40" stroke="#ff6d6d" stroke-width="15px" fill="transparent"/>
          </svg>
          <svg v-if="item.cross" fill="#6f6fff" height="90" width="90" stroke-width="10px" class="blue-cross" viewBox="0 0 460.775 460.775">
            <g>
              <path d="M285.08,230.397L456.218,59.27c6.076-6.077,6.076-15.911,0-21.986L423.511,4.565c-2.913-2.911-6.866-4.55-10.992-4.55 c-4.127,0-8.08,1.639-10.993,4.55l-171.138,171.14L59.25,4.565c-2.913-2.911-6.866-4.55-10.993-4.55 c-4.126,0-8.08,1.639-10.992,4.55L4.558,37.284c-6.077,6.075-6.077,15.909,0,21.986l171.138,171.128L4.575,401.505 c-6.074,6.077-6.074,15.911,0,21.986l32.709,32.719c2.911,2.911,6.865,4.55,10.992,4.55c4.127,0,8.08-1.639,10.994-4.55 l171.117-171.12l171.118,171.12c2.913,2.911,6.866,4.55,10.993,4.55c4.128,0,8.081-1.639,10.992-4.55l32.709-32.719 c6.074-6.075,6.074-15.909,0-21.986L285.08,230.397z"/> 
            </g>
          </svg>
        </div>
      </div>
    </section>
    <section class="players-section">
      <v-icon :class="turnPlayer(this.players[0])" large color="#ff0000">mdi-account</v-icon>
      <v-icon :class="turnPlayer(this.players[1])" large color="#0000ff">mdi-account</v-icon>
    </section>
  </v-app>
</template>

<script>
  export default {
    name: 'App',
    computed: {
      countRound () {
        if (!this.victory && this.rounds > 4) {
          this.lostGame()
        }
        return this.rounds
      }
    },
    data: () => ({
      title: 'Tres en raya',
      board: [
        { value: 1, circle: false, cross: false },
        { value: 2, circle: false, cross: false },
        { value: 3, circle: false, cross: false },
        { value: 4, circle: false, cross: false },
        { value: 5, circle: false, cross: false },
        { value: 6, circle: false, cross: false },
        { value: 7, circle: false, cross: false },
        { value: 8, circle: false, cross: false },
        { value: 9, circle: false, cross: false }
      ],
      rounds: 1,
      players: [
        { value: 1, paint: 'circle', round: 1, yourRound: true },
        { value: 2, paint: 'cross', round: 1, yourRound: false}
      ],
      victory: false
    }),
    mounted() {
      this.$vuetify.theme.dark = true
    },
    methods: {
      paintCellByPlayer (value) {
        const cell = this.board.find(cell => cell.value === value)
        const player1 = this.players.find(player => player.yourRound) // value 1 value 2
        const player2 = this.players.find(player => !player.yourRound) // value 2 value 1
        if (!cell.circle && !cell.cross) {
          cell[player1.paint] = true
          player1.round++
          player1.yourRound = !player1.yourRound
          player2.yourRound = !player2.yourRound
          if (player1.round === player2.round) {
            this.rounds = player2.round
          }
        }
        this.calculateVictory()
      },
      calculateVictory () {
        const winRules = {
          row1: 0,
          row2: 0,
          row3: 0,
          col1: 0,
          col2: 0,
          col3: 0,
          dig1: 0,
          dig2: 0
        }
        this.board.forEach(cell => {
          if (cell.value === 1 || cell.value === 2 || cell.value === 3) {
            if (cell.circle) {winRules.row1++}
            if (cell.cross) {winRules.row1--}
          }
          if (cell.value === 4 || cell.value === 5 || cell.value === 6) {
            if (cell.circle) {winRules.row2++}
            if (cell.cross) {winRules.row2--}
          }
          if (cell.value === 7 || cell.value === 8 || cell.value === 9) {
            if (cell.circle) {winRules.row3++}
            if (cell.cross) {winRules.row3--}
          }
          if (cell.value === 1 || cell.value === 4 || cell.value === 7) {
            if (cell.circle) {winRules.col1++}
            if (cell.cross) {winRules.col1--}
          }
          if (cell.value === 2 || cell.value === 5 || cell.value === 8) {
            if (cell.circle) {winRules.col2++}
            if (cell.cross) {winRules.col2--}
          }
          if (cell.value === 3 || cell.value === 6 || cell.value === 9) {
            if (cell.circle) {winRules.col3++}
            if (cell.cross) {winRules.col3--}
          }
          if (cell.value === 1 || cell.value === 5 || cell.value === 9) {
            if (cell.circle) {winRules.dig1++}
            if (cell.cross) {winRules.dig1--}
          }
          if (cell.value === 3 || cell.value === 5 || cell.value === 7) {
            if (cell.circle) {winRules.dig2++}
            if (cell.cross) {winRules.dig2--}
          }
        })
        for(const i in winRules) {
          if (winRules[i] === 3) { this.winGame(1) }
          if (winRules[i] === -3) { this.winGame(2) }
        }
      },
      winGame (value) {
        this.victory = true
        this.$swal.fire({
          icon: "success",
          title: "Felicidades",
          text: `Has ganado jugador ${value}`,
          allowOutsideClick: false,
          confirmButtonText: 'Reiniciar juego',
          background: "#0f091e",
          color: "white",
          confirmButtonColor: "#bd63b6"
        }).then((result) => {
          if (result.isConfirmed) {
            this.victory = false
            this.board.forEach(item => {
              item.circle = false
              item.cross = false
            })
            this.players.forEach(player => {
              player.round = 1
            })
            this.rounds = 1
          }
        })
      },
      lostGame() {
        this.$swal.fire({
          icon: "error",
          title: "Uups...",
          text: "Ambos perdeis",
          allowOutsideClick: false,
          confirmButtonText: 'Reiniciar juego',
          background: "#0f091e",
          color: "white",
          confirmButtonColor: "#bd63b6"
        }).then((result) => {
          if (result.isConfirmed) {
            this.victory = false
            this.board.forEach(item => {
              item.circle = false
              item.cross = false
            })
            this.players.forEach(player => {
              player.round = 1
            })
            this.rounds = 1
          }
        })
      },
      turnPlayer (player) {
        let nameClass = 'player-turn'
        if (player.value === 1 && player.yourRound) {
          nameClass = nameClass + '-red'
        }
        if (player.value === 2 && player.yourRound) {
          nameClass = nameClass + '-blue'
        }
        return nameClass
      }
    }
  }
</script>

<style>
#app {
  display: flex;
  width: 100%;
  padding: 2% 10%;
}
.v-application--wrap {
  align-content: center;
}
.game-section {
  width: 100%;
  max-height: 600px;
  display: flex;
  align-items: center;
  flex-direction: column;
}
.rounds-section, .players-section {
  display: flex;
  justify-content: center;
}
.title-game {
  font-size: 48px;
}
.box-container {
  width: 320px;
  height: 320px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  background-color: white;
}
.box-selected {
  background-color: #121212;
  width: 100px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.player-turn-red, .player-turn-blue {
  border-radius: 30px;
  margin: 30px 10px;
  padding: 5px;
}
.player-turn-red {
  /* background-color: #ff000021; */
  filter: drop-shadow( 0px 0px 15px #ff0000 );
  color: #ff6d6d !important;
}
.player-turn-blue {
  /* background-color: #0000ff21; */
  filter: drop-shadow( 0px 0px 15px #0000ff );
  color: #6f6fff !important;
}
.red-circle {
  filter: drop-shadow( 0px 0px 15px #ff0000 );
}
.blue-cross {
  filter: drop-shadow( 0px 0px 15px #0000ff );
}
</style>
