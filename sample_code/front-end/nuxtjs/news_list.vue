<template>
  <div>
    <p>News list</p>
    <div v-for="n in response.list" :key="n.slug">
      <nuxt-link :to="`/news/${n.topics_id}`">
        {{ n.ymd }} {{ n.subject }}
      </nuxt-link>
    </div>
  </div>
</template>

<script setup>
import { useFetch } from 'nuxt3';
import { ref } from 'vue';

const response = ref(null);

useFetch(async ({ $axios }) => {
  response.value = await $axios.$get('/rcms-api/4/news');
})

</script>
