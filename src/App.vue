<template>
  <div id="app">
    <Answers v-on:pushAnswer="receiveAnswer($event)" v-show="!inGame" />
    <Settings v-bind:answers="answers" v-on:pushSettings="receiveSettings($event)" v-show="!inGame" />
    <Gameplay v-bind:answers="answers" v-bind:settings="settings" v-show="inGame" />
  </div>
</template>

<script>
import Answers from './components/Answers.vue'
import Settings from './components/Settings.vue'
import Gameplay from './components/Gameplay.vue'

export default {
  name: 'App',
  components: {
    Answers,
    Settings,
    Gameplay
  },
  data() {
    return {
      answers: [],
      settings: {
        rounds: null,
        turnTime: null
      },
      inGame: false
    }
  },
  methods: {
    receiveAnswer(newAnswer) {
      this.answers.push(newAnswer);
      console.log(this.answers);
    },
    receiveSettings(gameSettings) {
      this.settings.rounds = gameSettings.rounds;
      this.settings.turnTime = gameSettings.turnTime;
      this.inGame = true;
      console.log(this.settings);
    }
  }
}
</script>

<style>

</style>
