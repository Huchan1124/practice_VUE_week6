<template>
  <h1>單一產品列表</h1>
  <div class="container">
    <!-- 單一產品列表 -->
    <div class="mt-4">
      <div
        id="productModal"
        tabindex="-1"
        role="dialog"
        aria-labelledby="exampleModalLabel"
        aria-hidden="true"
        ref="modal"
      >
        <div class="modal-dialog modal-xl" role="document">
          <div class="modal-content border-0">
            <div class="modal-header bg-dark text-white">
              <h5 class="modal-title" id="exampleModalLabel">
                <span>{{ product.title }}</span>
              </h5>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="modal"
                aria-label="Close"
              ></button>
            </div>
            <div class="modal-body">
              <div class="row">
                <div class="col-sm-6">
                  <img
                    class="img-fluid"
                    :src="product.imageUrl"
                    alt="product.content"
                  />
                </div>
                <div class="col-sm-6">
                  <span class="badge bg-primary rounded-pill"> </span>
                  <p>商品描述：{{ product.description }}</p>
                  <p>商品內容：{{ product.content }}</p>
                  <div v-if="product.price == product.origin_price" class="h5">
                    {{ product.price }}元
                  </div>
                  <div v-else>
                    <del class="h6">原價{{ product.origin_price }} 元</del>
                    <div class="h5">特價{{ product.price }} 元</div>
                  </div>
                  <div>
                    <div class="input-group">
                      <input
                        type="number"
                        class="form-control"
                        min="1"
                        v-model="qty"
                        @change="updateCartItem(product.id)"
                      />
                      <button
                        type="button"
                        class="btn btn-danger"
                        @click="addToCart(product.id)"
                      >
                        加入購物車
                      </button>
                    </div>
                  </div>
                </div>
              </div>
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
      product: [],
      productId: '',
      isLoadingItem: '',
      qty: 1
    }
  },
  methods: {
    getProduct () {
      const { id } = this.$route.params
      this.$http
        .get(
          `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/product/${id}`
        )
        .then((res) => {
          this.product = res.data.product
        })
        .catch((error) => {
          console.dir(error)
        })
    },
    addToCart (id, qty = 1) {
      const postData = {
        data: {
          product_id: id,
          qty: this.qty
        }
      }

      this.isLoadingItem = id

      this.$http
        .post(
          `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`,
          postData
        )
        .then((res) => {
          this.isLoadingItem = ''
          this.$router.push('/cart')
        })
        .catch((error) => {
          console.dir(error)
        })
    },
    updateCartItem (id) {
      const postData = {
        data: {
          product_id: id,
          qty: this.qty
        }
      }

      this.isLoadingItem = id

      this.$http.put(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart/${id}`, postData)
        .then((res) => {
          this.isLoadingItem = ''
        })
        .catch((error) => {
          console.dir(error)
        })
    }
  },
  mounted () {
    this.getProduct()
  }
}
</script>
