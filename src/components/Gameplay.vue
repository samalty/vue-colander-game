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
    <button v-on:click="onGo" id="gotBtn" v-show="!betweenRounds">{{ btnText }}</button>
    <button v-on:click="onPass" id="passBtn" v-show="!betweenRounds">Pass</button>
    <button v-on:click="onNextTurn" id="nextBtn" v-show="!betweenRounds">Next Player</button>
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
      btnText: 'Go!',
      answerIndex: null,
      prevAnswerIndex: null,
      answered: [],
      passed: [],
      betweenRounds: false,
      onTheClock: false,
      time: null,
      elapsedTime: 0,
      count: undefined
    }
  },
  methods: {

    onGo() {
      if (this.onTheClock == false) {
        this.onTheClock = !this.onTheClock;
        // Establish time limit if null
        (this.time == null) ? this.time = this.settings.turnTime : '' ;
        this.btnText = 'Got';
        // Enable pass button
        document.getElementById("passBtn").disabled = false;
        // Call randomise function to return answer
        this.randomise();
        // Initiate timer
        this.startTime();
      } else {
        // Call onGot function if round is in progress
        this.onGot();
      }
    },

    startTime() {
      if (this.time > 0) {
        this.count = setTimeout(() => {
          this.time--;
          this.startTime()
        }, 1000)
      } else this.timeOut();
    },

    stopTime() {
      clearInterval(this.count);
      console.log(this.time + ' time');
      this.onTheClock = !this.onTheClock;
    },

    timeOut() {
      this.time = "Time's up! Next player's turn.";
      this.onTheClock = !this.onTheClock;
      document.getElementById("gotBtn").disabled = true;
      document.getElementById("passBtn").disabled = true;
      document.getElementById("nextBtn").disabled = false;
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
      if (this.prevAnswerIndex != null) {
        this.answered.push(this.answers[this.prevAnswerIndex]);
        this.answers.splice(this.prevAnswerIndex, 1);
      }
      console.log(this.answers);
      console.log(this.answered);

      if (this.answers.length > 0) {
        // Call randomise function to return answer
        this.randomise();
      } else if (this.passed.length > 0) {
        // Push passed item back into answers if no other remaining items
        this.answers.push(this.passed[0]);
        this.passed.splice(0, 1);
        this.randomise();
      } else {
        this.endRound();
        this.stopTime();
      }
    },

    endRound() {
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
    },

    onPass() {
      //if (this.passed.length == 1) {
      //  document.getElementById("passBtn").disabled = true;
      //} else {
        // Overwrite prevAnswerIndex for splicing answers array
        this.prevAnswerIndex = this.answerIndex;
        // Remove answered item from answered array and push into passed array
        (this.prevAnswerIndex) != null ? this.passed.push(this.answers.splice(this.prevAnswerIndex, 1)) : '' ;
        console.log(this.passed[0])
        // Call randomise function to return answer
        this.randomise();
        // Disable button to prevent further passes
        document.getElementById("passBtn").disabled = true;
      //}
    },

    onNextTurn() {
      // Use eventbus to trigger method in Scores component
      EventBus.$emit('nextTurn');
      // Reset time limit
      this.time = this.settings.turnTime;
      this.btnText = 'Go!';
      document.getElementById("gotBtn").disabled = false;
      document.getElementById("passBtn").disabled = false;
      document.getElementById("nextBtn").disabled = true;
    },

    onNextRound() {
      this.round++;
      this.betweenRounds = false;
      this.btnText = 'Go!';
      this.answer = 'Ready?';
      this.answers = this.answered;
      this.answered = [];
      document.getElementById("gotBtn").disabled = false;
      document.getElementById("passBtn").disabled = false;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>