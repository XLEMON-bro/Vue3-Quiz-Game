<template>
  
  <div>

    <ScoreBoard :winCount="this.winCount" :loseCount="this.lostCount"/>

    <template v-if="this.question">

      <h1 v-html="this.question">
      </h1>
      
      <template v-for="(answer, index) in this.answers" :key="index">
        <input 
        :disabled="this.answerSubmited"
        type="radio" 
        name="options" 
        :value="answer"
        v-model="this.chosenAnswer">
        
        <label v-html="answer"></label><br>  
      </template>
      
      <button v-if="!this.answerSubmited" @click="this.submitAnswer()" class="send" type="button">Send</button>

      <section v-if="this.answerSubmited" class="result">
        <h4 v-if="this.chosenAnswer == this.correctAnswer" 
        v-html="'&#9989; Congratulations, the answer &#x201D;' + this.chosenAnswer  + '&#x201D; is correct.'">
        </h4>
        <h4 v-else 
        v-html="'&#10060; You picked the wrong answer. The correct is &#x201D;' + this.correctAnswer + '&#x201D;.'">
        </h4>
        <button @click="this.getNewQuestion()" class="send" type="button">Next question</button>
      </section>
      
    </template>
  </div>
  
</template>

<script>

import ScoreBoard from './components/ScoreBoard.vue';


export default {

  name: 'App',

  components: {
    ScoreBoard
  },

  data(){
    return{
      question: undefined,
      incorectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmited: false,
      winCount: 0,
      lostCount: 0
    }
  },
  computed:{
    answers(){
      var answers = [...this.incorectAnswers]; // (pass value and not the reference) also same as JSON.parse(JSON.stringify(this.incorectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if(!this.chosenAnswer){
        alert('Pickone of the options');
      }else{
        this.answerSubmited = true;

        if(this.chosenAnswer == this.correctAnswer){
          this.winCount++;
        }else{
          this.lostCount++;
        }
      }
    },
    getNewQuestion(){
      this.answerSubmited = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      
      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
    }
  },
  created(){
    this.getNewQuestion();
  }
}

//https://opentdb.com/api.php?amount=1&category=18

//https://opentdb.com/api.php?amount=10

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio]{
    margin: 12px 4px;
  }

  button.send{
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    border-radius: 6px;
    cursor: pointer;
  }
}


</style>
