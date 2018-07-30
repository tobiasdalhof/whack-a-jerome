<template>
  <div id="app">
    <div class="d-flex justify-content-center align-items-center" style="height: 100vh">
      <div class="table d-flex align-items-center">
        <div v-if="showStartScreen === true">
          <div @click="startGame">Start</div>
        </div>
        <div v-if="showEndScreen === true">
          <div>
            <h1>Verloren -.-</h1>
            <p>Rounds: {{ stats.clearedRounds }}</p>
            <p>Holes: {{ stats.clearedRounds }}</p>
            <p @click="startGame">Play again</p>
          </div>
        </div>
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
      gameIsActive: false,
      stats: {
        clearedHoles: 0,
        clearedRounds: 0,
      },
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
      this.runGame()
    },
    endGame () {
      this.gameIsActive = false
      this.showEndScreen = true
    },
    runGame () {
      const holes = this.$el.querySelectorAll('.hole')

      const round = setInterval(() => {
        let clearedHoles = 0
        const holesCount = this.getRandomInt(1, 3)
        const activeHoles = []
        const usedHoles = []

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
          if (clearedHoles !== holesCount) {
            this.endGame()
            clearInterval(round)
            return
          }
          this.stats.clearedRounds++
        }, 1200)
      }, 1400)
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
</style>
