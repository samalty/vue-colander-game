<template>
  <div>
    <div>
      <p class="round">{{ rounds[round] }}</p>
    </div>
    <div>
      <p class="answers">{{ answer }}</p>
    </div>
    <div>
      <p class="results" v-show="gameOver">{{ result }}</p>
    </div>
    <div>
      <p class="timer" v-bind:class="{ warning: time <= 10 }" 
                       v-show="showTimer">{{ time }}</p>
    </div>
    <div class="buttons">
      <button v-on:click="onGo"
              v-bind:disabled="!canGo"
              v-bind:class="{ disabled: !canGo }"
              class="button"
              id="gotBtn"
              v-show="!betweenRounds">{{ btnText }}</button>
      <button v-on:click="onPass"
              v-bind:disabled="!canPass"
              v-bind:class="{ disabled: !canPass }"
              class="button"
              id="passBtn"
              v-show="!betweenRounds">Pass</button>
      <button v-on:click="onNextTurn"
              v-bind:disabled="!canNextTurn"
              v-bind:class="{ disabled: !canNextTurn }"
              class="button"
              id="nextBtn"
              v-show="!betweenRounds">Next Player</button>
      <button v-on:click="onNextRound"
              class="button"
              id="nextRoundBtn"
              v-show="betweenRounds">{{ nextBtnText }}</button>
    </div>
    <span class="audio">
      <audio id="audio1"><source src="../media/stopwatch.wav" type="audio/mpeg"></audio>
      <audio id="audio2"><source src="../media/alarm.wav" type="audio/mpeg"></audio>
    </span>
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
      nextBtnText: 'Next round >>',
      answerIndex: null,
      prevAnswerIndex: null,
      answered: [],
      passed: [],
      betweenRounds: false,
      onTheClock: false,
      time: null,
      canGo: true,
      canPass: false,
      canNextTurn: false,
      gameOver: false,
      showTimer: true,
      result: null
    }
  },
  methods: {

    onGo() {
      if (this.onTheClock == false) {
        this.onTheClock = true;
        // Establish time limit if null
        (this.time == null) ? this.time = this.settings.turnTime : '' ;
        this.btnText = 'Got it!';
        this.canPass = true;
        // Call randomise function to return answer and initiate timer
        this.randomise();
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
          this.startTime();
          this.ticktock();
        }, 1000)
      } else this.timeOut();
    },

    ticktock() {
      const stopwatch = document.getElementById('audio1');
      (this.onTheClock == true) ? stopwatch.play() : stopwatch.pause() ;
    },

    stopTime() {
      clearInterval(this.count);
      this.onTheClock = false;
    },

    timeOut() {
      (this.passed.length > 0) ? this.emptyPass() : '' ;
      this.answer = "Time's up! Next player's turn.";
      this.showTimer = false;
      this.onTheClock = !this.onTheClock;
      this.canGo = !this.canGo;
      this.canPass = false;
      this.canNextTurn = !this.canNextTurn;
      this.ticktock();
      this.alarm();
    },

    alarm() {
      const alarm = document.getElementById('audio2');
      alarm.play();
    },

    randomise() {
      // Randomise new item from answers array
      this.answerIndex = Math.floor(Math.random() * this.answers.length);
      this.answer = this.answers[this.answerIndex];
    },

    emptyPass() {
      // Pass passed answer back into answers array
      this.answers.push(this.passed[0][0]);
      this.passed.splice(0, 1);
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
      if (this.answers.length > 0) {
        // Call randomise function to return answer
        this.randomise();
      } else if (this.passed.length > 0) {
        this.onTheClock = false;
        this.emptyPass();
        this.randomise();
      } else {
        this.endRound();
        this.stopTime();
      }
    },

    onPass() {
      // Overwrite prevAnswerIndex for splicing answers array
      this.prevAnswerIndex = this.answerIndex;
      // Remove answered item from answered array and push into passed array
      (this.prevAnswerIndex) != null ? this.passed.push(this.answers.splice(this.prevAnswerIndex, 1)) : '' ;
      // Call randomise function to return answer
      this.randomise();
      // Disable button to prevent further passes
      this.canPass = !this.canPass;
    },

    endRound() {
      this.betweenRounds = true;
      this.showTimer = false;
      this.answerIndex = null;
      this.prevAnswerIndex = null;
      this.ticktock();
      if (this.round < this.settings.rounds-1) {
        this.answer = "Colander is empty. Click 'next round' to resume.";
      } else {
        this.gameOver = true;
        this.answer = null;
        this.nextBtnText = 'Home';
      }
    },

    onNextTurn() {
      // Use eventbus to trigger method in Scores component
      EventBus.$emit('nextTurn');
      // Reset time limit
      this.time = this.settings.turnTime;
      this.btnText = 'Go!';
      this.answer = 'Ready?';
      this.canGo = !this.canGo;
      this.showTimer = true;
      this.canPass = false;
      this.canNextTurn = !this.canNextTurn;
    },

    onNextRound() {
      if (this.round < this.settings.rounds-1) {
        // Update settings for next round
        this.round++;
        this.betweenRounds = false;
        this.btnText = 'Go!';
        this.answer = 'Ready?';
        this.answers = this.answered;
        this.answered = [];
        this.showTimer = true;
        this.canPass = false;
        // Else reload application for new game
      } else location.reload();
    }

  },
  updated() {
    EventBus.$on('score', (data) => {
      this.score = data;
      switch (true) {
        case (this.score[0] > this.score[1]):
          this.result = 'Game over. Team A wins!';
          break;
        case (this.score[0] < this.score[1]):
          this.result = 'Game over. Team B wins!';
          break;
        case (this.score[0] == this.score[1]):
          this.result = "Game over. It's a draw!";
          break;
      }
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .round{
    color: #ffff00;
    font-size: 30px;
    transition: 0.5s;
  }

  .answers,
  .timer{
    font-size: 36px;
  }

  .results{
    font-size: 40px;
  }

  .warning{
    color: #cc0000;
  }

  .buttons{
    position: absolute;
    bottom: 10px;
    width: 100%;
    max-width: 500px;
    height: auto;
  }

  .button{
    font-size: 18px;
    font-family: 'Bree Serif', serif;
    box-sizing: border-box;
    width: 48%;
    padding: 20px;
    margin: 3px;
    color: #ffffff;
    border-radius: 5%;
    cursor: pointer;
    transition: 0.5s;
  }

  #gotBtn{
    background-image: linear-gradient(#008800, #00b300);
    width: 98%;
    margin: auto;
    border: 3px solid #00b300;
  }

  #passBtn{
    background-image: linear-gradient(#990000, #cc0000);
    border: 3px solid #cc0000;
  }

  #nextBtn{
    background-image: linear-gradient(#cc7a00, #ff9900);
    border: 3px solid #ff9900;
  }

  #nextRoundBtn{
    background-image: linear-gradient(#cc7a00, #ff9900);
    width: 98%;
    margin: auto;
    border: 3px solid #ff9900;
  }

  .disabled{
    color: #999999;
    transition: 0.5s;
  }

</style>