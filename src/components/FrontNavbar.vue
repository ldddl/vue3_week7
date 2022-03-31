<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
      <router-link class="navbar-brand" to="/">哇屋</router-link>
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarNav"
        aria-controls="navbarNav"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
          <!-- <li class="nav-item">
            <router-link class="nav-link" to="/">首頁</router-link>
          </li> -->
          <li class="nav-item">
            <router-link class="nav-link" to="/products">產品列表</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/cart">購物車</router-link>
          </li>
        </ul>
      </div>
      <div>
        <button class="btn" type="button">
          <router-link class="nav-link" to="/login">後台</router-link>
        </button>
      <button class="btn btn-primary ms-3" type="button">
        結帳
        <span class="badge rounded-pill bg-danger">{{cartData.carts?.length}}</span>
      </button>
      </div>
    </div>
  </nav>
</template>

<script>
import emitter from '@/libs/emitter'
export default {
  data () {
    return {
      cartData: {}
    }
  },
  methods: {
    getCart () {
      this.$http
        .get(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`)
        .then((result) => {
          console.log('cartData', result)
          this.cartData = result.data.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    clearCart () {
      // 清除購物車
      console.log('clearCart()')
      this.$http
        .delete(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/carts`)
        .then((result) => {
          console.log(result)
          this.getCart()
        })
        .catch((err) => {
          console.log(err)
        })
    },
    toDashboardView () {

    }
  },
  mounted () {
    this.getCart()
    emitter.on('get-cart', () => {
      this.getCart()
    })
  }
}
</script>
