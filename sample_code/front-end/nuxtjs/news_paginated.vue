<template>
  <div>
    <p>News list page{{ this.$route.query.page }}</p>
    <div v-for="n in response.list" :key="n.slug">
      <nuxt-link :to="`/news_list/${n.topics_id}`">
        {{ n.ymd }} {{ n.subject }}
      </nuxt-link>
    </div>

  </div>
</template>

<script>
export default {
  async asyncData({ $axios, route }) {
    return {
      response: await $axios.$get('/rcms-api/7/news', {
        params: {
          pageID: route.query.page || 1,
        },
      }),
    };
  },
};
</script>