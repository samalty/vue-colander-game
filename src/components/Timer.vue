<template>
  <div>
    <p>Timer</p>
    <p>{{ seconds }}</p>
  </div>
</template>

<script>
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
      seconds: 30
    }
  },
  methods: {
    timer() {
        this.seconds = this.settings.turnTime;
        this.seconds--;
        //if (this.seconds === 0) {

        //}
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>