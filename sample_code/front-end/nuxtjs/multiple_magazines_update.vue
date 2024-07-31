<template>
  <div>
    <h1>Update mail magazine</h1>
    <form>
      <p v-if="resultMessage !== null" style="color:green">{{ resultMessage }}</p>

      <div v-for="magazine in magazines" :key="magazine.id">
        <h2>{{ magazine.label }}</h2>
        <input type="radio" :id="`s-${magazine.id}`" :value="{ 'status': 'subscribe', 'id': magazine.id }"
          v-model="submitData[magazine.key]" @change="handleChange(magazine.key)" />
        <label :for="`s-${magazine.id}`">Subscribe</label>
        <input type="radio" :id="`u-${magazine.id}`" :value="{ 'status': 'unsubscribe', 'id': magazine.id }"
          v-model="submitData[magazine.key]" @change="handleChange(magazine.key)" />
        <label :for="`u-${magazine.id}`">Unsubscribe</label>
      </div>

    </form>
  </div>
</template>

<script>
export default {
  middleware: 'auth',

  data() {
    return {
      resultMessage: null,
      submitData: {
        news: { 'status': 'unsubscribe', 'id': 7 },
        sale: { 'status': 'unsubscribe', 'id': 8 },
        event: { 'status': 'unsubscribe', 'id': 9 },
      },
      magazines: [
        { id: 7, key: 'news', label: 'Announcement of the Latest Release' },
        { id: 8, key: 'sale', label: 'Notification of Exclusive Sale' },
        { id: 9, key: 'event', label: 'Announcement of the Latest Release' },
      ],
    };
  },
  async asyncData({ $axios }) {
    return {
      response: await $axios.$get('/rcms-api/18/my_magazine/info'),
    };
  },
  created() {
    //Reflect Subscription Status Upon Access
    if (this.response && this.response.list) {
      this.response.list.forEach((magazineInfo) => {
        const matchingMagazine = this.magazines.find(magazine => magazine.id === magazineInfo.magazine_id);
        if (matchingMagazine) {
          const key = matchingMagazine.key;
          this.$set(this.submitData, key, { 'status': 'subscribe', 'id': magazineInfo.magazine_id });
        }
      });
    }
  },
  methods: {
    async handleChange(magazineKey) {
      try {
        await this.$axios.$post(
          `/rcms-api/18/magazine/${this.submitData[magazineKey].status}/${this.submitData[magazineKey].id}`,
          { member_id: this.$store.state.profile.member_id }
        );
        this.resultMessage = 'Data submitted successfully';
      } catch (error) {
        this.resultMessage = 'Error submitting data';
        console.error(error);
      }
    },
  },
};
</script>