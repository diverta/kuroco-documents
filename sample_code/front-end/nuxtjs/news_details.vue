<template>
  <div>
    <h1 class="title">{{ response.details.subject }}</h1>
    <div class="post" v-html="response.details.contents"></div>
  </div>
</template>

<script setup>
import { useFetch } from 'nuxt3';
import { ref } from 'vue';
import { useRoute } from 'vue-router';

const response = ref(null);
const route = useRoute();

useFetch(async ({ $axios }) => {
  response.value = await $axios.$get(`/rcms-api/4/newsdetail/${route.value.params.slug}`);
})

</script>
