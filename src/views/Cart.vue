<template>
    <div class="cart-page">
        <div class="column multiline">
            
            <div class="column is-12">
                <h1 class="title">Cart</h1>
            </div>

            <div class="column is-12 box">
                <table class="table is-fullwidth" v-if="cartTotalLength">
                    <thead>
                        <tr>
                            <th>Product</th>
                            <th>Price</th>
                            <th>Quantity</th>
                            <th>Total</th>
                            <th></th>
                        </tr>
                    </thead>

                    <tbody>
                        <CartItem
                            v-for="item in cart.items"
                            :key="item.product.id"
                            :initialItem="item"
                            v-on:removeFromCart="removeFromCart"
                        />
                    </tbody>
                </table>

                <p class="p-2" v-else>You have nothing in your cart yet, dude...</p>
            </div>

            <div class="column is-12 box">
                <h2 class="subtitle">Summary</h2>
                {{ cartTotalLength }} items, <strong>{{ cartTotalPrice.toFixed(2) }}€</strong>
                <hr>
                <router-link to="/cart/checkout" class="button is-dark">Proceed to checkout</router-link>
            </div>

        </div>
    </div>
</template>

<script>
import axios from 'axios'
import CartItem from '@/components/CartItem.vue';

export default {
    name: 'Cart',
    components : {
        CartItem
    },
    data() {
        return {
            cart: {
                items: []
            }
        }
    },
    mounted() {
        this.cart = this.$store.state.cart
        document.title = 'Cart | GuitareShop'
    },
    methods : {
        removeFromCart (item) {
            this.cart.items = this.cart.items.filter(i => i.product.id !== item.product.id)
        },
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