<template>
  <div>
    <form>
      <h1>Subscribe to Magazine</h1>
      <p v-if="resultMessage !== null">
        {{ resultMessage }}
      </p>
      <input v-model="emailEntered" name="email" type="email" placeholder="email">
      <button v-on:click.prevent="subscribeSubmit">Subscribe</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      emailEntered: '',
      resultMessage: null,
    }
  },
  methods: {
    //Subscribe
    async subscribeSubmit() {
      try {
        const payload = {
          email: this.emailEntered
        }
        // post data
        const response = await this.$axios.$post(`/rcms-api/1/magazine_subscribe_with_key/1`, payload)
        this.resultMessage = response.messages[0]
      } catch (error) {
        this.resultMessage = error.response.data.errors[0].message
      }
    }
  }
}
</script>