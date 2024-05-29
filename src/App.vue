<template>
  <div id="wrapper">

    <!-- Nav Bar -->
    <nav class="navbar is-dark">
      
      <!-- Site name -->
      <div class="navbar-brand">
        <router-link to="/" class="navbar-item"><strong>GuitareShop</strong></router-link>
        <a @click="showMobileMenu = !showMobileMenu"
          class="navbar-burger"
          aria-label="menu"
          aria-expanded="false"
          data-target="navbar-menu"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>

      <div id="navbar-menu" class="navbar-menu" :class="{'is-active': showMobileMenu}">
        
        <!-- Search bar -->
        <div class="navbar-start">
          <div class="navbar-item">
            <form action="/search" method="get">
              <div class="field has-addons">
              
                <div class="control">
                  <input type="text" class="input" placeholder="Search for..." name="query">
                </div>

                <div class="control">
                  <button class="button is-warning">
                    <span class="icon"><i class="fas fa-search"></i></span>
                  </button>
                </div>

              </div>
            </form>
          </div>
        </div>

        <div class="navbar-end">
          
          <!-- Categories -->
          <router-link to="/electric" class="navbar-item">Guitares</router-link>
          <router-link to="/hollowbody" class="navbar-item">Hollowbody</router-link>
          <router-link to="/bass" class="navbar-item">Basses</router-link>

          <div class="navbar-item">
            <div class="buttons">
              
              <!-- Account -->
              <template v-if="this.$store.state.isAuthenticated">
                <router-link to="/my-account" class="button is-light">My account</router-link>
              </template>
              <!-- Login -->
              <template v-else>
                <router-link to="/login" class="button is-white">Log in</router-link>
              </template>

              <!-- Sign up -->
              <router-link v-if="!this.$store.state.isAuthenticated" to="/signup" class="button is-light">Sign up</router-link>
              
              <router-link to="/cart" class="button is-warning">
                <span class="icon"><i class="fas fa-shopping-cart"></i></span>
                <span>Cart ({{ cartTotalLength }})</span>
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <!-- Loading cycle -->
    <div class="is-loading-bar has-text-centered" :class="{'is-loading' : $store.state.isLoading }">
      <div class="lds-dual-ring"></div>
    </div>
    
    <!-- Main body -->
    <section class="section">
      <router-view/>
    </section>

    <!-- Footer -->
    <footer class="footer is-flex is-flex-direction-column">
      <p class="has-text-centered mb-3">Copyright Eric 2024</p>
      <div class="has-text-centered">
          <router-link to="/about" class="has-text-centered">About</router-link>
      </div>
    </footer>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      showMobileMenu: false,
      cart: {
        items: []
      }
    }
  },
  beforeCreate() {
    this.$store.commit('initializeStore')
    const token = this.$store.state.token
    if (token) {
      axios.defaults.headers.common['Authorization'] = "Token " + token
    } else {
      axios.defaults.headers.common['Authorization'] = ""
    }
  },
  mounted() {
    this.cart = this.$store.state.cart
  },
  computed: {
    cartTotalLength() {
      let totalLength = 0;
      for (let i = 0; i < this.cart.items.length; i++) {
        totalLength += this.cart.items[i].quantity
      }
      return totalLength
    }
  },
}
</script>

<style lang="scss">
@import '../node_modules/bulma';

.lds-dual-ring {
  display: inline-block;
  width: 80px;
  height: 80px;
}
.lds-dual-ring:after {
  content: " ";
  display: block;
  width: 64px;
  height: 64px;
  margin: 8px;
  border-radius: 50%;
  border: 6px solid #ccc;
  border-color: #ccc transparent #ccc transparent;
  animation: lds-dual-ring 1.2s linear infinite;
}
@keyframes lds-dual-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.is-loading-bar {
  height: 0;
  overflow: hidden;

  -webkit-transition: all 0.3s;
  transition: all 0.3s;

  &.is-loading {
    height: 80px;
  }
}
</style>
