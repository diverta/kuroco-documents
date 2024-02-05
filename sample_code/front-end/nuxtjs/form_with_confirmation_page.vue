<template>
  <div>
    <section>
      <h1>Contact</h1>
      <form v-if="!validated" class="input">
        <div v-if="error" class="error">
          <p v-for="(err, idx) in error" :key="idx">
            {{ err }}
          </p>
        </div>
        <div>
          <dl>
            <dt>Name</dt>
            <dd><input v-model="submitData.name" name="name" type="text" :disabled="validated"></dd>
          </dl>
          <dl>
            <dt>Email</dt>
            <dd><input v-model="submitData.email" name="email" type="text" :disabled="validated"></dd>
          </dl>
          <dl>
            <dt>Message</dt>
            <dd><textarea v-model="submitData.body" name="body" :disabled="validated"></textarea></dd>
          </dl>
        </div>
        <button @click.prevent="handleOnValidate">Confirm your entry</button>
      </form>
      <form v-if="validated" class="confirm">
        <div v-if="submitted">Inquiry submitted.</div>
        <div v-else>
          <div>
            <div>Name   :{{submitData.name}}</div>
            <div>Email  :{{submitData.email}}</div>
            <div>Message:{{submitData.body}}</div>
          </div>
          <div>
            <button @click.prevent="handleOnSubmit">Submit</button>
            <button @click.prevent="validated = false">Back</button>
          </div>
        </div>
      </form>
    </section>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useAxios } from '@nuxtjs/axios';

const { $post } = useAxios();

const submitted = ref(false);
const validated = ref(false);
const submitData = ref({});
const error = ref(null);

const handleOnValidate = async () => {
  //Validate the entry
  try {
    await $post('/rcms-api/1/form_validate', { ...submitData.value });
    validated.value = true;
    error.value = null;
  } catch (e) {
    error.value = e.response.data.errors;
  }
};

const handleOnSubmit = async () => {
  //Post processing to Kuroco endpoints
  try {
    await $post('/rcms-api/1/form_send', { ...submitData.value });
    submitted.value = true;
  } catch (e) {
    console.log(e);
  }
};
</script>