<template>
    <div>
        <div v-if="!signupDone">
            <form @submit.prevent="signup">
                <p v-if="error" :style="{ color: 'red' }">
                    {{ error }}
                </p>
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
                    <input v-model="user.email" name="email" type="email" placeholder="email">
                </div>
                <div>
                    <label>login_pwd</label>
                    <input v-model="user.login_pwd" name="login_pwd" type="password" placeholder="login_pwd">
                </div>
                <div>
                    <button type="submit">
                        Signup
                    </button>
                </div>
            </form>
        </div>
        <div v-if="signupDone">
            Registration completed.
        </div>
    </div>
</template>
<script>
export default {
    data () {
        return {
            signupDone: false,
            user: {},
            error: null
        }
    },
    methods: {
        async signup () {
            try {
                // Create Payload
                const payload = {
                    email_send_ng_flg: false, // Set default value
                    ...this.user
                }
                // New Member Registration Request
                await this.$axios.$post('/rcms-api/18/member/insert',payload)
                this.signupDone = true
            } catch (e) {
                console.error(e)
                this.error = 'An error has occurred.'
            }
        }
    }
}
</script>