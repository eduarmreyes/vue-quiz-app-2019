<template>
  <div>
    <b-jumbotron>
      <template slot="lead">{{parseHTMLCodes(currentQuestion.question)}}</template>

      <hr class="my-4" />

      <b-list-group v-for="(answer, index) in answers" :key="index">
        <b-list-group-item
          @click.prevent="selectAnswer(index)"
          :class="answeredClass(index)"
        >{{parseHTMLCodes(answer)}}</b-list-group-item>
      </b-list-group>

      <b-button @click="back" variant="info" :disabled="currentQuestionIndex === 0">Back</b-button>
      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >Send Answer</b-button>
      <b-button
        @click="next"
        variant="success"
        :disabled="currentQuestionIndex === totalQuestionsNumber"
      >Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
export default {
  props: {
    currentQuestion: Object,
    currentQuestionIndex: Number,
    totalQuestionsNumber: Number,
    back: Function,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      const answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      const answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = this.shuffleArray(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    /**
     * Randomly shuffle an array
     * https://stackoverflow.com/a/2450976/1293256
     * @param  {Array} array The array to shuffle
     * @return {String}      The first item in the shuffled array
     */
    shuffleArray(arrayToShuffle) {
      let currentIndex = arrayToShuffle.length;
      let temporaryValue, randomIndex;

      while (0 !== currentIndex) {
        // pick a remaining element
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;

        // swap it with the current element
        temporaryValue = arrayToShuffle[currentIndex];
        arrayToShuffle[currentIndex] = arrayToShuffle[randomIndex];
        arrayToShuffle[randomIndex] = temporaryValue;
      }

      return arrayToShuffle;
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    answeredClass(index) {
      return !this.answered && this.selectedIndex === index
        ? "selected"
        : this.answered && this.correctIndex === index
        ? "correct"
        : this.answered &&
          this.selectedIndex === index &&
          this.correctIndex !== index
        ? "incorrect"
        : "";
    },
    parseHTMLCodes(string) {
      let parser = new DOMParser();
      let dom = parser.parseFromString(
        "<!doctype html><body>" + string,
        "text/html"
      );
      return dom.body.textContent;
    }
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: lightcoral;
}
.btn {
  margin: 0 5px;
}
</style>
