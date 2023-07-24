<template>
  <div>
    <section>
      <h1>Contact
      </h1>
      <form>
        <div v-if="error" class="error">
          <p v-for="(err, idx) in error" :key="idx">
            {{ err }}
          </p>
        </div>

        <div v-if="submitted">Inquiry submitted.</div>

        <div>
          <dl>
            <dt>Name</dt>
            <dd><input v-model="submitData.name" :name=name type="text"></dd>
          </dl>
          <dl>
            <dt>Email</dt>
            <dd><input v-model="submitData.from_mail" :name=from_mail type="text"></dd>
          </dl>
          <dl>
            <dt>Messege</dt>
            <dd><textarea v-model="submitData.body" :name=body></textarea></dd>
          </dl>
        </div>

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
      submitData: {},
      error: null,
      canSubmit: false,
    }
  },
  async asyncData({ $axios }) {
    return {
      response: await $axios.$get('/rcms-api/1/form/12'),
    };
  },
  methods: {
    async handleOnSubmit() {
      //Authentication by reCAPTCHA
      this.submitData.recaptcha_response = await this.$refs.recaptcha.fetchResponse();
      //Post processing to Kuroco endpoints
      try {
        await this.$axios.$post('/rcms-api/1/form?id=12',{ ...this.submitData });
        this.submitted = true;
        this.error = null;
      } catch (e) {
        this.error = e.response.data.errors;
      }
      await this.$recaptcha.reset();
    }
  }
};
</script>