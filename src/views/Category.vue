<template>
    <div class="page-category">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h2 class="is-size-2 has-text-centered">{{ category.name }}</h2>
            </div>
        </div>

        <section class="is-flex is-flex-wrap-wrap">
            <ProductBox
                v-for="product in category.products"
                :key="product.id"
                :product="product"
            />
      </section>
    </div>
</template>

<script>
import ProductBox from '@/components/ProductBox.vue';
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'Category',
    data() {
        return {
            category: {
                products: []
            }
        }
    },
    mounted() {
        this.getCategory()
    },
    watch: {
        $route(to, from) {
            if (to.name === 'Category') {
                this.getCategory()
            }
        }
    },
    components: {
        ProductBox
    },
    methods: {
        async getCategory() {
            const categorySlug = this.$route.params.category_slug
            this.$store.commit('setIsLoading', true)
            await axios
                .get(`api/v1/products/${categorySlug}`)
                .then(response => {
                    this.category = response.data
                    document.title = this.category.name + ' | GuitareShop'
                })
                .catch(error => {
                    console.log(error)
                    toast({
                        message: 'Something went wrong. Please try again.',
                        type: 'is-danger',
                        dismissible: true,
                        pauseOnHover: true,
                        duration: 2000,
                        position: 'bottom-right',
                        animate: { in: 'fadeIn', out: 'fadeOut' }
                    })
                })
            this.$store.commit('setIsLoading', false)
        }
    }
}
</script>