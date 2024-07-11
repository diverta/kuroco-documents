<template>
  <div>
    <h1 class="title">{{ response.details.subject }}</h1>
    <div class="post" v-html="response.details.contents"></div>
    <p v-if="resultMessage !== null">
      {{ resultMessage }}
    </p>
    <div>
      please type your name: <input v-model="userName" type="text" placeholder="your name">
    </div>
    <div>
      <ul v-for="comment in comments" :key="comment.comment_id">
        <li>
          {{ comment.note }} by {{ comment.name }}
          <button v-if="commentHistory.map(({ id }) => id).includes(comment.comment_id)" type="button"
            @click="() => deleteComment(comment.comment_id)">
            delete
          </button>
        </li>
      </ul>
      <form @submit.prevent="submitComment">
        <input v-model="inputComment" type="text" placeholder="comment">
        <button type="submit" :disabled="inputComment === '' || userName === ''">
          submit
        </button>
      </form>
    </div>
  </div>
</template>

<script>
async function getAllComments(topics_id) {
  const { list } = await this.$axios.$get(
    '/rcms-api/21/comments',
    {
      params: {
        module_id: topics_id,
        cnt: 999
      }
    }
  )
  return list
}

const COMMENT_HISTORY_KEY = 'CommentHistory'

export default {
  async asyncData({ $axios, params }) {
    try {
      const response = await $axios.$get(
        '/rcms-api/21/newsdetail/1047'
      )
      return { response, comments: await getAllComments.call({ $axios }, response.details.topics_id) }
    } catch (e) {
      console.log(e.message)
    }
  },
  data() {
    return {
      userName: '',
      response: null,
      comments: [],
      inputComment: '',
      commentHistory: [],
      resultMessage: null,
    }
  },
  mounted() {
    this.commentHistory = JSON.parse(localStorage.getItem(COMMENT_HISTORY_KEY)) || []
  },
  methods: {
    async submitComment() {
      const delkey = `${this.userName}_${Date.now()}`
      const submitResponse = await this.$axios.$post('/rcms-api/21/comment', {
        module_id: this.response.details.topics_id,
        name: this.userName,
        note: this.inputComment,
        delkey
      })
      this.addCommentHistory({ id: submitResponse.id, delkey })
      this.comments = await getAllComments.call(this, this.response.details.topics_id)
      this.inputComment = ''
    },
    async deleteComment(commentId) {
      try {
        await this.$axios.$post(`/rcms-api/21/comment_delete/${commentId}`, {
          delkey: this.commentHistory.find(({ id }) => `${id}` === `${commentId}`).delkey
        })
        this.deleteCommentHistory(commentId)
        this.comments = await getAllComments.call(this, this.response.details.topics_id)
        this.inputComment = ''
      } catch (error) {
        this.resultMessage = error.response.data.errors[0].message
      }
    },
    addCommentHistory(payload) {
      const restored = JSON.parse(localStorage.getItem(COMMENT_HISTORY_KEY)) || []
      restored.push(payload)
      localStorage.setItem(COMMENT_HISTORY_KEY, JSON.stringify(restored))
      this.commentHistory = restored
    },
    deleteCommentHistory(commentId) {
      const restored = JSON.parse(localStorage.getItem(COMMENT_HISTORY_KEY)) || []
      const filtered = restored.filter(({ id }) => `${id}` !== `${commentId}`)
      localStorage.setItem(COMMENT_HISTORY_KEY, JSON.stringify(filtered))
      this.commentHistory = filtered
    }
  }
}
</script>