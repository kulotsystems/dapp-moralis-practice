<template>
    <div>
        <button @click="login">Login with Metamask</button>

        <div v-if="user">
            <h3>USER</h3>
            <pre>{{ user }}</pre>
        </div>

        <div v-if="error" style="color: red">
            <h3>ERROR</h3>
            {{ error.message }}
        </div>
    </div>
</template>

<script>
    /**
     *
     * Web3 integration to Vite solved with:
     * https://stackoverflow.com/questions/72075742/error-with-web3js-nodejs-utils-in-vite-react-ts-app-how-to-resolve-typeerror
     *
     */
    import Web3 from 'web3';
    import Moralis from 'moralis';

    export default {
        name: 'App',
        components: {},
        data() {
            return {
                user: null,
                web3: null,

                error: null
            }
        },
        computed: {},
        methods : {
            login() {
                this.error = null;
                Moralis.Web3.authenticate()
                    .then(user => {
                        this.user = user;

                        // set some properties
                        this.user.set('lastAccess', new Date());
                        this.user.save();

                        console.log(this.user);
                    })
                    .catch(error => {
                        console.log('USER AUTHENTICATE: ', error);
                        this.error = error;
                    });
            }
        },
        created() {
            Moralis.initialize(import.meta.env.VITE_MORALIS_APP_ID);
            Moralis.serverURL = import.meta.env.VITE_MORALIS_SERVER_URL;

            // Enable web3 and get the initialized web3 instance from Web3.js
            Moralis.enableWeb3()
                .then(response => {
                    this.web3 = new Web3(Moralis.provider);
                })
                .catch(error => {
                    console.log('ENABLE WEB3: ', error)
                });
        }
    }
</script>

<style scoped>

</style>