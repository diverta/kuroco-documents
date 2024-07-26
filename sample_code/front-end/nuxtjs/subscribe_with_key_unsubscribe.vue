<template>
  <div>
    <h1>Unsubscribe to Magazine</h1>
    <div>
      Email: {{$route.query.email}}
    </div>
    <p v-if="resultMessage !== null">
      {{ resultMessage }}
    </p>
  </div>
</template>

<script>
export default {
  validate({ query }) {
    return /[!-~]{32}/.test(query.key)
  },
  data() {
    return {
      error: null,
      resultMessage: null,
    }
  },
  methods: {
    async unsubscribeSubmit() {
      try {
        const payload = {
          email: this.$route.query.email,
          key: this.$route.query.key
        }
        const response = await this.$axios.$post(`/rcms-api/1/magazine_unsubscribe_with_key/1`, payload)
        this.resultMessage = response.messages[0]
      } catch (error) {
        this.resultMessage = error.response.data.errors[0].message
      }
    }
  },
  mounted($route) {
    this.unsubscribeSubmit();
  }
}
</script>