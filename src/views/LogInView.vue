<template>
<div class="page-wrapper">
        <div class="page-content--bge5">
            <div class="container">
                <div class="login-wrap">
                    <div class="login-content">
                        <div class="login-form">
                            <div>
                                <div class="form-group">
                                    <label>Username / Email Address</label>
                                    <input class="au-input au-input--full" type="text" v-model="username" placeholder="Username / Email">
                                </div>
                                <div class="form-group">
                                    <label>Password</label>
                                    <input class="au-input au-input--full" type="password" v-model="password" placeholder="Password">
                                </div>
                                <div class="login-checkbox">
                                    <label>
                                        <input type="checkbox" name="remember">Remember Me
                                    </label>
                                    <label>
                                        <a href="#">Forgotten Password?</a>
                                    </label>
                                </div>
                                <button class="au-btn au-btn--block au-btn--green m-b-20" @click="onClickLogIn">sign in</button>
                            </div>
                            <div class="register-link">
                                <p>
                                    Don't you have account?
                                    <a href="#">Sign Up Here</a>
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
  name: "LogIn",
  data() {
    return{
      username: '',
      password: '',
    }
  },
  methods: {
    async onClickLogIn() {
      await axios
        .get(`https://localhost:44369/api/gianhang/ghlogin/${this.username}/${this.password}`
        )
        .then((result) => {
            localStorage.setItem("GianHang", result)
          console.log(localStorage.getItem("GianHang"))
        })
        .catch((err) => {
          console.log(err);
        });
    },
  }
}
</script>