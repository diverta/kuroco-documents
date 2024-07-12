<template>
    <div>
        <div v-if="signupDone">
            Registration completed.
        </div>
        <div v-if="error_url" :style="{ color: 'red' }">
            <p v-for="(error, idx) in error_url" :key="idx">
                {{ error.message }}
            </p>
        </div>
        <div v-else>
            <form @submit.prevent="registUser">
                <div>
                    <label>name1</label>
                    <input name="name1" type="text" :value=invitationRes.data.data.ext_info.name1 disabled>
                </div>
                <div>
                    <label>name2</label>
                    <input name="name2" type="text" :value=invitationRes.data.data.ext_info.name2 disabled>
                </div>
                <div>
                    <label>email</label>
                    <input name="email" type="email" :value=invitationRes.data.data.email disabled>
                </div>
                <div>
                    <label>login_pwd</label>
                    <input v-model="login_pwd" name="login_pwd" type="password" placeholder="login_pwd">
                </div>
                <div>
                    <button type="submit">
                        Sign UP
                    </button>
                </div>
            </form>
            <div v-if="error" :style="{ color: 'red' }">
                <p v-for="(error, idx) in error" :key="idx">
                    {{ error.message }}
                </p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    validate({ query }) {
        //Return 404 if key is invalid
        return /[!-~]{32}/.test(query.key)
    },
    data() {
        return {
            signupDone: false,
            login_pwd: "",
            error: null
        }
    },
    //Obtain pending  member information
    async asyncData({ $axios, query}) {
        try {
            const invitationRes = await $axios.post('/rcms-api/33/member_invite',
                {
                    email_hash: query.key
                });
            return { invitationRes };
        } catch (e) {
            return { error_url: e.response.data.errors };
        }
    },
    //Regist user
    methods: {
        async registUser() {
            try {
                const payload = {
                    login_pwd: this.login_pwd,
                    email: this.invitationRes.data.data.email,
                    ...this.invitationRes.data.data.ext_info
                }
                await this.$axios.post('/rcms-api/33/member_regist', payload);
                this.signupDone = true;
            } catch (e) {
                this.error = e.response.data.errors;
            }
        }
    },
}
</script>
