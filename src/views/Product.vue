<template>
    <div class="page-product">
        <div class="is-flex-desktop">
            <div class="column is-7">
                <figure class="image">
                    <img style="width: 85%" :src="product.get_image" :alt="product.name">
                </figure>
            </div>

            <div class="column is-3">
                <h1 class="title mt-6">{{ product.name }}</h1>

                <p>{{ product.description }}</p>
                <h2 class="subtitle mt-6">Information</h2>
                <p><strong>Price: </strong>{{ product.price }}€</p>
                
                <div class="field has-addons mt-6">
                    <div class="control">
                        <input type="number" class="input" min="1" v-model="quantity">
                    </div>

                    <div class="control">
                        <a class="button is-dark" @click="addToCart">Add to cart</a>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'Product',
    data() {
        return {
            product: {},
            quantity: 1
        }
    },
    mounted() {
        this.getProduct()
    },
    methods: {
        async getProduct() {
            this.$store.commit('setIsLoading', true)
            const category_slug = this.$route.params.category_slug
            const product_slug = this.$route.params.product_slug

            await axios
                .get(`api/v1/products/${category_slug}/${product_slug}`)
                .then(response => {
                    this.product = response.data
                    document.title = this.product.name + ' | GuitareShop'
                })
                .catch(error => console.log(error))
            this.$store.commit('setIsLoading', false)
        },

        addToCart() {
            if (isNaN(this.quantity) || this.quantity < 1)
                this.quantity = 1
            
            const item = {
                product: this.product,
                quantity: this.quantity
            }
            this.$store.commit('addToCart', item)

            toast({
                message: 'The product was added to the cart',
                type: 'is-success',
                dismissible: true,
                pauseOnHover: true,
                duration: 2000,
                position: 'bottom-right',
                animate: { in: 'fadeIn', out: 'fadeOut' }
            })
        }
    }
}
</script>