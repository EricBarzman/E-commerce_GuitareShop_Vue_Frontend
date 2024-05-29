<template>
    <div class="page-signup">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Sign up</h1>

                <form @submit.prevent="submitForm">
                <div class="field">
                    <label>Username</label>
                    <div class="control">
                        <input type="text" class="input" v-model="username">
                    </div>
                </div>

                <div class="field">
                    <label>Password</label>
                    <div class="control">
                        <input type="password" class="input" v-model="password">
                    </div>
                </div>

                <div class="field">
                    <label>Confirm password</label>
                    <div class="control">
                        <input type="password" class="input" v-model="passwordConfirm">
                    </div>
                </div>

                <div class="notification is-danger" v-if="errors.length">
                    <p v-for="error in errors" :key="error">{{ error }}</p>
                </div>

                <div class="field">
                    <div class="control">
                        <button class="button is-dark">Sign up</button>
                    </div>
                </div>

                <hr>
                Already signed up? <router-link to="/login">Log in</router-link>
            </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'Signup',
    data() {
        return {
            username: '',
            password: '',
            passwordConfirm: '',
            errors: [],
        }
    },
    mounted() {
        document.title = 'Sign up | GuitareShop'
    },
    methods : {
        submitForm() {
            this.errors = []
            if (this.username === '')
                this.errors.push('You need to provide an username')
            
            if (this.password ==='')
                this.errors.push('You need to provide a password')

            if (this.password !== this.passwordConfirm)
                this.errors.push('Both passwords do not match')

            if (!this.errors.length) {
                const formData = {
                    username : this.username,
                    password: this.password
                }

                axios
                    .post('api/v1/users/', formData)
                    .then(response => {
                        toast({
                            message: 'Your account was successfully created! Please log in',
                            type: 'is-success',
                            dismissible: true,
                            pauseOnHover: true,
                            duration: 2000,
                            position: 'bottom-right',
                            animate: { in: 'fadeIn', out: 'fadeOut' },
                        })
                        this.$router.push('/login')
                    })
                    .catch(error => {
                        if (error.response) {
                            for (const property in error.response.data) {
                                this.errors.push(`${property}: ${error.response.data[property]}`)
                            }
                            console.log(JSON.stringify(error.response.data))
                        } else if (error.message) {
                            this.errors.push('Something went wrong. Please try again')
                            console.log(JSON.stringify(error))
                        }
                    })
            }
        }
    }
}
</script>