<template>
  <div>
    <form class="settings-form" @submit.prevent="onSubmit">
      <p>How many rounds do you want to play?</p>
      <input type="radio" v-model.number="rounds" value="3" id="three" checked="checked">
      <label for="three">3 (excluding blanket round)</label>
      <input type="radio" v-model.number="rounds" value="4" id="four">
      <label for="four">4 (including blanket round)</label>
      <p class="question">How many seconds per turn?</p>
      <input type="radio" v-model.number="turnTime" value="30" id="thirty" checked="checked">
      <label for="thirty">30</label>
      <input type="radio" v-model.number="turnTime" value="45" id="fourtyfive">
      <label for="fourtyfive">45</label>
      <input type="radio" v-model.number="turnTime" value="60" id="sixty">
      <label for="sixty">60</label>
      <p>
        <input type="submit" value="Begin game">
      </p>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Settings',
  props: {
    answers: {
        type: Array,
        required: true
    }
  },
  data() {
    return {
      rounds: 3,
      turnTime: 30
    }
  },
  methods: {
    onSubmit() {
      if (this.answers.length >= 3) {
        let gameSettings = {
            rounds: this.rounds,
            turnTime: this.turnTime
        }
        //console.log(gameSettings);
        this.$emit('pushSettings', gameSettings);
        let answers = this.answers;
        //eventBus.$emit('gameplay', gameSettings);
        this.$emit('gameplay', answers);
      } else {
        alert("There must be at least 3 items in the colander in order to play. You currently have " + this.answers.length + " items.");
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>