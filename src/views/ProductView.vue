<template>
<h2>單一產品</h2>
  <div class="container">
    <div class="card mb-3">
    <div class="row g-0">
      <div class="col-md-4">
        <img class="img-fluid" :src="product.imageUrl" alt="">
      </div>
      <div class="col-md-8">
        <div class="card-body">
          <h5 class="card-title">{{product.title}}</h5>
          <p class="card-text">{{product.description}}</p>
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
      product: {}
    }
  },
  methods: {
    getProduct () {
      // console.log(this.$route)
      // console.log(this.$route.params.id)
      const { id } = this.$route.params
      // console.log(id)
      // this 調用 main.js 中 axios 的方法
      this.$http(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${id}`)
        .then((result) => {
          // console.log(result.data.products)
          this.product = result.data.product
        })
        .catch((err) => {
          console.log(err)
        })
    }
  },
  mounted () {
    this.getProduct()
  }
}
</script>
