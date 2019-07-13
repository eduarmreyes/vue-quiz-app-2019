<template>
  <div>
    <b-jumbotron>
      <template slot="lead">{{currentQuestion.question}}</template>

      <hr class="my-4" />

      <b-list-group v-for="(answer, index) in answers" :key="index">
        <b-list-group-item
          @click.prevent="selectAnswer(index)"
          :class="[selectedIndex === index ? 'selected': '']"
        >{{answer}}</b-list-group-item>
      </b-list-group>

      <b-button variant="primary" @click="submitAnswer">Send Answer</b-button>
      <b-button @click="next" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
export default {
  props: { currentQuestion: Object, next: Function, increment: Function },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: []
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
      this.increment(isCorrect);
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
