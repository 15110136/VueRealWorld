<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-10 offset-md-1 col-xs-12">
          <list-error :errors="error"/>
          <form @submit.prevent="onPublish(article.slug);">
            <fieldset :disabled="inProgress">
              <fieldset class="form-group">
                <input
                  type="text"
                  class="form-control form-control-lg"
                  v-model="article.title"
                  placeholder="Article Title"
                >
              </fieldset>

              <fieldset class="form-group">
                <input
                  type="text"
                  class="form-control"
                  v-model="article.description"
                  placeholder="What's this article about?"
                >
              </fieldset>

              <fieldset class="form-group">
                <textarea
                  class="form-control"
                  rows="8"
                  v-model="article.body"
                  placeholder="Write your article (in markdown)"
                ></textarea>
              </fieldset>
              <fieldset class="form-group">
                <input
                  type="text"
                  class="form-control"
                  placeholder="Enter tags"
                  v-model="tagInput"
                  v-on:keypress.enter.prevent="addTag(tagInput);"
                >
                <div class="tag-list">
                  <span
                    class="tag-default tag-pill"
                    v-for="(tag, index) of article.tagList"
                    :key="tag + index"
                  >
                    <i class="ion-close-round" v-on:click="removeTag(tag);"></i>
                    {{ tag }}
                  </span>
                </div>
              </fieldset>
            </fieldset>
            <button
              class="btn btn-lg pull-xs-right btn-primary"
              :disabled="inProgress"
              type="submit"
            >Publish</button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ListErrorVue from "../components/ListError.vue";
import { mapGetters } from "vuex";
import {
  ARTICLE_EDIT,
  ARTICLE_PUBLISH,
  ARTICLE_EDIT_REMOVE_TAG,
  ARTICLE_EDIT_ADD_TAG
} from "../store/actionType";
export default {
  name: "ArticleEdit",
  components: {
    "list-error": ListErrorVue
  },
  props: {
    previousArticle: {
      type: Object
    }
  },
  data() {
    return {
      tagInput: null,
      inProgress: false,
      error: {}
    };
  },
  computed: {
    ...mapGetters(["article"])
  },
  methods: {
    onPublish(slug) {
      let action = slug ? ARTICLE_EDIT : ARTICLE_PUBLISH;
      // console.log(action);

      this.inProgress = true;
      this.$store
        .dispatch(action)
        .then(({ data }) => {
          this.inProgress = false;
          console.log(data.article.slug);
          this.$router.push({
            name: 'home'
          });
        })
        .catch(({ response }) => {
          this.inProgress = false;
          console.log(this.error);
          
          this.error = response.data.errors;
          console.log(this.error);
        });
    },
    removeTag(tag) {
      this.$store.dispatch(ARTICLE_EDIT_REMOVE_TAG, tag);
    },
    addTag(tag) {
      this.$store.dispatch(ARTICLE_EDIT_ADD_TAG, tag);
      this.tagInput = null;
    }
  }
};
</script>