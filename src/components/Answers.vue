<template>
  <div>
    <form class="answers-form" @submit.prevent="onSubmit">
      <p>
        <input v-model="answer">
      </p>
      <p>    
        <input type="submit" value="Add to colander">
      </p>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Answers',
  //props: {
  //  msg: String
  //},
  data() {
    return {
      answer: null
    }
  },
  methods: {
    onSubmit() {
      if (this.answer) {
        let newAnswer = this.answer;
        console.log(newAnswer + ' <<');
        this.$emit('pushAnswer', newAnswer);
      } else {
        alert("The form field is empty.");
      }
      this.answer = null;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>