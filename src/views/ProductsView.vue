<template>
  <h1>產品列表</h1>
  <div class="container">
    <!-- 產品列表 -->
    <div class="mt-4">
      <table class="table align-middle">
        <thead>
          <tr>
            <th>圖片</th>
            <th>商品名稱</th>
            <th>價格</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td style="width: 200px">
              <div
                :style="{ backgroundImage: `url(${product.imageUrl})` }"
                style="
                  height: 100px;
                  background-size: cover;
                  background-position: center center;
                "
              ></div>
            </td>
            <td>{{ product.title }}</td>
            <td>
              <div v-if="product.origin_price == product.price" class="h5">
                {{ product.price }} 元
              </div>
              <div v-else>
                <del class="h6">原價 {{ product.origin_price }} 元</del>
                <div class="h5">特價 {{ product.price }} 元</div>
              </div>
            </td>
            <td>
              <div class="btn-group btn-group-sm">
                <router-link       class="btn btn-outline-secondary"
                  :disabled="isLoadingItem == product.id" :to="`/product/${product.id}`">查看更多</router-link>
                <button
                  type="button"
                  class="btn btn-danger"
                  @click="addToCart(product.id)"
                  :disabled="isLoadingItem == product.id"
                >
                  <span
                    class="spinner-border spinner-border-sm"
                    v-show="isLoadingItem == product.id"
                  ></span>
                  加到購物車
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      products: [],
      productId: '',
      isLoadingItem: ''
    }
  },
  methods: {
    getProducts () {
      this.$http.get(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/products/all`)
        .then((res) => {
          this.products = res.data.products
        })
        .catch((error) => {
          console.dir(error)
        })
    },
    addToCart (id, qty = 1) {
      const postData = {
        data: {
          product_id: id,
          qty
        }
      }

      this.isLoadingItem = id

      this.$http.post(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`, postData)
        .then((res) => {
          this.isLoadingItem = ''
        })
        .catch((error) => {
          console.dir(error)
        })
    }
  },
  mounted () {
    this.getProducts()
  }
}
</script>
