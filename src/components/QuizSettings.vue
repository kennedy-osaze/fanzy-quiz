<template>
  <b-container>
    <b-row>
      <b-col sm="12">
        <div class="mt-2 mb-2">
          <h6 class="display-4 text-center h6-d4-fs">Quiz Settings</h6>
        </div>
      </b-col>
    </b-row>

    <b-row>
      <b-col sm="12">
        <div class="quiz-form mt-2">
          <div v-if="errors.length" class="a">
            <b-alert show variant="danger">
              {{ errors[0] }}
            </b-alert>
          </div>

          <b-form @submit.prevent="handleSubmit">
            <b-form-group id="questions-no-group" label="No of Questions to Answer:" label-for="questions-no">
              <b-form-input type="number" v-model="numQuestions" id="questions-no" min="1" max="50"></b-form-input>
            </b-form-group>

            <b-form-group id="question-category-group" label="Select Questions Category:" label-for="question-category">
              <b-form-select id="question-category" v-model="category" :options="categories"></b-form-select>
            </b-form-group>

            <b-form-group id="question-difficulty-group" label="Select Questions Difficulty:" label-for="question-difficulty">
              <b-form-select id="question-difficulty" v-model="difficulty" :options="difficultyLevels"></b-form-select>
            </b-form-group>

            <div class="mt-5 text-center">
              <b-button type="submit" variant="success" class="px-5">Start Quiz</b-button>
            </div>
          </b-form>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
export default {
  name: "quiz-form",
  data() {
    return {
      numQuestions: 10,
      category: null,
      difficulty: null,
      errors: [],
    };
  },
  methods: {
    validateForm() {
      this.errors = [];

      if (this.numQuestions === '') {
        this.errors.push("The number of questions to answer is required");
      } else if (this.numQuestions < 1) {
        this.errors.push("The number of questions to answer must not be less than 1");
      } else if (this.numQuestions > 50) {
        this.errors.push("The number of questions to answer must be at most 50");
      }

      const category_values = this.categories.map(category => category.value);
      if (!category_values.includes(this.category)) {
        this.errors.push("The question category selected is invalid");
      }

      const difficulties_values = this.difficultyLevels.map(level => level.value);
      if (!difficulties_values.includes(this.difficulty)) {
        this.errors.push("The question difficulty level selected is invalid");
      }

      return Boolean(!this.errors.length);
    },
    handleSubmit() {
      if (this.validateForm()) {
        this.$emit('startQuiz', {
          numQuestions: this.numQuestions,
          category: this.categories.find(category => this.category === category.value),
          difficulty: this.difficultyLevels.find(level => this.difficulty === level.value)
        });
      }
    }
  },
  computed: {
    difficultyLevels() {
      return [
        { value: null, text: "Any Difficulty" },
        { value: "easy", text: "Easy" },
        { value: "medium", text: "Medium" },
        { value: "hard", text: "Hard" },
      ];
    },
    categories() {
      return [
        { value: null, text: 'Any Category' },
        { value: "9", text: 'General Knowledge' },
        { value: "10", text: "Entertainment: Books" },
        { value: "11", text: "Entertainment: Film" },
        { value: "12", text: "Entertainment: Music" },
        { value: "15", text: "Entertainment: Video Games" },
        { value: "29", text: "Entertainment: Comics" },
        { value: "16", text: "Entertainment: Board Games" },
        { value: "17", text: "Science & Nature" },
        { value: "30", text: "Science: Gadgets" },
        { value: "18", text: "Science: Computers" },
        { value: "19", text: "Science: Mathematics" },
        { value: "20", text: "Mythology" },
        { value: "21", text: "Sports" },
        { value: "22", text: "Geography" },
        { value: "23", text: "History" },
        { value: "24", text: "Politics" },
        { value: "25", text: "Art" },
        { value: "26", text: "Celebrities" },
        { value: "27", text: "Animals" },
        { value: "28", text: "Vehicles" },
      ];
    }
  }
}
</script>

<style scoped>

</style>
