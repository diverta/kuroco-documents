<template>
    <div>
        <div v-if="!presignupDone">
            <form @submit.prevent="signup">
                <div v-if="error" :style="{ color: 'red' }">
                    <p v-for="(error, idx) in error" :key="idx">
                        {{ error.message }}
                    </p>
                </div>
                <div>
                    <label>name1</label>
                    <input v-model="user.name1" name="name1" type="text" placeholder="name1">
                </div>
                <div>
                    <label>name2</label>
                    <input v-model="user.name2" name="name2" type="text" placeholder="name2">
                </div>
                <div>
                    <label>email</label>
                    <input v-model="email" name="email" type="email" placeholder="email">
                </div>
                <div>
                    <button type="submit">
                        Sign UP
                    </button>
                </div>
            </form>
        </div>
        <div v-else-if="presignupDone">
            Your pre-registration has been completed. Please check your e-mail.
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            presignupDone: false,
            email: null,
            user: {},
            error: null
        }
    },
    methods: {
        async signup() {
            try {
                const payload = {
                    email: this.email,
                    ext_info: {
                        ...this.user
                    },
                }
                // post data
                await this.$axios.$post('/rcms-api/33/member_invite', payload)
                this.presignupDone = true
            } catch (e) {
                this.error = e.response.data.errors
            }
        },
    },
}
</script>
