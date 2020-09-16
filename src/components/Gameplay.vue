<template>
  <div>
    <div>
        <h5>Team A</h5>
        <h5>{{ teamAScore }}</h5>
    </div>
    <div>
        <h5>Team B</h5>
        <h5>{{ teamBScore }}</h5>
    </div>
    <div>
        <h5>{{ rounds[round] }}</h5>
    </div>
    <div>
        <h5>{{ answer }}</h5>
    </div>
    <button v-on:click="onGot" id="goBtn">Go!</button>
    <button v-on:click="onPass" id="passBtn">Pass</button>
    <button v-on:click="onNext">Next Player</button>
    <button v-on:click="onNextRound" v-show="betweenRounds">Next Round</button>
  </div>
</template>

<script>
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
      turn: 1,
      teamAScore: 0,
      teamBScore: 0,
      answer: 'Ready?',
      answerIndex: null,
      prevAnswerIndex: null,
      answered: [],
      passed: [],
      betweenRounds: false
    }
  },
  methods: {
    onGot() {
      if (this.answer != 'Ready?') {
        // Increment score for active team upon correct answer
        this.turn % 2 != 0 ? this.teamAScore++ : this.teamBScore++ ;
      }
      if (this.answers.length > 1) {
        console.log(this.answers.length)
        // Overwrite prevAnswerIndex for splicing answers array
        this.prevAnswerIndex = this.answerIndex;
        // Remove answered item from answers array and push into answered array
        (this.prevAnswerIndex) != null ? this.answered.push(this.answers.splice(this.prevAnswerIndex, 1)) : '' ;
        //console.log(this.prevAnswer)
        console.log('Answered array ' + this.answered);
        console.log(this.answered);
        console.log('Answers array ' + this.answers);
        console.log(this.answers)
        // Randomise new item from answers array
        this.answerIndex = Math.floor(Math.random() * this.answers.length);
        this.answer = this.answers[this.answerIndex];
      } else {
        this.answer = "Colander is empty. Click 'next round' to resume.";
        this.betweenRounds = true;
        console.log('empty array');
        document.getElementById("goBtn").disabled = true;
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
        // Randomise new item from answers array
        this.answerIndex = Math.floor(Math.random() * this.answers.length);
        this.answer = this.answers[this.answerIndex];
        document.getElementById("passBtn").disabled = true;
      }
    },
    onNext() {
      this.turn++;
    },
    onNextRound() {
      console.log(this.answered)
      this.round++;
      this.answer = "Ready?";
      this.betweenRounds = false;
      document.getElementById("goBtn").disabled = false;
      //this.answers = this.answered;
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