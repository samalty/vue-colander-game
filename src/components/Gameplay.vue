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
    <button v-on:click="onGot">Go!</button>
    <button>Pass</button>
    <button v-on:click="onNext">Next player</button>
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
      turn: 1,
      teamAScore: 0,
      teamBScore: 0
    }
  },
  methods: {
    onGot() {
      this.turn % 2 != 0 ? this.teamAScore++ : this.teamBScore++ ;
    },
    onNext() {
      this.turn++;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>