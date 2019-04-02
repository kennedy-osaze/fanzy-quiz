<template>
  <div class="quiz-result">
    <p class="display-4 text-center text-success">Congratulations! You have concluded your set quiz successfully</p>

    <div class="table-responsive">
      <table class="table table-bordered">
        <caption class="table-caption">Quiz Result</caption>
        <tbody>
          <tr>
            <td>Questions Category:</td>
            <td>{{ quizSettings.category.text }}</td>
          </tr>
          <tr>
            <td>Questions Difficulty:</td>
            <td>{{ quizSettings.difficulty.text }}</td>
          </tr>
          <tr>
            <td>Total Number of Questions:</td>
            <td>{{ quizSettings.numQuestions }}</td>
          </tr>
          <tr>
            <td>Time Spent on Quiz:</td>
            <td>{{ quizDuration }}</td>
          </tr>
          <tr>
            <td>Number of Questions Attempted:</td>
            <td>{{ quizDetails.currentResult.attemptedQuestionsCount }}</td>
          </tr>
          <tr>
            <td>Number of Questions Skipped:</td>
            <td>{{ questionsSkippedCount }}</td>
          </tr>
          <tr>
            <td>Number of Question Answered Correctly:</td>
            <td>{{ quizDetails.currentResult.correctAnswersCount }}</td>
          </tr>
          <tr>
            <td>Number of Questions Answered Incorrectly:</td>
            <td>{{ questionsAnsweredIncorrectlyCount }}</td>
          </tr>
          <tr>
            <td>Score in Percentage:</td>
            <td>{{ scoreInPercentage }}%</td>
          </tr>
          <tr>
            <td>Grade:</td>
            <td :class="['text-white', (grade.pass) ? 'bg-success' : 'bg-danger']">{{ grade.text }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="mt-5 text-center">
      <b-button variant="primary" @click="$emit('reload')">Let's Do This Again</b-button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'quiz-result',
  props: {
    quizSettings: Object,
    quizDetails: Object
  },
  computed: {
    questionsSkippedCount() {
      return this.quizDetails.questions.length - this.quizDetails.currentResult.attemptedQuestionsCount;
    },
    questionsAnsweredIncorrectlyCount() {
      return this.quizDetails.currentResult.attemptedQuestionsCount - this.quizDetails.currentResult.correctAnswersCount;
    },
    scoreInPercentage() {
      const score = (this.quizDetails.currentResult.correctAnswersCount / this.quizDetails.questions.length) * 100;
      // return Math.round(score * 100) / 100; /* Round score to two decimals places if needed */
      return _.round(score, 2);
    },
    grade() {
      return this.scoreInPercentage >= 50 ? { pass: true, text: 'Pass' } : { pass: false, text: 'Fail' };
    },
    quizDuration() {
      const { start, end } = this.quizDetails.timer;
      let diff = Math.abs(end - start) / 1000;

      let h = Math.floor(diff / 3600);
      diff -= h * 3600;

      let m = Math.floor(diff / 60);
      diff -= m * 60;

      let s = Math.floor(diff);
      
      let duration = '';
      if (h > 0) duration += `${h} ${(h === 1) ? 'hour' : 'hours'} `;
      if (m > 0) duration += `${m} ${(m === 1) ? 'minute' : 'minutes'} `;
      if (s > 0) duration += `${s} ${(s === 1) ? 'second' : 'seconds'}`;

      return duration;
    }
  }
};
</script>

<style scoped>
  .table-caption {
    caption-side: top;
    text-align: center;
    font-size: 1.2rem;
    font-weight: 400;
    line-height: 1.2;
  }

  .display-4 {
    font-size: 1.5rem;
  }

  tr td:last-child {
    text-align: center;
  }
</style>
