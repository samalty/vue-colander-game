<template>
  <div>
    <div>
        <h5>{{ rounds[round] }}</h5>
    </div>
    <div>
        <h5>{{ answer }}</h5>
    </div>
    <div>
        <h5>{{ time }}</h5>
    </div>
    <button v-on:click="onGo" id="gotBtn">Go!</button>
    <button v-on:click="onPass" id="passBtn">Pass</button>
    <button v-on:click="onNextTurn">Next Player</button>
    <button v-on:click="onNextRound" v-show="betweenRounds">Next Round</button>
  </div>
</template>

<script>
import { EventBus } from '../main';
export default {
  name: 'Gameplay',
  props: {
    answers: {
        type: Array,
        required: true
    },
    settings: {
        type: Object,
        required: true
    }
  },
  data() {
    return {
      rounds: ['Round 1: Articulate', 'Round 2: Charades', 'Round 3: Articulate V2', 'Round 4: Charades V2'],
      round: 0,
      answer: 'Ready?',
      answerIndex: null,
      prevAnswerIndex: null,
      answered: [],
      passed: [],
      betweenRounds: false,
      onTheClock: false,
      time: null
    }
  },
  methods: {

    onGo() {
      if (this.onTheClock == false) {

        this.onTheClock = !this.onTheClock;
        this.time = this.settings.turnTime;
        console.log(this.onTheClock);
        // Call randomise function to return answer
        this.randomise();
        // Initiate timer
        this.timer();
      } else {
        // Call onGot function if round is in progress
        this.onGot();
      }
    },

    timer() {
      if (this.time > 0) {
        setTimeout(() => {
          this.time--;
          this.timer()
        }, 1000)
      } else {
        this.time = "Time's up!";
        this.onTheClock = !this.onTheClock;
      }
    },

    pause() {
      console.log('inside pause function')
      clearTimeout(this.time);
    },

    randomise() {
      // Randomise new item from answers array
      this.answerIndex = Math.floor(Math.random() * this.answers.length);
      this.answer = this.answers[this.answerIndex];
    },

    onGot() {
      // Emit answer to Scores component to update scores
      EventBus.$emit('newAnswer', this.answer);

      // Overwrite prevAnswerIndex for splicing answers array
      this.prevAnswerIndex = this.answerIndex;

      // Remove answered item from answers array and push into answered array
      //(this.prevAnswerIndex) != null ? this.answered.push(this.answers.splice(this.prevAnswerIndex, 1)) : '' ;
      if (this.prevAnswerIndex != null) {
        this.answered.push(this.answers[this.prevAnswerIndex]);
        this.answers.splice(this.prevAnswerIndex, 1);
      }
      console.log(this.answers);
      console.log(this.answered);
      if (this.answers.length > 0) {
        // Call randomise function to return answer
        this.randomise();
      } else {
        if (this.round < this.settings.rounds-1) {
          this.answer = "Colander is empty. Click 'next round' to resume.";
          this.betweenRounds = true;
        } else {
          switch (true) {
            case (this.teamAScore > this.teamBScore):
              this.answer = 'Game over. Team A wins!';
              break;
            case (this.teamAScore < this.teamBScore):
              this.answer = 'Game over. Team B wins!';
              break;
            case (this.teamAScore == this.teamBScore):
              this.answer = "Game over. It's a draw!";
              break;
          }
        }
        this.answerIndex = null;
        this.prevAnswerIndex = null;
        document.getElementById("gotBtn").disabled = true;
      }
    },

    onPass() {
      if (this.passed.length == 1) {
        console.log('Too many items')
      } else {
        // Overwrite prevAnswerIndex for splicing answers array
        this.prevAnswerIndex = this.answerIndex;
        // Remove answered item from answered array and push into passed array
        (this.prevAnswerIndex) != null ? this.passed.push(this.answers.splice(this.prevAnswerIndex, 1)) : '' ;
        console.log('Passed array ' + this.passed)
        // Call randomise function to return answer
        this.randomise();
        document.getElementById("passBtn").disabled = true;
      }
    },

    onNextTurn() {
      // Use eventbus to trigger method in Scores component
      EventBus.$emit('nextTurn');
    },

    onNextRound() {
      this.round++;
      this.betweenRounds = false;
      document.getElementById("gotBtn").disabled = false;
      this.answer = 'Ready?';
      this.answers = this.answered;
      this.answered = [];
    }
  //},
  //computed: {
  //  onNextRound() {
  //    this.round++;
  //    this.answer = "Ready?";
  //    // Refill answers array for next round
  //    this.answers = this.answered.slice();
  //    this.answered.splice(0, this.answered.length);
  //    console.log(this.answers);
  //  }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>