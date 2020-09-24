<template>
  <div>
    <div class="team team1" v-bind:class="{ active: turn % 2 != 0 }">
        <h5>Team A</h5>
        <h5>{{ teamAScore }}</h5>
    </div>
    <div class="team team2" v-bind:class="{ active: turn % 2 == 0 }">
        <h5>Team B</h5>
        <h5>{{ teamBScore }}</h5>
    </div>
  </div>
</template>

<script>
import { EventBus } from '../main';
export default {
  name: 'Scores',
  data() {
    return {
      turn: 1,
      teamAScore: 0,
      teamBScore: 0
    }
  },
  //methods: {

  //},
  created() {
    EventBus.$on('newAnswer', (data) => {
      // Receive current answer from Gameplay component and update score
      this.answer = data;
      if (this.answer != 'Ready?') {
        this.turn % 2 != 0 ? this.teamAScore++ : this.teamBScore++ ;
      }
    });
    EventBus.$on('nextTurn', () => {
      // Update turn when button in gameplay component is clicked
      this.turn++;
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .team{
    font-family: 'Bree Serif', serif;
    font-size: 24px;
    float: left;
    width: 47%;
    padding: 5px;
    background-color: #000000;
    border-radius: 10px;
    border: 5px solid #ffffff;
    box-sizing: border-box;
    margin-bottom: 50px;
    transition: 0.5s;
  }

  .team1{
    float: left;
  }

  .team2{
    float: right;
  }

  h5{
    margin: 0;
  }

  .active{
    border: 5px solid #00b300;
    transition: 0.5s;
  }

</style>