<template>
  <div>
    <h2>COLANDER GAME</h2>
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
  data() {
    return {
      answer: null
    }
  },
  methods: {
    onSubmit(e) {
      e.preventDefault();
      if (this.answer) {
        let newAnswer = this.answer;
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

  h2{
    font-size: 30px;
  }

  p{
    font-size: 18px;
  }

  form{
    width: 75%;
    margin: auto;
    text-align: center;
  }

  input{
    width: 100%;
    font-size: 18px;
    margin: 5px;
    box-sizing: border-box;
    font-family: 'Bree Serif', serif;
  }

</style>