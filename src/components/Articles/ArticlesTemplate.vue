<template>
  <div class="col-lg c-articles flex">
    <Alert :text="errors" v-if="haveError"/>
    <button
      :class="[showForm ? 'btn-danger' : 'btn-primary' ,'btn']"
      @click="handleShowForm"
    >{{ showForm ? 'Close' : 'Add Article' }}
    </button>

    <FormArticle
      v-show="showForm"
      @save-article="saveArticle"
      @validate-error="showErrors"
    />
    <ListArticles :articles="articles" />
  </div>
</template>

<script>
import FormArticle from './FormArticle'
import Alert from './Alert'
import ListArticles from '@/components/Articles/ListArticles'

export default {
  name: 'ArticlesTemplate',
  components: {
    ListArticles,
    Alert,
    FormArticle
  },
  data () {
    return {
      articles: [],
      showForm: false,
      haveError: false,
      errors: ''
    }
  },
  methods: {
    handleShowForm () {
      this.showForm = !this.showForm
    },
    saveArticle (article) {
      this.errors = ''
      this.haveError = false
      this.saveArticleInDatabase(article)
    },
    showErrors (error) {
      this.haveError = true
      this.errors = error
    },
    async saveArticleInDatabase (article) {
      fetch('api/v1/article', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(article)
      })
        .then(response => response.json())
        .then(data => {
          this.articles.push(data)
          console.log(data)
        })
        .catch(console.log)
    },
    async getArticlesToDatabase () {
      const response = await fetch('api/v1/article')
      const data = await response.json()

      this.articles = [...data]
    }
  },
  mounted () {
    this.getArticlesToDatabase()
  }
}
</script>

<style scoped>
.c-articles {
  background-color: rgba(202, 210, 197, .2);
  border-radius: 4px;
  padding: 10px;
}
</style>
