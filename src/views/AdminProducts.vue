<template>
  <div class="container">
    <div class="text-end mt-4">
      <button
        class="btn btn-primary"
        @click="openModal('isAdd')"
        :is-new="isNew"
      >
        建立新的產品
      </button>
    </div>
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="120">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item,i) in products" :key="i">
          <td>{{ item.category }}</td>
          <td>{{ item.title }}</td>
          <td class="text-end">{{ item.origin_price }}</td>
          <td class="text-end">{{ item.price }}</td>
          <td>
            <span class="text-success" v-if="item.is_enabled">啟用</span>
            <span v-else>未啟用</span>
          </td>
          <td>
            <div class="btn-group">
              <button
                type="button"
                class="btn btn-outline-primary btn-sm"
                @click="openModal('isEdit', item)"
              >
                編輯
              </button>
              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="openModal('isDelete', item)"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- 將元件加入畫面  -->
    <!-- v-bind 綁定 內層元件pages，值為 外層元件pagination -->
    <!-- 觸發事件v-on 綁定 內層get-product，值為 外層getProducts -->
    <pagination :pages="pagination" @get-product="getProducts"></pagination>
  </div>

    <!-- v-bind綁定 內層元件temp-product 及 外層元件tempProduct  -->
    <!-- v-on綁定 內層元件get-products 及 外層元件getProducts  -->
  <product-modal
    :temp-product="tempProduct"
    @get-products="getProducts"
    :is-new="isNew"
    ref="productModal"
  ></product-modal>

  <delProductModal
    ref="delProductModal"
    :temp-product="tempProduct"
    class="modal fade"
    tabindex="-1"
    aria-labelledby="delProductModalLabel"
    aria-hidden="true"
  >
  </delProductModal>
</template>

<script>
// import Modal from 'bootstrap/js/dist/modal'
import ProductModal from '@/components/ProductModal'
import pagination from '@/components/AdminPagination.vue'
import delProductModal from '@/components/AdminDeleteModal.vue'

export default {
  components: { ProductModal, pagination, delProductModal },
  data () {
    return {
      products: [],
      tempProduct: {
        imagesUrl: [] // 利用陣列存放多圖
      },
      isNew: false, // isNew，從 openModal() 改變 isNew 的值來判斷 updateProduct)() 使用編輯或新增
      pagination: {} // 定義一個新的pagination物件，從API取得分頁用的pagination後，賦予到data的pagination
    }
  },
  methods: {
    checkLogin () {
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*\=\s*([^;]*).*$)|^.*$/,'$1') // eslint-disable-line
      this.$http.defaults.headers.common['Authorization'] = token // eslint-disable-line
      this.$http
        .post(`${process.env.VUE_APP_API}/api/user/check`)
        .then((result) => {
          // console.log(result);
          console.log('check_OK')
          this.getProducts()
        })
        .catch((err) => {
          console.dir(err)
          alert('登入驗證失敗')
          window.location.href = 'index.html'
        })
    },
    getProducts (page = 1) {
    // 參數預設值 page = 1 ，用於分頁功能未取值時，顯示第一頁面
      console.log(page)
      this.$http
        .get(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/products/?page=${page}`)
        .then((result) => {
          // console.log(result);
          console.log(result.data.products)
          console.log(this.pagination)
          this.products = result.data.products
          this.pagination = result.data.pagination // 取得pagination
        })
        .catch((err) => {
          console.log('getProducts')
          console.log(err)
        })
    },
    openModal (status, product) {
      // 以status區分新增及編輯按鈕
      if (status === 'isAdd') {
        // console.log(this.$refs)
        // 新增
        this.tempProduct = {
          imagesUrl: [] // 利用陣列存放多圖
        }
        this.$refs.productModal.openModal()
        // modal.show()
        this.isNew = true
      } else if (status === 'isEdit') {
        // 編輯
        console.log(product)
        // this.tempProduct = product;
        this.tempProduct = JSON.parse(JSON.stringify(product))
        // modal.show()
        this.$refs.productModal.openModal()
        this.isNew = false
      } else if (status === 'isDelete') {
        this.tempProduct = JSON.parse(JSON.stringify(product))
        console.log(this.tempProduct)
        // delProductModal.show()
        // this.$refs.delProductModal.show()
        this.$refs.delProductModal.openModal()
      }
    }
  },
  mounted () {
    this.checkLogin()
    // delProductModal = new Modal(
    //   document.querySelector('#delProductModal')
    // )
  }
}
</script>
