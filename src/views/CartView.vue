<template>
  <h2>購物車</h2>
  <div class="container">

  <table class="table align-middle">
    <thead>
      <tr>
        <th>圖片</th>
        <th>商品名稱</th>
        <th>價格</th>
        <th>
          <button class="btn btn-outline-secondary" type="button"
            @click="clearCart()"
            :disabled="!cartData.carts?.length > 0">
            <!-- {{cartData}} -->
            <!-- {{cartData.carts?.length}} -->
            清空購物車
            </button>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="product in products" :key="product.id">
        <td style="width: 200px">
          <!-- :style 的值為物件格式 background-image 要轉成大寫 backgroundImage -->
          <div
            :style="{ backgroundImage: `url(${product.imageUrl})` }"
            style="
              height: 100px;
              background-size: cover;
              background-position: center;
            "
          ></div>
        </td>
        <td>{{ product.title }}</td>
        <td>
          <!-- 用資料驅動畫面
                    當product.price === product.origin_price時
                    只顯示product.price
                  -->
          <div class="h5" v-if="product.price === product.origin_price">
            {{ product.price }} 元
          </div>
          <div v-else>
            <del class="h6">原價 {{ product.origin_price }} 元</del>
            <div class="h5">現在只要 {{ product.price }} 元</div>
          </div>
        </td>
        <td>
          <div class="btn-group btn-group-sm">
            <!-- 滑鼠事件帶上id -->
            <!-- :disabled="isLoadingItem === product.id"
                      按鈕點擊後，觸發disabled，直到ajax結束後解除disabled
                      可避免用戶連點後，造成伺服器負擔
                    -->
            <button
              type="button"
              class="btn btn-outline-secondary"
              @click="openProductModal(product.id)"
              :disabled="isLoadingItem === product.id"
            >
              <i
                class="fas fa-spinner fa-pulse"
                v-show="isLoadingItem === product.id"
              ></i>
              查看更多
            </button>
            <button
              type="button"
              class="btn btn-outline-danger"
              @click="addToCart(product.id)"
              :disabled="isLoadingItem === product.id"
            >
              <i
                class="fas fa-spinner fa-pulse"
                v-show="isLoadingItem === product.id"
              ></i>
              加到購物車
            </button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  </div>
</template>

<script>
import emitter from '@/libs/emitter'
export default {
  data () {
    return {
      cartData: {},
      products: [],
      productId: '',
      isLoadingItem: '' // 局部讀取效果:點擊該品項後，幾秒內按鈕無法再次被點擊
    }
  },
  methods: {
    getProduct () {
      this.$http.get(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products/all`)
        .then((result) => {
          console.log('getProduct', result)
          this.products = result.data.products
        })
        .catch((err) => {
          console.log(err)
        })
    },
    openProductModal (id) {
      // id，為產品id
      // 利用 refs 取得 product-modal元件，並操作該元件中的方法
      this.productId = id
      this.$refs.productModal.openModal()
    },
    getCart () {
      this.$http
        .get(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`)
        .then((result) => {
          console.log('getCart', result)
          this.cartData = result.data.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    addToCart (id, qty = 1) {
      // qyt預設值為1
      console.log('addToCart')
      // 加入購物車，依照API文件，必須帶入兩參數
      const data = {
        product_id: id,
        qty // 同名可縮寫
      }
      this.isLoadingItem = id
      this.$http
        .post(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`, { data })
        .then((result) => {
          console.log(result)
          this.getCart()
          // this.$refs.productModal.closeModal()
          this.isLoadingItem = ''
          // console.log('addToCart - emitter')
          emitter.emit('get-cart')
        })
        .catch((err) => {
          console.log(err)
        })
    },
    removeCartItem (id) {
      // 刪除品項
      this.isLoadingItem = id
      this.$http
        .delete(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${id}`)
        .then((result) => {
          console.log(result)
          this.getCart()
          this.isLoadingItem = ''
        })
        .catch((err) => {
          console.log(err)
        })
    },
    updateCartItem (item) {
      console.log('updateCartItem')
      // 更新品項，依照API文件，必須帶入兩參數
      const data = {
        product_id: item.id,
        qty: item.qty
      }
      this.isLoadingItem = item.id
      // console.log(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`,{ data })
      this.$http
        .put(`${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${item.id}`, { data })
        .then((result) => {
          console.log(result)
          this.getCart()
          this.isLoadingItem = ''
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
          emitter.emit('get-cart')
          this.getCart()
        })
        .catch((err) => {
          console.log(err)
        })
    },
    pushForm () {
      this.clearCart()
      this.$refs.form.resetForm()
      alert('送出表單')
    }
  },
  mounted () {
    this.getProduct()
    this.getCart()
  }
}
</script>
