<template>
  <div class="container">
  <div class="col-md-12 row">
    <form>
            <label for="name">Name</label>
            <div>
                <input id="name" type="text" v-model="name" required autofocus>
            </div>

            <label for="email" >E-Mail Address</label>
            <div>
                <input id="email" type="email" v-model="email" required>
            </div>

            <label for="password">Password</label>
            <div>
                <input id="password" type="password" v-model="password" required>
            </div>

            <label for="password-confirm">Confirm Password</label>
            <div>
                <input id="password-confirm" type="password" v-model="password_confirmation" required>
            </div>
            <div>
                <button
                type="submit"
                @click="handleSubmit"
                class="btn btn-los">
                    Register
                </button>
            </div>
        </form>
  </div>
  </div>
</template>

<script>
export default {
  props: ['nextUrl'],
  data () {
    return {
      name: '',
      email: '',
      password: '',
      password_confirmation: '',
      is_admin: null
    }
  },
  methods: {
    handleSubmit (e) {
      e.preventDefault()
      const options = {
        headers: {
          'Content-Type': 'application/json'
        }
      }
      if (this.password === this.password_confirmation && this.password.length > 0) {
        let url = process.env.VUE_APP_REGISTER
        if (this.is_admin != null || this.is_admin === 1) url = 'http://localhost:3000/register-admin'
        this.$http.post(url, {
          name: this.name,
          email: this.email,
          password: this.password,
          is_admin: this.is_admin
        }, options)
          .then(response => {
            localStorage.setItem('user', JSON.stringify(response.data.user))
            localStorage.setItem('jwt', response.data.token)
            if (localStorage.getItem('jwt') != null) {
              this.$emit('loggedIn')
              if (this.$route.params.nextUrl != null) {
                this.$router.push(this.$route.params.nextUrl)
                window.location.reload()
              } else {
                this.$router.push('home')
                window.location.reload()
              }
            }
          })
          .catch(error => {
            console.log(error)
            alert('Unable to register')
          })
      } else {
        this.password = ''
        this.passwordConfirm = ''
        return alert('Passwords do not match')
      }
    }
  }
}
</script>
