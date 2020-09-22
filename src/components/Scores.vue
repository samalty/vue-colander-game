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
  answer: {
    type: String,
    required: true
  },
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
          this.answer = data;
          console.log(this.answer + ' answer')
          if (this.answer != 'Ready?') {
            this.turn % 2 != 0 ? this.teamAScore++ : this.teamBScore++ ;
          }
      });
      EventBus.$on('nextTurn', () => {
          this.turn++;
      });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>