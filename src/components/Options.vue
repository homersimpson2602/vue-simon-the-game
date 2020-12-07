<template>
  <div class="options">
    <span class="game-status defeat-level" v-show="defeatAnnouncement">Неверно! Игра окончена. </span>
    <span class="game-status success-level" 
      v-show="gameLevel > 1 && !defeatAnnouncement"
    >Верно! Следующий раунд. </span>
    
    <button id="start"
      v-on:click="$emit('start-the-game')"
      @click="$emit('change-difficulty', difficulty)"
    >Играть</button>
    <div class="stats">
      <span> Уровень: <strong>{{gameLevel}}</strong></span>
      <span>Очки: <strong>{{currentPTS}}</strong></span>
    </div>
    <h3>Уровень сложности:</h3>
    <ul>
      <li>
        <label for="easy">Лёгкий</label>
        <input type="radio" name="difficulty" id="easy" value="easy"
          v-model="difficulty"
          @change="$emit('change-difficulty', difficulty)"
        >
      </li>
      <li>
        <label for="normal">Нормальный</label>
        <input type="radio" name="difficulty" id="normal" value="normal" checked
          v-model="difficulty"
          @change="$emit('change-difficulty', difficulty)" 
        >
      </li>
      <li>
        <label for="hard">Сложный</label>
        <input type="radio" name="difficulty" id="hard" value="hard"
          v-model="difficulty"
          @change="$emit('change-difficulty', difficulty)"
        >
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "Options",
  props: {
    gameLevel: Number,
    playerSuccessSteps: Number,
    defeatAnnouncement: Boolean
  },
  data() {
    return {
      difficulty: "normal"
    }
  },
  methods: {

  },
  computed: {
    currentPTS() {
      if (this.gameLevel != 0) {
        if (this.difficulty === "hard") {
          return  Math.floor(this.playerSuccessSteps * this.gameLevel * 1.5)
        }
        if (this.difficulty === "normal") {
          return Math.floor(this.playerSuccessSteps * this.gameLevel * 1.25)
        }
        if (this.difficulty === "easy") {
          return  Math.floor(this.playerSuccessSteps * this.gameLevel * 1)
        }
      }
      return 0
    }
  }
}
</script>

<style scoped lang="sass">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap')

.options
  font-family: 'Roboto', sans-serif
  width: 360px
  box-shadow: 0 0 3px 3px #ccc
  border-radius: 5px
  padding: 10px
  display: flex
  flex-direction: column
  justify-content: space-between

  .stats span
    display: block
    margin-bottom: 10px
    border-bottom: 1px solid #ccc
  
  .defeat-level
    color: #ff0000
    font-weight: 700
  
  .success-level
    color: rgb(0, 107, 2)
    font-weight: 700

ul
  display: flex
  flex-direction: column
  width: 120px
  position: relative
  list-style: none

  li
    position: static
    margin-bottom: 10px
    display: flex
    justify-content: space-between
    border-bottom: 1px solid #ccc

button#start
  background-color: rgb(0, 107, 2)
  color: #fff
  width: 100px
  padding: 10px
  font-family: 'Roboto', sans-serif
  font-size: 1.2rem
  font-weight: 700
  outline: none
  border: none
  border-radius: 3px
  box-shadow: 1px 1px  1px #ccc

  &:focus,&:active
    filter: saturate(200%)
    transform: translateY(1px)
    
</style>