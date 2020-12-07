<template>
  <div id="app">
    <ul class="sectors">
      <li class="sector blue"
        :class="{'active': activeSector == 1} "
        ref="blue"
        id="1"
        v-on:click="addPlayerStep"
      > 
      </li>

      <li class="sector red"
        :class="{'active': activeSector == 2} "
        ref="red"
        id="2"
        v-on:click="addPlayerStep"
      ></li>

      <li class="sector green" 
        :class="{'active': activeSector == 3} "
        ref="green"
        id="3"
        v-on:click="addPlayerStep"
      ></li>

      <li class="sector yellow"
        :class="{'active': activeSector == 4} "
        ref="yellow"
        id="4"
        v-on:click="addPlayerStep"
      ></li>
    </ul>
    <div 
      class="hints"
      v-show="isPlaying && rendering"
    >Запоминай!</div>

    <div 
      class="hints"
      v-show="isPlaying && !rendering"
    >Попоробуй повторить</div>
    <Options 
      v-on:start-the-game="startTheGame"
      v-on:change-difficulty="setDifficulty"
      :gameLevel='gameLevel'
      :playerSuccessSteps="playerSuccessSteps"
      :defeatAnnouncement="defeatAnnouncement"
    /> 

  </div>
</template>

<script>
import Options from '@/components/Options.vue'


export default {
  name: 'App',
  components: {
    Options
  },
  data() {
    return {
      defeatAnnouncement: false,
      playerSequence: [],
      gameLevel: 0,
      dalayMS: 1000,
      playerSuccessSteps: 0,
      areYouWinningSon: true,
      activeSector: null,
      isPlaying: false,
      sequence: [],
      rendering: false,
    }
  },
  methods: {
    setDifficulty(dif) {
      console.log(dif)
      if (dif === "hard") this.delayMS = 400
      if (dif === "normal") this.delayMS = 1000
      if (dif === "easy") this.delayMS = 1500 
    },
    startTheGame() {
      this.setSequence()
      this.setDifficulty()
      this.renderSequence()
      this.defeatAnnouncement = false
      this.gameLevel = 0
      this.isPlaying = true
      this.areYouWinningSon = true
      this.playerSequence = []
      console.log("Начали игру")
    },
    setSequence() {
      this.sequence = []
      console.log(this.sequence)
      for(let i = 0; i < 10; i++) {
        this.addStep()
      }
    },
    addStep() {
      this.sequence.push( Math.floor(Math.random() * 4 + 1))
    },
    async renderSequence() {
      this.rendering = true
      await this.delay(1000) // before round start
      this.activeSector = null // remove highlight
      await this.delay(1000) // 
      for (let i = 0; i <= this.gameLevel; i++) {
        this.activeSector = +this.sequence[i]
        this.playMusic(this.activeSector)
        await this.delay(this.delayMS) // duration highlight each sector 
        this.activeSector = null // remove highlight
        await this.delay(this.delayMS) // duration highlight each sector 
      }
      await this.delay(500)
      this.rendering = false
    },
//-------------------------
    delay(ms) {
      return new Promise(r => setTimeout(() => r(), ms))
    },
    async addPlayerStep(event) {
      if (event) {
        this.sector = event.target
      }
      if (event && this.gameStatus && !this.rendering) {
        this.playerSequence.push(+event.target.getAttribute('id'))
        this.activeSector = event.target.getAttribute('id')
        this.playMusic(event.target.getAttribute('id'))
        this.compareSequence()
      } 
    },
    compareSequence() {
      let currentPlayerStep = this.playerSequence[this.playerSequence.length - 1]
      console.log(this.sequence[this.playerSequence.length - 1])

      if (currentPlayerStep === this.sequence[this.playerSequence.length - 1]) {
        this.playerSuccessSteps += 1
        if (this.playerSequence.length === this.gameLevel + 1) {
          this.gameLevel += 1
          this.renderSequence()
          this.playerSequence = [] // reset player sequence
        }
        
      } else if (this.isPlaying){ 
        this.areYouWinningSon = false
        this.endGame()
      }
    },
    endGame() {
      this.isPlaying = false
      this.defeatAnnouncement = true
    },
    playMusic(sector) {
      let audio = new Audio('./sounds/' + sector +'.mp3')
      audio.play()
    }
    
  },
  computed: {
    gameStatus() {
      return this.isPlaying && this.areYouWinningSon
    }
  }
}
</script>

<style lang="sass">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap')

*
  padding: 0
  margin: 0
  box-sizing: border-box

#app
  margin: 20px auto
  padding-top: 40px
  display: flex
  flex-wrap: wrap
  justify-content: center
  max-width: 1240px
  position: relative

ul.sectors
  margin: 0 auto
  position: relative
  width: 360px
  height: 360px
  list-style: none
  border-radius: 50%
  overflow: hidden
  box-shadow:  0 0 4px 4px #ccc

  li
    position: absolute
    width: 179px
    height: 179px

  li:nth-child(1):hover
    border-right: 4px solid rgba(255,255,255, .7)
    border-bottom: 4px solid rgba(255,255,255, .7)

  li:nth-child(2):hover
    border-left: 4px solid rgba(255,255,255, .7)
    border-bottom: 4px solid rgba(255,255,255, .7)

  li:nth-child(3):hover
    border-left: 4px solid rgba(255,255,255, .7)
    border-top: 4px solid rgba(255,255,255, .7)

  li:nth-child(4):hover
    border-right: 4px solid rgba(255,255,255, .7)
    border-top: 4px solid rgba(255,255,255, .7)



  .active
    transform: scale(0.95)
    filter: saturate(200%) sepia(60%) contrast(175%)

  .blue
    top: 0
    left: 0
    background-color: #0779e3

  .red
    top: 0
    right: 0
    background-color: #e30707

  .green
    bottom: 0
    right: 0
    background-color: #07e33a

  .yellow
    bottom: 0
    left: 0
    background-color: #fbff1c

.hints
  position: absolute
  top: 0px
  font-family: 'Roboto', sans-serif
  width: 200px
  background-color: #cce6ff
  padding: 10px
  border-radius: 5px

</style>
