<template>
  <div style="width: 550px;" class="home d-inline-flex flex-column flex-wrap justify-content-center">
    <h1>Welcome to the Trivia Quiz!</h1>
    <h2>Your current score is {{this.score}}/{{this.questions.length}}</h2>
    <div v-for="question in questions" :key="question">
      <div class="alert alert-info" role="alert">
        {{unescape(question.question)}}
      </div>
      <div class="d-flex flex-wrap">
        <button v-for="answer in question.possible_answers" :key="answer" v-bind:id="answer" v-on:click="checkAnswer(question, answer)" style="flex: 45%; margin: 5px;" type="button" class="btn btn-primary">{{unescape(answer)}}</button>
      </div>
      <br>
      <br>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import axios from 'axios'

@Options({
  methods: {
    unescape (message: string) {
      return message.replaceAll('&quot;', '"').replaceAll('&#039;', "'").replaceAll('&amp;', '&').replaceAll('&lt;', '<').replaceAll('&gt;', '>').replaceAll('&#038;', '&').replaceAll('&shy;', '-')
    },
    checkAnswer (question: any, answer: string) {
      var clickedElement = document.getElementById(answer)
      if (clickedElement == null) {
        return
      }
      if (question.correct_answer === answer) {
        this.score++
        this.revealAnswer(question)
      } else {
        clickedElement.classList.add('btn-danger')
        clickedElement.classList.remove('btn-primary')
        this.revealAnswer(question)
      }
    },
    revealAnswer (question: any) {
      var correctElement = document.getElementById(question.correct_answer)
      if (correctElement != null) {
        correctElement.classList.add('btn-success')
        correctElement.classList.remove('btn-primary')
      }
    }
  },
  data () {
    return {
      questions: [],
      score: 0
    }
  },
  mounted () {
    axios.get('https://opentdb.com/api.php?amount=10&category=9&difficulty=easy&type=multiple')
      .then(response => {
        const results = response.data.results
        results.forEach((question: any) => {
          const possibleAnswers = [question.correct_answer].concat(question.incorrect_answers).sort((a, b) => 0.5 - Math.random())
          this.questions.push({
            question: question.question,
            possible_answers: possibleAnswers,
            correct_answer: question.correct_answer
          })
        })
      })
  }
})

export default class Home extends Vue {}
</script>
