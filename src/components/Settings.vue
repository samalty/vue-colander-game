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
        // Emit game settings and answers to gameplay component
        let gameSettings = {
            rounds: this.rounds,
            turnTime: this.turnTime
        }
        this.$emit('pushSettings', gameSettings);
        let answers = this.answers;
        this.$emit('gameplay', answers);
        // Disable buttons in gameplay component
        document.getElementById("passBtn").disabled = true;
        document.getElementById("nextBtn").disabled = true;
      } else {
        alert("There must be at least 3 items in the colander in order to play. You currently have " + this.answers.length + " items.");
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  form{
    width: 100%;
    margin: auto;
    text-align: center;
  }

  input[type=radio]{
    opacity: 0;
    width: 0;
    margin: 0;
  }

  label[for="three"],
  label[for="four"],
  label[for="thirty"],
  label[for="fourtyfive"],
  label[for="sixty"]{
    font-family: 'Bree Serif', serif;
    display: inline-block;
    cursor: pointer;
    padding: 10px;
    color: #ffffff;
    border: 2px solid #ffffff;
    background-image: linear-gradient(#990000, #cc0000);
  }

  label[for="three"],
  label[for="four"]{
    width: 230px;
    margin: 5px;
  }

  label[for="thirty"],
  label[for="fourtyfive"],
  label[for="sixty"]{
    width: 50px;
    margin: 7px;
  }

  input:checked + label[for="three"],
  input:checked + label[for="four"],
  input:checked + label[for="thirty"],
  input:checked + label[for="fourtyfive"],
  input:checked + label[for="sixty"]{
    background-image: linear-gradient(#008800, #00b300);
  }

</style>