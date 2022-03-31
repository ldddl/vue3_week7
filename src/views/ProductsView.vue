<template>
  <!-- <h2>產品列表</h2> -->
  <div class="container">
    <div class="row row-cols-1 row-cols-lg-3 g-3">
      <div class="col" v-for="item in products" :key="item.id">
        <div class="card-group">
        <div class="card">
          <img :src="item.imageUrl"
          class="card-img-top img-fluid img-thumbnail"
          alt="...">
          <div class="card-body">
            <h5 class="card-title">{{ item.title }}</h5>
            <p class="card-text">{{ item.description }}</p>
            <router-link :to="`/product/${item.id}`" class="btn btn-primary">查看更多</router-link>
          </div>
        </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      products: []
    }
  },
  methods: {
    getProducts () {
      // this 調用 main.js 中 axios的方法
      this.$http(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products/all`)
        .then((result) => {
          // console.log(result.data.products)
          this.products = result.data.products
        })
        .catch((err) => {
          console.log(err)
        })
    }
  },
  mounted () {
    this.getProducts()
  }
}
</script>
