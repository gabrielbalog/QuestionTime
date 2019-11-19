<template>
  <div class="container mt-2">
    <h1 class="mb-3">Ask a Question</h1>
    <form @submit.prevent="onSubmit">
      <textarea
        rows="3"
        v-model="question_body"
        class="form-control"
        placeholder="What do you want to ask?"
      ></textarea>
      <br />
      <button type="submit" class="btn btn-success">Publish</button>
    </form>
    <p v-if="error" class="muted mt-2">{{ error }}</p>
  </div>
</template>

<script>
import { apiService } from "../common/api.service";

export default {
  name: "QuestionEditor",
  props: {
    slug: {
      type: String,
      required: false
    }
  },
  data() {
    return {
      question_body: null,
      error: null
    };
  },
  methods: {
    onSubmit() {
      if (!this.question_body) {
        this.error = "You can't send an empty question!";
      } else if (this.question_body.length > 240) {
        this.error = "Ensure this field has no more than 240 characters";
      } else {
        let endpoint = "/api/questions/";
        let method = "POST";
        if (this.slug !== undefined) {
          endpoint += `${this.slug}/`;
          method = "PUT";
        }
        apiService(endpoint, method, {
          content: this.question_body
        }).then(question_data => {
          this.$router.push({
            name: "question",
            params: { slug: question_data.slug }
          });
        });
      }
    }
  },
  async beforeRouteEnter(to, from, next) {
    console.log(to.params.slug); // eslint-disable-line no-console
    if (to.params.slug !== undefined) {
      let endpoint = `/api/questions/${to.params.slug}/`;
      let data = await apiService(endpoint);
      console.log(data.content); // eslint-disable-line no-console
      return next(vm => (vm.question_body = data.content));
    } else {
      return next();
    }
  },
  created() {
    document.title = "Editor - QuestionTime";
  }
};
</script>