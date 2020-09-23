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

</style>