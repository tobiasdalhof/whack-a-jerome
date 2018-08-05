<template>
  <div id="app">
    <div>
      <div><b>Debug</b></div>
      <div>Level: {{ level }}</div>
      <div>Speed: {{ speed }}</div>
      <div>Round: {{ currentRound }} / {{ roundsPerLevel }}</div>
    </div>
    <div class="d-flex justify-content-center align-items-center" style="height: 100vh">
      <div class="table d-flex align-items-center position-relative">
        <div v-if="showStartScreen === true">
          <div @click="startGame">Start</div>
        </div>
        <div v-if="showEndScreen === true">
          <div>
            <h1>Verloren -.-</h1>
            <p>Rounds: {{ stats.clearedRounds }}</p>
            <p>Holes: {{ stats.clearedHoles }}</p>
            <p @click="startGame">Play again</p>
          </div>
        </div>
        <div v-if="showNextLevelScreen === true">
          <div>Level {{ level }} finished</div>
          <div @click="runGame(level+1)">Start level {{ level+1 }}</div>
        </div>
        <!-- <div> -->
        <div v-show="gameIsActive === true">
          <div class="w-100">
            <div class="d-inline-block w-100 mb-4">
              <div class="d-flex justify-content-center">
                <div class="hole"></div>
                <div class="hole"></div>
                <div class="hole"></div>
              </div>
            </div>
            <div class="d-inline-block w-100 mt-4">
                <div class="d-flex justify-content-center">
                  <div class="hole"></div>
                  <div class="hole"></div>
              </div>
            </div>
            <!-- <div class="rounds">
              <div :class="'round' + currentRoundClass(index)" v-for="index in roundsPerLevel" :key="index"></div>
            </div> -->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      showStartScreen: true,
      showEndScreen: false,
      showNextLevelScreen: false,
      gameIsActive: false,
      level: 1,
      roundsPerLevel: 1,
      startSpeed: 1400,
      currentRound: 0,
      stats: {
        clearedHoles: 0,
        clearedRounds: 0,
      },
    }
  },
  computed: {
    speed () {
      if (this.level === 1) {
        return this.startSpeed
      }
      return this.startSpeed - (this.startSpeed * (0.035 * this.level))
    }
  },
  methods: {
    getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min
    },
    startGame () {
      this.showStartScreen = false
      this.showEndScreen = false
      this.gameIsActive = true
      this.stats.clearedHoles = 0
      this.stats.clearedRounds = 0
      this.runGame(1)
    },
    endGame () {
      this.gameIsActive = false
      this.showEndScreen = true
      this.showNextLevelScreen = false
      this.currentRound = 0
      this.level = 1
    },
    runGame (level) {
      this.gameIsActive = true
      this.showNextLevelScreen = false
      this.level = level
      const holes = this.$el.querySelectorAll('.hole')

      const round = setInterval(() => {
        let clearedHoles = 0
        const holesCount = this.getRandomInt(1, 3)
        const activeHoles = []
        const usedHoles = []

        this.currentRound++

        for (let i = 0; i < holesCount;) {
          const randomInt = this.getRandomInt(0, holes.length-1)

          if (usedHoles.includes(randomInt) === false) {
            activeHoles.push(holes[randomInt])
            usedHoles.push(randomInt)
            i++
          }
        }

        const clickFunction = (e) => {
          const hole = e.target
          hole.setAttribute('style', 'background-color:lime')
          clearedHoles++ 
          this.stats.clearedHoles++
        }

        for (let i = 0; i < activeHoles.length; i++) {
          const hole = activeHoles[i]
          hole.addEventListener('click', clickFunction)
          hole.setAttribute('style', 'background-color:red')
        }

       setTimeout(() => {
          for (let i = 0; i < activeHoles.length; i++) {
            const hole = activeHoles[i]
            hole.removeEventListener('click', clickFunction)
            hole.setAttribute('style', '')
          }

          this.stats.clearedRounds++
          if (this.stats.clearedRounds % this.roundsPerLevel === 0) {
            clearInterval(round)
            this.currentRound = 0
            this.showNextLevelScreen = true
            this.gameIsActive = false
            return
          }

          if (clearedHoles !== holesCount) {
            this.endGame()
            clearInterval(round)
            return
          }
        }, 1200)
      }, this.speed)
    },
  },
  mounted () {

  }
}
</script>

<style lang="scss">
@import '~bootstrap/scss/_functions.scss';
@import '~bootstrap/scss/_variables.scss';
@import '~bootstrap/scss/_mixins.scss';
@import '~bootstrap/scss/_utilities.scss';
@import '~bootstrap/scss/_grid.scss';
@import '~bootstrap/scss/_images.scss';

body {
  margin: 0;
  background-color: #222;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #f5f5f5;
}
.table {
  width: 800px;
  height: 600px;
  background-color: brown;
  border-radius: 4px;
  .hole {
    margin: 0 50px;
    width: 150px;
    height: 150px;
    border-radius: 50%;
    background-color: #000;
  }
}
.rounds {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  display: flex;
  height: 10px;
  .round {
    flex: 1;
    height: 10px;
    background-color: rgba(47,47,47,.5);
    &.finished {
      background-color: rgba(0,255,90,.5);
    }
  }
}
</style>
