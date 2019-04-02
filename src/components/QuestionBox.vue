<template>
  <div class="mt-4 text-center" v-if="error">
    <p>{{ error }}</p>

    <div class="mt-5">
      <b-button variant="primary" @click="$emit('reload')">Refresh App</b-button>
    </div>
  </div>

  <div v-else class="question-box-container">
    <div>
      <b-nav tabs class="justify-content-end mt-2 border-bottom-0">
        <b-nav-item disabled>Question: {{ currentQuestionNum }}/{{ questionsTotal }}</b-nav-item>
        <b-nav-item disabled>Scored: {{ correctAnswersCount }}</b-nav-item>
        <b-nav-item disabled>Attempted: {{ attemptedQuestionsCount  }}</b-nav-item>
      </b-nav>
    </div>

    <b-jumbotron>
      <template slot="lead">
        <div class="wrap-text text-center" v-html="currentQuestion.question"></div>
      </template>

      <hr class="my-4">
      <b-list-group class="mb-3">
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          v-html="answer"
          :class="[answerClass(index), 'pointer']"
          @click="selectAnswer(index)"
        >
        </b-list-group-item>
      </b-list-group>

      <div class="text-center mt-4">
        <b-button
          variant="primary"
          :class="'mr-2'"
          @click="submitAnswer"
          :disabled="selectedAnswerIndex === null || answered"
        >
          Submit
        </b-button>

        <b-button v-on:click="$emit('next', currentResult)" variant="success">{{ isLastQuestion() ? 'Done' : 'Next' }}</b-button>
      </div>
    </b-jumbotron>
  </div>
</template>

<script>
  import _ from 'lodash';

  export default {
    name: 'question-box',
    props: {
      questionsTotal: {
        type: Number,
        default: 0
      },
      currentQuestion: {
        type: Object,
        default: {}
      },
      error: {
        type: String,
        default: ''
      },
    },
    data() {
      return {
        selectedAnswerIndex: null,
        correctAnswerIndex: null,
        shuffledAnswers: [],
        answered: false,
        correctAnswersCount: 0,
        attemptedQuestionsCount: 0,
      };
    },
    computed: {
      currentQuestionNum() {
        return (this.questionsTotal) ? this.currentQuestion.index + 1 : 0;
      },
      answers() {
        return [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
      },
      currentResult() {
        return {
          attemptedQuestionsCount: this.attemptedQuestionsCount,
          correctAnswersCount: this.correctAnswersCount,
        };
      }
    },
    methods: {
      selectAnswer(index) {
        if (!this.answered) {
          this.selectedAnswerIndex = index;
        }
      },
      shuffleAnswers() {
        this.shuffledAnswers = _.shuffle(this.answers);
        this.correctAnswerIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
      },
      submitAnswer() {
        this.answered = true;

        if (this.selectedAnswerIndex === this.correctAnswerIndex) this.correctAnswersCount++;

        this.attemptedQuestionsCount++;
      },
      answerClass(index) {
        if (!this.answered && this.selectedAnswerIndex === index) return 'selected';

        if (this.answered && index === this.correctAnswerIndex) return 'correct';

        if (this.answered && index !== this.correctAnswerIndex && this.selectedAnswerIndex === index) return 'incorrect';

        return '';
      },
      isLastQuestion() {
        const lastQuestionIndex = this.questionsTotal - 1;
        return lastQuestionIndex === this.currentQuestion.index;
        this.$emit('endQuiz');
      }
    },
    watch: {
      currentQuestion() {
        this.selectedAnswerIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  };
</script>

<style scoped>
  @media (min-width: 576px) {
    .jumbotron {
      padding: 3rem 2rem;
    }
  }

  .wrap-text {
    overflow-wrap: break-word;
    word-wrap: break-word;
    hyphens: auto;
  }

  .pointer {
    cursor: pointer;
  }

  .list-group-item:hover, .selected {
    color: #0c5460;
    background-color: #bee5eb80;
    border-color: #bee5eb80;
  }

  .correct {
    color: #155724;
    background-color: lightgreen;
    border-color: lightgreen;
  }

  .incorrect {
    color: #721c24;
    background-color: #f8d7da;
    border-color: #f5c6cb;
  }

  .brand {
    font-size: 1.5em;
    font-weight: 500;
  }
  
  .brand a {
    color: slategrey;
  }
</style>
