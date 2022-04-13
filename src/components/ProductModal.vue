<template>
 <div id="productModal" ref="modal" class="modal fade" tabindex="-1" aria-labelledby="productModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-xl">
    <div class="modal-content border-0">
      <div class="modal-header bg-dark text-white">
        <h5 id="productModalLabel" class="modal-title">
          <span>新增產品</span>
        </h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <div class="row">
          <div class="col-sm-4">
            <div class="mb-2">
              <div class="mb-3">
                <label for="imageUrl" class="form-label">輸入圖片網址</label>
                <input type="text" class="form-control img-fluid" v-model="product.imageUrl"
                       placeholder="請輸入圖片連結" id="imageUrl">
              </div>
              <img class="img-fluid" :src="product.imageUrl" alt="">
            </div>
            <div class="md-3">
              <h3>多圖設置</h3>
              <div v-if="Array.isArray(product.imagesUrl)">
                <template v-for="(item,key) in product.imagesUrl" :key="key + '66666'">
                  <label for="imageUrl" class="form-label">輸入圖片網址</label>
                  <input type="text" class="form-control img-fluid" v-model="product.imagesUrl[key]"
                       placeholder="請輸入圖片連結" id="imageUrl">
                  <img class="img-fluid" :src="product.imagesUrl[key]" alt="">
                </template>
              </div>
            </div>
            <div v-if="Array.isArray(product.imagesUrl)">
              <button class="btn btn-outline-primary btn-sm d-block w-100" v-if="!product.imagesUrl.length||product.imagesUrl[product.imagesUrl.length-1]" @click="product.imagesUrl.push('')">
                新增圖片
              </button>
              <button class="btn btn-outline-danger btn-sm d-block w-100" v-else @click="product.imagesUrl.pop()">
                刪除圖片
              </button>
            </div>
            <div v-else>
              <label for="imageUrl" class="form-label">輸入圖片網址</label>
                  <input type="text"
                  class="form-control img-fluid"
                  v-model="product.imagesUrl[key]"
                  placeholder="請輸入圖片連結" id="imageUrl">
                  <img class="img-fluid" :src="product.imagesUrl[key]" alt="">
              <button class="btn btn-outline-primary btn-sm d-block w-100" @click="product.imagesUrl.push('')">
                新增圖片2
              </button>
              <button class="btn btn-outline-danger btn-sm d-block w-100" @click="product.imagesUrl.pop()">
                刪除圖片
              </button>
            </div>
          </div>
          <div class="col-sm-8">
            <div class="mb-3">
              <label for="title" class="form-label">標題</label>
              <input id="title" type="text" class="form-control" placeholder="請輸入標題" v-model="product.title">
            </div>

            <div class="row">
              <div class="mb-3 col-md-6">
                <label for="category" class="form-label">分類</label>
                <input id="category" type="text" class="form-control" v-model="product.category"
                       placeholder="請輸入分類">
              </div>
              <div class="mb-3 col-md-6">
                <label for="price" class="form-label">單位</label>
                <input id="unit" type="text" class="form-control" placeholder="請輸入單位" v-model="product.unit">
              </div>
            </div>

            <div class="row">
              <div class="mb-3 col-md-6">
                <label for="origin_price" class="form-label">原價</label>
                <input id="origin_price" type="number" min="0" class="form-control" placeholder="請輸入原價" v-model.number="product.origin_price">
              </div>
              <div class="mb-3 col-md-6">
                <label for="price" class="form-label">售價</label>
                <input id="price" type="number" min="0" class="form-control"
                       placeholder="請輸入售價" v-model.number="product.price">
              </div>
            </div>
            <hr>

            <div class="mb-3">
              <label for="description" class="form-label">產品描述</label>
              <textarea id="description" type="text" class="form-control"
                        placeholder="請輸入產品描述" v-model="product.description">
              </textarea>
            </div>
            <div class="mb-3">
              <label for="content" class="form-label">說明內容</label>
              <textarea id="description" type="text" class="form-control"
                        placeholder="請輸入說明內容" v-model="product.content">
              </textarea>
            </div>
            <div class="mb-3">
              <div class="form-check">
                <input id="is_enabled" class="form-check-input" type="checkbox"
                       :true-value="1" :false-value="0" v-model="product.is_enabled">
                <label class="form-check-label" for="is_enabled">是否啟用</label>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">
          取消
        </button>
        <button type="button" class="btn btn-primary" @click="updateProducts()">
          確認
        </button>
      </div>
    </div>
  </div>
 </div>
</template>

<script>
import Modal from 'bootstrap/js/dist/modal'
export default {
  data () {
    return {
      product: {
        imagesUrl: [],
        imgPreviewURL: ''
      },
      modal: {}
    }
  },
  props: ['tempProduct', 'isNew'],
  watch: {
    tempProduct () {
      this.product = JSON.parse(JSON.stringify(this.tempProduct))
    }
  },
  methods: {
    updateProducts () {
      let url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`
      let method = 'post'
      console.log(this.product)
      console.log(this.isNew)
      if (!this.isNew) {
        // 編輯
        console.log(this.product.id)
        url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${this.product.id}`
        method = 'put'
      } else {
        // 新增
        console.log(this.product)
        if (this.product.imagesUrl.length === 0) {
          alert('請新增更多圖片')
          return 0
        }
      }
      // 需根據API文件格式傳遞資料，{ data: this.tempProduct }
      this.$http[method](url, { data: this.product })
        .then((result) => {
          console.log(result)
          // this.getProducts(); // getProducts為外層方法，無法直接觸發，透過$emit()傳遞資料
          this.$emit('get-products')
          this.modal.hide()
        })
        .catch((err) => {
          let errMessage = ''
          err.data.message.forEach((item, i) => {
            errMessage += item
          })
          alert(errMessage)
        })
    },
    openModal () {
      this.modal.show()
    }
  },
  mounted () {
    // console.log(this.$refs.modal)
    this.modal = new Modal(this.$refs.modal)
    // console.log(this.modal)
    // this.openModal()
    // console.log(this.product)
    // console.log(this.tempProduct)
  }
}
</script>
