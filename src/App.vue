<script>

import { ref, computed } from 'vue'

export default {
  setup () {

    const gameOver = ref(false)

    const nextLevel = () => {
      let guess = Math.floor(Math.random() * 55) + 1
      let bad = -1
      if (guess >= 45) bad = 10 // 10
      else if (guess >= 36) bad = 9
      else if (guess >= 28) bad = 8
      else if (guess >= 21) bad = 7
      else if (guess >= 15) bad = 6
      else if (guess >= 11) bad = 5
      else if (guess >= 7) bad = 4
      else if (guess >= 4) bad = 3
      else if (guess >= 2) bad = 2
      else if (guess >= 1) bad = 1
      return bad
    }

    const newGame = () => {
      gameOver.value = false
      rows.value = [nextLevel()]
      choices.value = [null]
    }

    const newStep = (step) => {
      if (gameOver.value) return

      rows.value.unshift(nextLevel())
      //console.log(rows.value)
      choices.value[0] = step
      choices.value.unshift(null)
    }

    const selectStep = (step) => {
      console.log('selectStep')
      if (step === rows.value[0]) {
        gameOver.value = true
      } else {
        newStep(step)
      }
    }

    const score = computed(() => {
      return choices.value.reduce((sum, item) => sum + item, 0 )
    })

    let rows = ref([nextLevel()])
    let choices = ref([null])


    const rowsReduced = computed(() => {
      let r = rows.value.slice(0, 10)
      console.log(r)
      return r
    })

    //console.log(rows.value)

    return {
      selectStep,
      newGame,

      gameOver,
      rows,
      rowsReduced,
      choices,
      score
    }
  }
}
</script>

<template lang="pug">
article
  header
    .round
      .info
        .small Step
        .large {{ rows.length }}
    .game-over(v-if="gameOver")
      .game-status Game Over
      .controls
        button(type="button", @click="newGame") New Game
    .score
      .info
        .small Score
        .large {{ score }}
  section
    .game(:class="{'gameover': gameOver}")
      .steps(v-for="(row, rowIndex) in rowsReduced")
        .step(v-for="n in 10", @click="selectStep(n)", :class="{'selected': choices[rowIndex] === n, 'broken': row === n}") {{ n }}

</template>

<style lang="stylus">
html, body
  padding 0
  margin 0
  background-color #111
  font-family Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  height 100vh
  color #ddd

article
  padding 50px 20px 0
  margin 0 auto
  max-width 600px

header
  display flex
  justify-content space-between
  margin-bottom 100px
  
  .info
    display flex
    flex-direction column
    align-items center
    justify-content center
    border 2px solid #ddd
    border-radius 6px
    height 100px
    width 150px
    .small
      color #999
      text-transform uppercase
    .large
      font-size 32px

.game-over
  text-align center
.game-status
  font-size 32px
  margin-bottom 10px
.controls
  button
    font-family inherit
    padding 10px 20px
    font-size 16px


.game
  display flex
  flex-wrap wrap
  .steps
    display flex
    transition .3ms
    .step
      align-items center
      border 2px solid #ddd
      border-radius 6px
      display flex
      justify-content center
      margin 3px
      height 50px
      width 50px
      &.selected
        background-color #00A36C
        border-color #00A36C
    for num in (1..10)
      &:nth-child({num+1})
        opacity (.5-((num+(num*2))/100))
        transform scale((1-((num+(num*2))/100)))
  &:not(.gameover)
    .steps
      &:nth-child(1)
        .step:hover
          cursor pointer
          background-color #00A36C
  &.gameover
    .steps
      .step
        &.broken
          background-color #EE4B2B
          border-color #EE4B2B
        
</style>
