<template>
  <div>
    <h5>Timer</h5>
    <p>{{ timer }}</p>
  </div>
</template>

<script>
import { EventBus } from '../main';
export default {
  name: 'Timer',
  props: {
    settings: {
        type: Object,
        required: true
    }
  },
  data() {
    return {
      count: 30,
      displaySeconds: 30,
      inTurn: false
    }
  },
  computed: {
    timer() {
      const timeRemaining = this.count;
      const minutes = Math.floor(timeRemaining / 60);
      let seconds = timeRemaining % 60;
      (seconds < 10) ? seconds = `0${seconds}` : '' ;
      return `${minutes}:${seconds}`;
    }
  },
  created() {
    EventBus.$on('stopWatch', (data) => {
      // Receive current answer from Gameplay component and update score
      this.inTurn = data;
      if (this.inTurn == true) {
        this.seconds--;
      }
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>