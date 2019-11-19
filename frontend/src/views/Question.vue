<template>
  <div class="single-question mt-2">
    <div class="container">
      <h1>{{ question.content }}</h1>
      <p class="mb-0">
        Posted By:
        <span class="author-name">{{ question.author }}</span>
      </p>
      <p>{{ question.created_at }}</p>
      <hr />
    </div>
    <div class="container">
      <AnswerComponent v-for="(answer, index) in answers" :key="index" :answer="answer"></AnswerComponent>
    </div>
  </div>
</template>

<script>
import { apiService } from "../common/api.service";
import AnswerComponent from "../components/Anwser";

export default {
  name: "Question",
  props: {
    slug: {
      type: String,
      required: true
    }
  },
  components: {
    AnswerComponent
  },
  data() {
    return {
      question: {},
      answers: []
    };
  },
  methods: {
    setPageTitle(title) {
      document.title = title;
    },
    getQuestionData() {
      let endpoint = `/api/questions/${this.slug}/`;
      apiService(endpoint).then(data => {
        this.question = data;
        this.setPageTitle(data.content);
      });
    },
    getQuestionAnswers() {
      let endpoint = `/api/questions/${this.slug}/answers/`;
      apiService(endpoint).then(data => {
        this.answers = data.results;
      });
    }
  },
  created() {
    this.getQuestionData();
    this.getQuestionAnswers();
  }
};
</script>

<style>
.author-name {
  font-weight: bold;
  color: #dc3545;
}
</style>
