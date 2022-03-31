<template>
  <div class="container">
    <div class="row justify-content-center">
      <h1 class="h3 mb-3 font-weight-normal">請先登入</h1>
      <h1>登入後台</h1>
      <div class="col-8">
        <form id="form" class="form-signin">
          <div class="form-floating mb-3">
            <!-- 用v-model綁定data()中的user.username -->
            <input
              type="email"
              class="form-control"
              id="username"
              placeholder="name@example.com"
              required
              autofocus
              v-model="user.username"
            />
            <label for="username">帳號</label>
          </div>
          <div class="form-floating">
            <!-- 用v-model綁定data()中的user.password -->
            <input
              type="password"
              class="form-control"
              id="password"
              placeholder="Password"
              required
              v-model="user.password"
            />
            <label for="password">密碼</label>
          </div>
          <button
            @click="logIn()"
            class="btn btn-lg btn-primary w-100 mt-3"
            type="button"
          >
            登入
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
const url = 'https://vue3-course-api.hexschool.io/v2'
export default {
  data () {
    return {
      user: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    logIn () {
      console.log(this.user)
      console.log(`${url}/admin/signin`)

      this.$http
        .post(`${url}/admin/signin`, this.user)
        .then((result) => {
          console.log(result)
          // 取得token、expired
          console.log(result.data.token)
          const { token, expired } = result.data // 解構方式取得 token, expired
          document.cookie = `hexToken = ${token}; expires = ${new Date(
            expired
          )}`
          // 成功後，轉址
          window.location.href = '#/admin'
        })
        .catch((err) => {
          console.dir(err)
        })
    }
  },
  mounted () {}
}
</script>
