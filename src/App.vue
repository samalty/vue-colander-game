<template>
  <div id="app">
    <Tabs v-show="!inGame">
      <Tab name="Play" selected=true>
        <Answers v-on:pushAnswer="receiveAnswers($event)" />
        <Settings v-bind:answers="answers" v-on:pushSettings="receiveSettings($event)" />
      </Tab>
      <Tab name="Instructions">
        <Instructions />
      </Tab>
    </Tabs>
    <Scores v-show="inGame" />
    <Gameplay v-bind:answers="answers" v-bind:settings="settings" v-show="inGame" />
  </div>
</template>

<script>
import Answers from './components/Answers.vue'
import Settings from './components/Settings.vue'
import Instructions from './components/Instructions.vue'
import Scores from './components/Scores.vue'
import Gameplay from './components/Gameplay.vue'
import Tab from './components/Tab.vue'
import Tabs from './components/Tabs.vue'

export default {
  name: 'App',
  components: {
    Answers,
    Settings,
    Instructions,
    Scores,
    Gameplay,
    Tab,
    Tabs
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
    receiveAnswers(newAnswer) {
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

  body{
    background-color: #abbec7;
    background-image: url("https://www.transparenttextures.com/patterns/carbon-fibre-big.png");
  }

  #app{
    max-width: 500px;
    height: auto;
    margin: auto;
  }
</style>
