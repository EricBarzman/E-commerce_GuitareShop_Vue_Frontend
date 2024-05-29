<template>
    <div class="page-search">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">Search</h1>
                <h2 class="is-size-5 has-text-grey">{{ products.length }} result{{ products.length === 1 ? '' : 's' }} for: {{ query }}</h2>
            </div>
        </div>
        <section class="is-flex is-flex-wrap-wrap">
            <ProductBox v-for="product in products" :key="product.id" :product="product"/>
        </section>
    </div>
</template>

<script>
import axios from 'axios'
import ProductBox from '@/components/ProductBox.vue'

export default {
    name: 'Search',
    data() {
        return {
            products: [],
            query: ''
        }
    },
    components: {
        ProductBox
    },
    mounted() {
        document.title = 'Search | GuitareShop'
        let uri = window.location.search.substring(1)
        let params = new URLSearchParams(uri)

        if (params.get('query')) {
            this.query = params.get('query')
            this.performSearch()
        }
    },
    methods: {
        async performSearch() {
            this.$store.commit('setIsLoading', true)
            
            await axios
                    .post('api/v1/products/search/', { 'query': this.query})
                    .then(resp => this.products = resp.data)
                    .catch(error => console.log(error))
            
            this.$store.commit('setIsLoading', false)
        }
    }
}
</script>