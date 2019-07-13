<template>
  <div id="app">
    <Header
      :numberOfCorrectAnswers="numberOfCorrectAnswers"
      :numberOfTotalAnswers="numberOfTotalAnswers"
    />
    <b-container>
      <b-row class="justify-content-center">
        <b-col md="6">
          <QuestionBox
            v-if="questions.length"
            :currentQuestion="questions[currentQuestionIndex]"
            :currentQuestionIndex="currentQuestionIndex"
            :totalQuestionsNumber="questions.length - 1"
            :next="next"
            :back="back"
            :increment="increment"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QuestionBox from "./components/QuestionBox.vue";

export default {
  name: "app",
  components: {
    Header,
    QuestionBox
  },
  data() {
    return {
      questions: [],
      currentQuestionIndex: 0,
      totalQuestionsNumber: -1,
      numberOfCorrectAnswers: 0,
      numberOfTotalAnswers: 0
    };
  },
  methods: {
    next() {
      this.currentQuestionIndex += 1;
    },
    back() {
      this.currentQuestionIndex -= 1;
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numberOfCorrectAnswers += 1;
      }
      this.numberOfTotalAnswers += 1;
    }
  },
  mounted: function() {
    fetch("https://opentdb.com/api.php?amount=10&category=27&type=multiple", {
      method: "get"
    })
      .then(response => {
        return response.json();
      })
      .then(jsonData => {
        this.questions = jsonData.results;
      });
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
