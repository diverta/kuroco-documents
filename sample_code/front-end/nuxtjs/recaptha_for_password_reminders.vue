<template>
  <div>
      <section>
          <h1>Password reset</h1>
          <form>
              <div v-if="error" class="error">
                  <p v-for="(err, idx) in error" :key="idx">
                      {{ err }}
                  </p>
              </div>
              <div v-if="submitted">Email has been sent.</div>
              <input v-model="emailEntered" name="email" type="email" placeholder="email" />
              <inquiry-recaptcha ref="recaptcha" :is-succeeded.sync="canSubmit" />
              <button @click.prevent="handleOnSubmit" :disabled="!canSubmit">Submit</button>
          </form>
      </section>
  </div>
</template>

<script>
//Load Components
import InquiryRecaptcha from '@/components/inquiry/inquiry-recaptcha.vue'

export default {
  components: {
      InquiryRecaptcha,
  },
  data() {
      return {
          submitted: false,
          emailEntered: "",
          error: null,
          canSubmit: false,
      }
  },
  methods: {
      async handleOnSubmit() {
          try {
              const payload = {
                  email: this.emailEntered,
                  recaptcha_response: await this.$refs.recaptcha.fetchResponse(),
              }
              await this.$axios.$post('/rcms-api/1/reminder_with_recaptcha', payload)
              this.submitted = true
              this.error = null
          } catch (e) {
              this.error = e.response.data.errors
          }
          await this.$recaptcha.reset();
      }
  }
};
</script>