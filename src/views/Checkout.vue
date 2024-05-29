<template>
    <div class="page-checkout">
        <div class="column is-12">
            <h1 class="title">Checkout</h1>
        </div>

        <div class="column is-12 box">
            <table class="table is-fullwidth">
                <thead>
                    <tr>
                        <th>Product</th>
                        <th>Price</th>
                        <th>Quantity</th>
                        <th>Total</th>
                    </tr>
                </thead>

                <tbody>
                    <tr v-for="item in cart.items" :key="item.product.id">
                        <td>{{ item.product.name }}</td>
                        <td>{{ item.product.price }}</td>
                        <td>{{ item.quantity }}</td>
                        <td>${{ getItemTotal(item).toFixed(2) }}</td>
                    </tr>
                </tbody>

                <tfoot>
                    <tr>
                        <td colspan="2">Total</td>
                        <td>{{ cartTotalLength }}</td>
                        <td>${{ cartTotalPrice.toFixed(2) }}</td>
                    </tr>
                </tfoot>
            </table>

            <div class="column is-12 box">
                <h2 class="subtitle">Shipping details</h2>
                <p class="has-text-grey mb-4">*All fields are required</p>
                
                <div class="columns is-multiline">
                    <div class="column is-6">
                        <div class="field">
                            <label>First name<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="text" class="input" v-model="firstname">
                            </div>
                        </div>

                        <div class="field">
                            <label>Last name<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="text" class="input" v-model="lastname">
                            </div>
                        </div>

                        <div class="field">
                            <label>Email<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="email" class="input" v-model="email">
                            </div>
                        </div>

                        <div class="field">
                            <label>Phone<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="text" class="input" v-model="phone">
                            </div>
                        </div>
                    </div>

                    <div class="column is-6">
                        <div class="field">
                            <label>Address<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="text" class="input" v-model="address">
                            </div>
                        </div>

                        <div class="field">
                            <label>Zip code<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="text" class="input" v-model="zipcode">
                            </div>
                        </div>

                        <div class="field">
                            <label>City<span class="has-text-grey">*</span></label>
                            <div class="control">
                                <input type="text" class="input" v-model="city">
                            </div>
                        </div>
                    </div>
                </div>

                <div class="notification is-danger mt-4" v-if="errors.length">
                    <p class="has-text-white" v-for="error in errors" :key="error">{{ error }}</p>
                </div>

                <hr>
                <div id="card-element" class="mb-5"></div>

                <template v-if="cartTotalLength" class="">
                    <hr>
                    <button class="button is-dark" v-on:click="submitForm">Pay with Stripe</button>
                </template>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'Checkout',
    data() {
        return {
            cart: {
                items: []
            },
            stripe : {},
            card : {},
            firstname : '',
            lastname: '',
            email: '',
            phone: '',
            address: '',
            zipcode: '',
            city: '',
            errors: [],
        }
    },
    mounted() {
        document.title = 'Checkout | GuitareShop'
        this.cart = this.$store.state.cart

        if (this.cartTotalLength > 0) {
            this.stripe = Stripe(
                'pk_test_51PHn7KRtQqmmGrZ8g9Ajd7hfhwbDDgi1gZwFk6ggTZOe7ksQhZehRACDkDhnvu1y7gCvZ5LhGWsCDjRfHSswOODh003moVGl4D'
            )
            const elements = this.stripe.elements();
            this.card = elements.create('card', { hidePostalCode: true })
            this.card.mount('#card-element')
        }
    },
    methods: {
        getItemTotal(item) {
            return item.quantity * item.product.price
        },
        async submitForm() {
            this.errors = []
            if (this.firstname === '')
                this.errors.push('The first name is missing.')
            if (this.lastname === '')
                this.errors.push('The last name is missing.')
            if (this.email === '')
                this.errors.push('The email is missing.')
            if (this.phone === '')
                this.errors.push('The phone number is missing.')
            if (this.address === '')
                this.errors.push('The address is missing.')
            if (this.zipcode === '')
                this.errors.push('The zip code is missing.')
            if (this.city === '')
                this.errors.push('The city is missing.')

            if (!this.errors.length) {
                this.$store.commit('setIsLoading', true)

                this.stripe.createToken(this.card)
                    .then(result => {
                        if (result.error) {
                            this.$store.commit('setIsLoading', false)
                            this.errors.push('Something went wrong with Stripe. Please try again.')
                            console.log(result.error.message)
                        } else {
                            this.stripeTokenHandler(result.token)
                        }
                    })
            }
        },
        async stripeTokenHandler(token) {
            const items = []

            for (const item of this.cart.items) {
                const obj = {
                    product: item.product.id,
                    quantity: item.quantity,
                    price: item.product.price * item.quantity
                }
                items.push(obj)
            }

            const data = {
                'firstname': this.firstname,
                'lastname': this.lastname,
                'email': this.email,
                'address': this.address,
                'zipcode': this.zipcode,
                'city': this.city,
                'phone': this.phone,
                'items': items,
                'stripe_token': token.id,
            }

            await axios
                .post('api/v1/checkout/', data)
                .then(response => {
                    this.$store.commit('clearCart')
                    this.$router.push('/cart/success')
                })
                .catch(error => {
                    this.errors.push('Something went wrong. Please try again.')
                    console.log(error)
                })

            this.$store.commit('setIsLoading', false)
        }
    },
    computed: {
        cartTotalLength() {
            return this.cart.items.reduce((tot, val) => {
                return tot += val.quantity
            }, 0)
        },
        cartTotalPrice() {
            return this.cart.items.reduce((tot, val) => {
                return tot += val.product.price * val.quantity
            }, 0)
        },
    }
}
</script>