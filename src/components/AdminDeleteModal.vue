<template>
<div id="delProductModal" ref="delModal">
  <div class="modal-dialog">
    <div class="modal-content border-0">
      <div class="modal-header bg-danger text-white">
        <h5 id="delProductModalLabel" class="modal-title">
          <span>刪除產品</span>
        </h5>
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="modal"
          aria-label="Close"
        ></button>
      </div>
      <div class="modal-body">
        是否刪除
        <!-- {{product}} -->
        <strong class="text-danger">{{ product.title }}</strong>
        商品(刪除後將無法恢復)。
      </div>
      <div class="modal-footer">
        <button
          type="button"
          class="btn btn-outline-secondary"
          data-bs-dismiss="modal"
        >
          取消
        </button>
        <button type="button" class="btn btn-danger" @click="deleteProduct()">
          確認刪除
        </button>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import Modal from 'bootstrap/js/dist/modal'
export default {
  props: ['tempProduct'],
  watch: {
    tempProduct () {
      this.product = JSON.parse(JSON.stringify(this.tempProduct))
      console.log(this.product)
    }
  },
  data () {
    return {
      product: {
        // imagesUrl: [],
        // imgPreviewURL: ''
      },
      modal: {}
    }
  },
  methods: {
    deleteProduct () {
      const url = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/product/${this.tempProduct.id}`
      console.log(url)
      this.$http
        .delete(url)
        .then((result) => {
          console.log(result)
          this.getProducts()
          this.closeModal()
        })
        .catch((err) => { console.log(err) })
    },
    openModal () {
      this.modal.show()
    },
    closeModal () {
      this.modal.hide()
    }
  },
  mounted () {
    this.modal = new Modal(this.$refs.delModal)
  }
}
</script>
