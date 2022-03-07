<template>
  <main  class="wrapper__bgImg">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-md-4">
          <div class="login__wrapper mx-auto">
            <h2 class="text-light fw-bold mb-3">登入</h2>
            <div class="mb-3">
              <label for="username" class="form-label text-light">Email </label>
              <input
                v-model="loginData.username"
                type="email"
                class="form-control"
                id="username"
                placeholder="請輸入信箱"
              />
            </div>
            <div class="mb-3">
              <label for="password" class="form-label text-light">密碼</label>
              <input
                v-model="loginData.password"
                type="password"
                class="form-control"
                id="password"
                placeholder="請輸入密碼"
              />
            </div>
            <div class="mt-5">
              <button @click="login" class="btn btn-danger w-100" type="button">
                登入
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data () {
    return {
      loginData: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      const { username, password } = this.loginData

      const postData = {
        username,
        password
      }

      this.$http.post(`${process.env.VUE_APP_API}/admin/signin`, postData)
        .then((res) => {
          const { token, expired } = res.data

          // 將 token 存至 cookie
          document.cookie = `aliciaToken=${token}; expires=${new Date(expired)}`

          this.$router.push('/admin/products')
        })
        .catch((error) => {
          alert(`${error.data.message}:${error.data.error.message}`)
        })
    }

  },
  mounted () {

  }
}
</script>
