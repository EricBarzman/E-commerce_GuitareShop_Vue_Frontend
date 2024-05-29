<template>
  <div class="home">
    <section>
      <div class="hero-body has-text-centered">
        <p class="title mb-6">
            Welcome to GuitareShop!
        </p>
        <p class="subtitle">
            Supplying your needs in music!
        </p>
      </div>
    </section>

    <div class="column is-multiline">
      <div class="column is-12">
        <h2 class="is-size-2 has-text-centered">Latest Products</h2>
      </div>

      <section class="is-flex is-flex-wrap-wrap">
        <ProductBox
          v-for="product in latestProducts"
          :key="product.id"
          :product="product"
        />
      </section>
    </div>
  </div>
</template>

<script>
import axios from "axios"
import ProductBox from "@/components/ProductBox.vue";

export default {
  name: 'Home',
  data() {
    return {
      latestProducts: []
    }
  },
  components: {
    ProductBox
  },
  mounted() {
    this.getLatestProducts()
    document.title = 'Home | GuitareShop'
  },
  methods: {
    async getLatestProducts() {
      this.$store.commit('setIsLoading', true)
      await axios
        .get('/api/v1/latest-products/')
        .then(response => {
          this.latestProducts = response.data
        })
        .catch(error => console.log(error))
      this.$store.commit('setIsLoading', false)
    }
  }
}
</script>