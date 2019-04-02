<template>
  <div id="app">
    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="8" offset-sm="2">
          <b-nav tabs class="mb-3">
            <b-nav-item class="brand" disabled>Fanzy Quiz App</b-nav-item>
          </b-nav>
          
          <Home v-if="page === 'home'" @begin="showQuizSettings"/>

          <QuizSettings v-else-if="page === 'quiz-form'" @startQuiz="startQuiz"/>

          <QuestionBox
            v-else-if="page === 'question-box'"
            :questionsTotal="quiz.questions.length"
            :currentQuestion="currentQuestion"
            :error="quiz.error"
            @next="gotoNextQuestion"
            @endQuiz="endQuiz"
            @reload="reload"
          />

          <QuizResult
            v-else-if="page === 'quiz-result'"
            :quizSettings="settings"
            :quizDetails="quiz"
            @reload="reload"
          />

        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import axios from 'axios';
import Home from './components/Home';
import QuizSettings from './components/QuizSettings';
import QuestionBox from './components/QuestionBox';
import QuizResult from './components/QuizResult';

export default {
  name: 'app',
  components: { Home, QuizSettings, QuestionBox, QuizResult },
  data() {
    return {
      page: "home",

      settings: {
        numQuestions: 0,
        category: null,
        difficulty: null
      },

      quiz: {
        timer: { start: 0, end: 0 },
        questions: [],
        currentQuestionIndex: 0,
        currentResult: {},
        error: '',
      }
    };
  },
  methods: {
    showQuizSettings() {
      this.page = "quiz-form";
    },
    startQuiz(quizSettings) {
      this.settings = Object.assign({}, this.settings, quizSettings);
      this.getQuizQuestions();

      this.page = "question-box";
      this.quiz.timer.start = Date.now();
    },
    endQuiz() {
      this.page = 'quiz-result';
      this.quiz.timer.end = Date.now();
    },
    gotoNextQuestion(result) {
      this.quiz.currentResult = result;
      this.quiz.currentQuestionIndex++;
    },
    reload() {
      this.page = "home";
    },
    getQuizQuestions() {
      const requestParams = {
        amount: this.settings.numQuestions,
        type: 'multiple',
        ...(this.settings.category && this.settings.category.value !== null)
          && { category: this.settings.category.value },
        ...(this.settings.difficulty && this.settings.difficulty.value !== null)
          && { difficulty: this.settings.difficulty.value }
      };

      axios.get('https://opentdb.com/api.php', {
        params: requestParams
      })
        .then(({ data }) => {
          if (data.response_code === 1) {
            throw new Error('There are insufficient questions for the selected category');
          }

          if (!Array.isArray(data.results) || !data.results.length) {
            throw new Error('No question was returned');
          }

          this.quiz.questions = data.results;
        })
        .catch(error => {
          this.quiz.questions = [];

          this.quiz.error = 'Oops! There was a problem fetching the questions. ';
          this.quiz.error += (error.message === 'Network Error')
            ? 'Please check your internet connection.'
            : error.message;
        });
    },
  },
  computed: {
    currentQuestion() {
      if (!this.quiz.questions.length) return {};

      return Object.assign(
        this.quiz.questions[this.quiz.currentQuestionIndex],
        { index: this.quiz.currentQuestionIndex }
      );
    },
  },
  watch: {
    'quiz.currentQuestionIndex': function (newIndex, oldIndex) {
      if (!this.quiz.questions[newIndex]) {
        this.quiz.currentQuestionIndex = oldIndex;
        this.endQuiz();
      }
    }
  }
}
</script>

<style>
  #app {
    font-family: "Roboto", Helvetica Neue, Helvetica, Arial, sans-serif;
    line-height: 1.5384616;
    color: #333333;
    margin-top: 50px;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  .btn {
    border-radius: 0.15rem !important;
  }

  .brand {
    font-size: 1.2em;
    font-weight: 500;
    background: #00695C;
  }
  
  .brand a {
    color: #fff !important;
  }

  .h6-d4-fs {
    font-size: 1.6rem;
  }
</style>
