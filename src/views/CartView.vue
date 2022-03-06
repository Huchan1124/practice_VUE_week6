<template>
  <h1>購物車頁面</h1>
  <div class="container">
    <div class="mt-4">
      <div class="text-end">
        <button
          class="btn btn-outline-danger"
          type="button"
          @click="removeAllCart"
          :disabled="cartData?.carts?.length == 0"
        >
          清空購物車
        </button>
      </div>
      <!-- 購物車列表 -->
      <table class="table align-middle">
        <thead>
          <tr>
            <th></th>
            <th>品名</th>
            <th style="width: 150px">數量/單位</th>
            <th class="text-end">價格</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="cartData?.carts">
            <tr v-for="cartItem in cartData?.carts" :key="cartItem.id">
              <td>
                <button
                  type="button"
                  class="btn btn-outline-danger btn-sm"
                  @click="removeCartItem(cartItem.id)"
                >
                  x
                </button>
              </td>
              <td>
                {{ cartItem.product.title }}
              </td>
              <td>
                <div class="input-group input-group-sm">
                  <div class="input-group mb-3">
                    <select
                      id=""
                      class="form-select"
                      v-model="cartItem.qty"
                      :disabled="isLoadingItem == cartItem.id"
                      @change="updateCartItem(cartItem)"
                    >
                      <option
                        :value="num"
                        v-for="num in 10"
                        :key="`${num}${cartItem.id}`"
                      >
                        {{ num }}
                      </option>
                    </select>
                    <span class="input-group-text" id="basic-addon2">{{
                      cartItem.product.unit
                    }}</span>
                  </div>
                </div>
              </td>
              <td class="text-end">
                {{ cartItem.qty * cartItem.product.price }}
              </td>
            </tr>
          </template>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="3" class="text-end">總計</td>
            <td class="text-end">{{ cartData?.final_total }}</td>
          </tr>
        </tfoot>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      cartData: {},
      productId: '',
      isLoadingItem: ''
    }
  },
  methods: {
    getCart () {
      this.$http.get(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`)
        .then((res) => {
          this.cartData = res.data.data
        })
        .catch((error) => {
          console.dir(error)
        })
    },
    removeCartItem (id) {
      this.isLoadingItem = id
      if (confirm('確定要移除品項?')) {
        this.$http.delete(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart/${id}`)
          .then((res) => {
            this.getCart()
            this.isLoadingItem = ''
          })
          .catch((error) => {
            console.dir(error)
          })
      }
    },
    updateCartItem (cartItem) {
      const postData = {
        data: {
          product_id: cartItem.id,
          qty: cartItem.qty
        }
      }
      this.isLoadingItem = cartItem.id

      this.$http.put(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart/${cartItem.id}`, postData)
        .then((res) => {
          this.isLoadingItem = ''
          this.getCart()
        })
        .catch((error) => {
          console.dir(error)
        })
    },
    removeAllCart () {
      if (confirm('確定要清空購物車?')) {
        this.$http.delete(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/carts`)
          .then((res) => {
            this.getCart()
          })
          .catch((error) => {
            console.dir(error)
          })
      }
    }
  },
  mounted () {
    this.getCart()
  }
}
</script>
